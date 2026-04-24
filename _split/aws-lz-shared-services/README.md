# aws-lz-shared-services

Workload-tier infrastructure that lives **inside** workload accounts after the
landing zone is established. Owned by the platform-services team, not cloud-ops.

## Examples of what lives here

- Shared ECR repositories
- Cross-account IAM roles for CI/CD
- Centralized observability (Grafana, Prometheus stacks)
- Shared RDS clusters / Redis caches per env
- Internal certificate authorities
- Service-discovery infra (Cloud Map, internal Route53 zones)

## Why a separate repo?

- **Cadence**: changes here are weekly; LZ-platform changes are monthly
- **Blast radius**: a misconfigured shared cache shouldn't block account onboarding
- **Ownership**: a different team owns these resources

## Layout (skeleton)

```
stacks/
  ecr/
  observability/
  shared-rds/
  service-discovery/
config/
  prod.yaml  staging.yaml  dev.yaml
.github/workflows/
  shared-services-ci.yml
CODEOWNERS
```

Consumes the same `aws-tf-modules` versioned modules as `aws-lz-platform`.
Same module-source pattern:

```hcl
module "ecr" {
  source = "git::ssh://git@github.com/<org>/aws-tf-modules.git//modules/ecr-repo?ref=ecr-repo/v1.0.0"
}
```

> **Defer building this repo until you actually need shared services.**
> If everything fits comfortably in `aws-lz-platform`, leave this empty.
