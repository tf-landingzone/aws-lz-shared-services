# aws-lz-shared-services

**Workload-tier shared infrastructure** — resources that live inside workload accounts after the landing zone is established. Owned by the platform-services team.

---

## What belongs here (vs aws-lz-platform)

| Question | aws-lz-platform | aws-lz-shared-services |
|---|---|---|
| Who owns it? | Cloud-ops / LZ team | Platform-services team |
| Change cadence | Monthly (LZ releases) | Weekly (service updates) |
| What fails if broken? | All account onboarding | Individual shared service |
| Examples | GuardDuty, Config, OIDC, TGW | ECR, Grafana, shared RDS |

---

## Planned stacks

```
stacks/
├── ecr/                 ← Shared ECR repositories (cross-account pull access)
├── observability/       ← Grafana + Prometheus (central metrics)
├── shared-rds/          ← Shared RDS clusters per environment
└── service-discovery/   ← Cloud Map + internal Route53 hosted zones
config/
├── prod.yaml
├── staging.yaml
└── dev.yaml
```

> This repo is a **skeleton** — stacks are added as the platform matures post-LZ bootstrap.

---

## AWS resources (planned)

| Stack | AWS Resources |
|---|---|
| `ecr` | ECR repositories, resource-based policies for cross-account pull |
| `observability` | ECS services (Grafana/Prometheus), ALB, Route53 records, ACM cert |
| `shared-rds` | RDS Aurora cluster, Secrets Manager credentials, subnet groups |
| `service-discovery` | Route53 private hosted zones, Cloud Map namespace |

---

## How to add a new shared service

1. Create `stacks/<service>/` with `main.tf`, `variables.tf`, `outputs.tf`
2. Add config values to `config/prod.yaml` and `config/staging.yaml`
3. Add a GitHub Actions workflow under `.github/workflows/`
4. The workflow should follow the same OIDC pattern as `aws-lz-platform` — no static AWS keys
