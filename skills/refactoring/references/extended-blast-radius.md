# Extended Blast Radius: Non-Code Artifacts

Beyond source code, symbols often appear in documentation, infrastructure, and tooling. Always check these.

## Documentation & Knowledge Artifacts

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **README/Docs** | `*.md`, `*.mdx`, `*.rst`, `*.adoc` | Usage examples, API docs, architecture decisions |
| **API Documentation** | `*openapi*`, `*swagger*`, `*api-doc*` | Endpoint names, schema definitions, examples |
| **ADRs** | `adr-*.md`, `docs/decisions/*` | Architecture Decision Records referencing the symbol |
| **Changelogs** | `CHANGELOG*`, `HISTORY*`, `RELEASE*` | Historical references (update if appropriate) |
| **Code comments** | Inline `//`, `/* */`, `#`, `"""` | Explanatory comments mentioning the symbol |

## UI Component Libraries & Storybook

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Storybook stories** | `*.stories.tsx`, `*.stories.ts`, `*.stories.js`, `*.story.*` | Component stories, argTypes, decorators |
| **Storybook docs** | `*.stories.mdx`, `*.docs.mdx` | MDX documentation pages |
| **Design tokens** | `tokens.*`, `theme.*`, `*.tokens.*` | Named design system values |
| **Component docs** | `*.doc.tsx`, `*README.md` in component dirs | Per-component documentation |

## GraphQL Schemas & Operations

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Schema definitions** | `*.graphql`, `*.gql`, `schema.*` | Types, fields, enums, directives |
| **Operations** | `*.graphql`, `*queries*`, `*mutations*` | Query/mutation/subscription names, variables |
| **Fragments** | `*fragment*`, `*.graphql` | Reusable fragment definitions |
| **Generated types** | `*generated*`, `*__generated__*` | Auto-generated types (regenerate!) |
| **Resolvers** | `*resolver*`, `*Resolver*` | Backend resolver implementations |
| **Client config** | `apollo.config.*`, `codegen.*` | Client configuration referencing schemas |

## Infrastructure as Code (IaC)

### Terraform

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Resources** | `*.tf` | `resource`, `data`, `module` names |
| **Variables** | `variables.tf`, `*.tfvars` | Variable names and defaults |
| **Outputs** | `outputs.tf` | Output value names |
| **Modules** | `modules/*/` | Module source references |
| **State** | `*.tfstate` | DO NOT manually edit — will drift |
| **Backend config** | `backend.tf`, `*.hcl` | State storage references |

### AWS CloudFormation / SAM

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Templates** | `*.template`, `template.yaml`, `template.json` | Logical resource names, parameters |
| **SAM templates** | `sam*.yaml`, `template.yaml` | Lambda function names, API definitions |
| **Nested stacks** | `*-stack.*` | Cross-stack references |
| **Parameters** | `*parameters*`, `*config*` | Parameter store references |

### AWS CDK

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Stack definitions** | `*-stack.ts`, `*Stack.ts`, `*-stack.py` | Construct IDs, resource names |
| **Constructs** | `*-construct.*`, `constructs/*` | Reusable construct definitions |
| **App entry** | `bin/*.ts`, `app.py` | Stack instantiation |
| **Config** | `cdk.json`, `cdk.context.json` | Context values, feature flags |

### Pulumi

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Programs** | `index.ts`, `__main__.py`, `main.go` | Resource names, exports |
| **Config** | `Pulumi.*.yaml` | Stack configuration values |
| **State** | Managed by Pulumi service | DO NOT manually edit |

### Kubernetes / Helm

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Manifests** | `*.yaml`, `*.yml` in `k8s/`, `kubernetes/`, `manifests/` | Resource names, labels, selectors |
| **Helm charts** | `Chart.yaml`, `values.yaml`, `templates/*` | Chart names, value references |
| **Kustomize** | `kustomization.yaml`, `patches/*` | Overlays, patches |
| **ConfigMaps/Secrets** | `*configmap*`, `*secret*` | Key names |

### Ansible

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Playbooks** | `*.yml`, `*.yaml` in `playbooks/` | Task names, variable references |
| **Roles** | `roles/*/` | Role names, defaults, vars |
| **Inventory** | `inventory*`, `hosts*` | Host/group names |
| **Variables** | `group_vars/*`, `host_vars/*` | Variable definitions |

## CI/CD Pipelines

| Platform | File Patterns | What to Look For |
|----------|---------------|------------------|
| **GitHub Actions** | `.github/workflows/*.yml` | Job names, step names, env vars, secrets |
| **GitLab CI** | `.gitlab-ci.yml`, `gitlab-ci/*.yml` | Job names, variables, includes |
| **Jenkins** | `Jenkinsfile`, `*.groovy` | Stage names, parameters, env vars |
| **Azure Pipelines** | `azure-pipelines.yml`, `.azure-pipelines/*` | Stage/job names, variables |
| **CircleCI** | `.circleci/config.yml` | Job names, orbs, parameters |
| **ArgoCD** | `argocd/*`, `applications/*` | Application names, sync policies |

## Environment & Configuration

| Artifact Type | File Patterns | What to Look For |
|--------------|---------------|------------------|
| **Env files** | `.env*`, `*.env` | Environment variable names |
| **Docker** | `Dockerfile*`, `docker-compose*.yml` | Image names, env vars, labels |
| **Package manifests** | `package.json`, `pom.xml`, `build.gradle`, `Cargo.toml` | Dependency names, script names |
| **Editor config** | `.vscode/*`, `.idea/*`, `*.code-workspace` | Launch configs, task names |
