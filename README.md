# PostgreSQL service for Kubernetes on Wodby

Run PostgreSQL as a database for Kubernetes applications managed by Wodby.

This repository defines the Wodby service manifests and operational
configuration for PostgreSQL.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Wodby stacks using this service

- [Dagster application stack](https://github.com/wodby/stack-dagster)
- [Django application stack](https://github.com/wodby/stack-django)
- [Drupal application stack](https://github.com/wodby/stack-drupal)
- [FastAPI application stack](https://github.com/wodby/stack-fastapi)
- [Flask application stack](https://github.com/wodby/stack-flask)
- [Go application stack](https://github.com/wodby/stack-go)
- [Next.js application stack](https://github.com/wodby/stack-nextjs)
- [Node.js application stack](https://github.com/wodby/stack-node)
- [PHP application stack](https://github.com/wodby/stack-php)
- [PostgreSQL application stack](https://github.com/wodby/stack-postgres)
- [Python application stack](https://github.com/wodby/stack-python)
- [Rails application stack](https://github.com/wodby/stack-rails)
- [Ruby application stack](https://github.com/wodby/stack-ruby)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `postgres` |
| Type | Database |
| Versions | `18` by default |
| Workloads | `main` (StatefulSet), primary; fixed replica count |
| Containers | `postgresql` using `wodby/postgres` |
| Endpoints | `postgres`: TCP 5432 |
| Volumes | Data, 10 GB |
| Helm | chart `oci://registry-1.docker.io/wodby/postgres`; version `0.2.1` |
| Configuration | 1 configuration files, 3 generated or fixed tokens |
| Operations | 1 import workflows, 1 backup workflows |

## Use this service

Use this service through one of the Wodby application stacks listed above, or
reference `postgres` from a custom Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).
