# Implementation Document 002 — Infrastructure Bootstrap

| Field | Value |
|-------|-------|
| Document ID | IMP-002 |
| Name | Infrastructure Bootstrap |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the local and production-ready infrastructure required to run Nebula ERP.

The objective is to provide a reproducible, containerized development environment that mirrors production as closely as possible while remaining simple for developers to bootstrap.

---

# 2. Objectives

The infrastructure must:

- Be fully containerized
- Require minimal manual setup
- Support local development
- Support production deployment
- Enable horizontal scaling
- Provide health monitoring
- Support backups
- Support disaster recovery
- Isolate services
- Minimize downtime

---

# 3. Infrastructure Overview

Nebula ERP consists of the following core services:

```text
                    Internet
                        │
                        ▼
                 Reverse Proxy
                   (Nginx)
                        │
      ┌─────────────────┼─────────────────┐
      ▼                 ▼                 ▼
 Frontend API       Worker Queue      Monitoring
                        │
                        ▼
                     Redis
                        │
                        ▼
                  PostgreSQL
                        │
                        ▼
                    Object Storage
                       (MinIO)
```

Each service runs independently inside its own container.

---

# 4. Core Infrastructure Components

| Component | Technology | Purpose |
|-----------|------------|---------|
| Reverse Proxy | Nginx | Routing & SSL |
| Frontend | React + Vite | Web application |
| Backend API | NestJS | Business logic |
| Worker | Node.js | Background processing |
| Database | PostgreSQL | Persistent data |
| Cache | Redis | Cache & queues |
| Object Storage | MinIO | Files & attachments |
| Mail Testing | Mailpit | Local SMTP |
| Monitoring | Future Phase | Metrics & alerts |

---

# 5. Directory Structure

```text
infrastructure/

├── docker/
│   ├── compose.yml
│   ├── compose.prod.yml
│   ├── compose.dev.yml
│   └── env/
│
├── nginx/
│   ├── nginx.conf
│   ├── default.conf
│   └── ssl/
│
├── postgres/
│   ├── init/
│   ├── backups/
│   └── migrations/
│
├── redis/
│   └── redis.conf
│
├── minio/
│   └── policies/
│
├── scripts/
│   ├── start.sh
│   ├── stop.sh
│   ├── reset.sh
│   └── backup.sh
│
└── monitoring/
```

The structure separates infrastructure configuration from application code.

---

# 6. Docker Compose Architecture

The development stack is orchestrated using Docker Compose.

Primary services:

```text
nginx

↓

web

↓

api

↓

worker

↓

postgres

↓

redis

↓

minio

↓

mailpit
```

All services communicate through an isolated Docker network.

---

# 7. Network Design

Create a dedicated bridge network:

```text
nebula-network
```

Characteristics:

- Internal DNS resolution
- Service-to-service communication
- No unnecessary exposed ports
- External access only through Nginx

Example communication:

```text
web
↓

api:3000

↓

postgres:5432

↓

redis:6379

↓

minio:9000
```

No container should communicate through localhost.

---

# 8. Volume Management

Persistent data must survive container recreation.

Named Docker volumes:

| Volume | Purpose |
|---------|---------|
| postgres-data | Database storage |
| redis-data | Redis persistence |
| minio-data | Object storage |
| mailpit-data | Local email data |
| logs | Shared service logs |

Temporary build artifacts should never be stored in persistent volumes.

---

# 9. Container Standards

Every container should include:

- Health checks
- Restart policy
- Named container
- Resource limits
- Environment validation
- Readiness checks
- Structured logging

Example restart policy:

```yaml
restart: unless-stopped
```

Containers should fail fast if required dependencies are unavailable.

---

# 10. Environment Separation

Separate configuration files should exist for:

```text
Development

↓

Testing

↓

Staging

↓

Production
```

Each environment may override:

- Ports
- Database credentials
- API endpoints
- Logging level
- Feature flags
- External integrations

Application code must remain environment-agnostic.

---

# 11. Health Checks

Every service must expose a health endpoint or equivalent status check.

Recommended checks:

| Service | Health Check |
|----------|--------------|
| web | HTTP response |
| api | `/health` endpoint |
| worker | Queue heartbeat |
| postgres | Connection test |
| redis | `PING` |
| minio | API readiness |
| nginx | HTTP status |

Docker Compose should wait for critical dependencies before starting dependent services.

---

# 12. PostgreSQL Configuration

PostgreSQL is the primary transactional database for Nebula ERP.

Recommended version:

```text
PostgreSQL 16+
```

Configuration principles:

- UTF-8 encoding
- UTC timezone
- Daily automated backups
- Connection pooling
- WAL enabled
- SSL support for production
- Persistent storage

Recommended databases:

| Database | Purpose |
|----------|---------|
| nebula | Primary application database |
| nebula_shadow | Prisma migration shadow database |

Key configuration values:

```text
max_connections = 200
shared_buffers = 512MB
effective_cache_size = 2GB
wal_level = replica
timezone = UTC
```

Development values may be reduced to match available system resources.

---

# 13. Redis Configuration

Redis provides caching, session storage, and asynchronous queue support.

Recommended version:

```text
Redis 7+
```

Responsibilities:

- BullMQ queues
- Session storage
- Rate limiting
- Distributed locks
- Temporary cache

Configuration guidelines:

```text
appendonly yes
save 60 1000
maxmemory-policy allkeys-lru
```

Production recommendations:

- Password protection
- TLS (where supported)
- Persistence enabled
- Regular snapshot verification

Redis should never be treated as the primary source of persistent business data.

---

# 14. MinIO Configuration

MinIO provides S3-compatible object storage.

Responsibilities:

- Product images
- User avatars
- Documents
- Reports
- Attachments
- AI-generated files
- Backup archives

Suggested buckets:

| Bucket | Purpose |
|---------|----------|
| uploads | General uploads |
| documents | Business documents |
| media | Images and videos |
| reports | Generated reports |
| backups | Backup archives |

Security guidelines:

- Private buckets by default
- Pre-signed URLs for downloads
- Role-based access
- Server-side encryption (production)

---

# 15. Mailpit Configuration

Mailpit is used only for local development and testing.

Responsibilities:

- Capture outgoing emails
- Preview HTML emails
- Verify templates
- Debug email workflows

Development endpoints:

```text
SMTP Port: 1025

Web UI: 8025
```

Mailpit must not be deployed to production.

Production email providers will be configured separately.

---

# 16. Nginx Reverse Proxy

Nginx serves as the single entry point into the platform.

Responsibilities:

- Reverse proxy
- SSL termination
- HTTP to HTTPS redirection
- Static asset serving
- Compression
- Security headers
- Request routing

Routing example:

```text
/

↓

Frontend

/api

↓

Backend API

/storage

↓

MinIO

/health

↓

Health endpoint
```

Recommended production features:

- HTTP/2
- Gzip or Brotli compression
- HSTS
- Rate limiting
- Request size limits
- WebSocket support

---

# 17. Logging Strategy

Every service must emit structured logs.

Minimum log fields:

- Timestamp
- Service name
- Environment
- Log level
- Request ID
- User ID (when available)
- Message

Log levels:

```text
TRACE

DEBUG

INFO

WARN

ERROR

FATAL
```

Development:

- Human-readable console logs

Production:

- Structured JSON logs
- Centralized log aggregation
- Log retention policy

Sensitive information (passwords, tokens, secrets, personal data) must never be written to logs.

---

# 18. Backup & Restore Strategy

Critical services requiring backups:

| Service | Backup Method |
|----------|---------------|
| PostgreSQL | Logical and scheduled backups |
| MinIO | Object replication or archive |
| Configuration | Version control |
| Environment files | Secure encrypted storage |

Recommended schedule:

- Daily incremental backups
- Weekly full backups
- Monthly archive retention

Restore procedures should be tested periodically to ensure backup integrity.

---

# 19. Infrastructure Security

Infrastructure hardening requirements:

- Non-root containers whenever possible
- Read-only filesystems where applicable
- Minimal container images
- Secret injection via environment or secret manager
- Internal service isolation
- Firewall restrictions
- HTTPS for all external traffic
- Regular dependency updates

Security scans should be integrated into the CI/CD pipeline before production deployments.

---

# 20. Monitoring & Observability

Infrastructure must be observable from day one.

Monitoring objectives:

- Service availability
- Resource utilization
- Performance metrics
- Error tracking
- Queue health
- Database health
- Storage utilization
- Network latency

Recommended monitoring stack:

| Component | Technology |
|-----------|------------|
| Metrics | Prometheus |
| Dashboards | Grafana |
| Log Aggregation | Loki |
| Container Metrics | cAdvisor |
| Alerting | Alertmanager |

Core metrics:

- CPU utilization
- Memory usage
- Disk usage
- Network throughput
- API response time
- Database connections
- Redis memory usage
- Queue depth
- Worker throughput

Alerts should notify operators before service degradation impacts users.

---

# 21. Scaling Strategy

The infrastructure should support horizontal scaling where practical.

Scalable services:

- Frontend
- Backend API
- Worker

Stateful services:

- PostgreSQL
- Redis
- MinIO

Scaling principles:

```text
Load Balancer

↓

Multiple API Instances

↓

Shared Redis

↓

Shared PostgreSQL

↓

Shared Object Storage
```

Scaling considerations:

- Stateless application design
- Externalized session storage
- Queue-based background processing
- Database connection pooling
- Independent worker scaling

Infrastructure should support future orchestration with Kubernetes without major architectural changes.

---

# 22. Disaster Recovery

The infrastructure must support recovery from service failures and data loss.

Recovery objectives:

| Service | Recovery Target |
|----------|-----------------|
| API | < 15 minutes |
| Worker | < 15 minutes |
| PostgreSQL | < 1 hour |
| Redis | < 30 minutes |
| MinIO | < 1 hour |

Recovery procedures:

1. Restore infrastructure.
2. Restore database.
3. Restore object storage.
4. Verify service health.
5. Validate application functionality.
6. Resume normal operations.

Disaster recovery procedures should be documented and tested on a scheduled basis.

---

# 23. Infrastructure Validation Checklist

Infrastructure is considered operational when the following are verified:

## Containers

- All containers start successfully.
- Health checks report healthy.
- Restart policies function correctly.

## Networking

- Services communicate over the internal network.
- External traffic is routed through Nginx.
- No unnecessary ports are exposed.

## Storage

- Persistent volumes are mounted.
- Database data persists after restart.
- Object storage persists after restart.

## Database

- PostgreSQL accepts connections.
- Migrations execute successfully.
- Seed scripts complete without errors.

## Cache

- Redis responds to health checks.
- Queue processing is operational.

## Object Storage

- MinIO buckets are created.
- File uploads and downloads succeed.

## Email

- Mailpit captures outgoing messages.
- Web interface is accessible.

## Security

- Environment variables load correctly.
- Secrets are not exposed.
- HTTPS configuration is validated (production).

---

# 24. Acceptance Criteria

The Infrastructure Bootstrap implementation is complete when:

- Docker Compose environment is operational.
- PostgreSQL is configured and persistent.
- Redis is configured and persistent.
- MinIO is operational.
- Mailpit is operational for local development.
- Nginx routes traffic correctly.
- Health checks are implemented.
- Logging strategy is documented.
- Backup strategy is defined.
- Disaster recovery process is documented.
- Monitoring strategy is established.
- Infrastructure validation checklist passes.

---

# 25. AI Context Summary

## Summary

The Infrastructure Bootstrap defines the operational platform that hosts Nebula ERP. It standardizes container orchestration, networking, persistent storage, service configuration, observability, backups, disaster recovery, and infrastructure security.

## Dependencies

- IMP-001 — Monorepo Foundation
- DOC-006 — Infrastructure
- DOC-007 — Security

## Referenced By

- Database implementation
- Backend services
- Frontend deployment
- CI/CD pipelines
- Production operations

---

# 26. Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|---------------------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Infrastructure Bootstrap implementation specification |