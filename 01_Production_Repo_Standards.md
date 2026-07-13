# Production Repo Standards

Added 11 July 2026. Applies to every repo in the execution pack. This file answers two questions: what files a production-grade repo contains beyond the code, and why each exists. Claude Code should scaffold these in Sprint 1 of every project; the CLAUDE.md files in this pack are to be read as if this list were appended to them.

## 1. Working environment (VS Code + WSL2)

- All repos live in the WSL2 Linux filesystem under `~/projects/`, one folder per repo, never nested, never under `/mnt/c` (10 to 20 times slower I/O, broken file watching, permission noise).
- Open repos with `code .` from inside WSL so VS Code runs in Remote - WSL mode; the terminal, git, terraform and claude all run on the Linux side.
- Tooling installed in WSL once: git, gh, claude, terraform, aws cli v2, kubectl, helm, ansible, node, python3, k6. Versions pinned per repo (see section 3).
- This pack remains on the Windows side as the reference. Starting a project means: `gh repo create`, clone into `~/projects/`, copy in that project's `PRD.md` and `CLAUDE.md`, open in VS Code, start Claude Code.

```
~/projects/
├── johnnybabs/            profile repo (README only)
├── johnbabalola.com/      Project A
├── vidcast/               Project B (existing repo, cloned)
├── portal-ansible/        Project C
├── vidcast-aiops/         Project D
└── prodops-evidence/      Project E
```

## 2. The standard file set, with reasons

### Root of every repo

| File | Why it exists |
|---|---|
| `README.md` | The 90-second pitch: what, architecture diagram, how to run, cost table, teardown. Recruiters read only this. |
| `CLAUDE.md` | Operating instructions and guardrails for AI-assisted sessions; keeps every session consistent with project rules. |
| `PRD.md` | Scope, sprints, definitions of done. Prevents scope drift; shows requirements thinking. |
| `LICENSE` | MIT. An unlicensed public repo is legally "all rights reserved" and looks unfinished. |
| `.gitignore` | tfstate, tfvars, .env, dist, venv, .terraform. The absence of leaked state files is checked by anyone competent who opens the repo. |
| `.editorconfig` | Consistent indentation and line endings across WSL and Windows editors; kills CRLF noise in diffs. |
| `Makefile` | The uniform interface: `make init plan apply deploy destroy test lint`. Anyone (including future you) operates the repo without reading the CI YAML. Interviews notice this. |
| `.pre-commit-config.yaml` | Hooks that run before any commit lands: `terraform fmt`, `terraform validate`, `tflint`, `gitleaks`, trailing whitespace, YAML lint. Reason: secrets and broken IaC are cheapest to catch at commit time, and it demonstrates shift-left security thinking beyond CI. |
| `.tool-versions` (asdf/mise) or `.terraform-version` | Pins terraform, kubectl, helm versions. Reproducibility: the repo behaves the same on any machine, which is the whole argument of IaC applied to the toolchain itself. |
| `teardown.sh` | Costed exit path, already required by the pack. Website repo's version requires `--force`. |
| `SECURITY.md` | One paragraph: how to report an issue, what is intentionally out of scope. Signals security is a considered dimension, not an accident. |
| `CHANGELOG.md` | Keep-a-Changelog format, updated per sprint. Cheap, and it narrates progress for anyone reading the repo history. |

### `.github/` directory

| File | Why it exists |
|---|---|
| `workflows/*.yml` | CI/CD. Minimum per repo: lint/validate on PR, plan on PR with the plan output as a PR comment, apply/deploy on main via OIDC. Separation of plan (read) and apply (write) roles is the production pattern. |
| `PULL_REQUEST_TEMPLATE.md` | Forces the self-review checklist the pack already mandates: what changed, why, evidence (plan output or screenshot), rollback note. Makes the solo PR discipline visible and honest. |
| `dependabot.yml` | Weekly bumps for GitHub Actions and Terraform providers. An action pinned to a two-year-old tag is a real supply-chain finding; automated patching is a production behaviour that costs nothing. |
| `CODEOWNERS` | Trivial when solo (`* @johnnybabs`) but it wires review-required branch protection correctly and shows the convention is known. |
| `ISSUE_TEMPLATE/` (optional) | Only worth it on vidcast, where sprint tasks can be tracked as issues to show planning in public. |

### `docs/` directory

| File | Why it exists |
|---|---|
| `docs/adr/NNNN-title.md` | Architecture Decision Records. THE answer to "considerations and reasons for technical decisions". One numbered file per significant decision, written when the decision is made, never rewritten (superseded instead). This is the strongest mid-level signal in the entire file set: it proves decisions were choices between alternatives, not defaults. |
| `docs/metrics.md` | Measured numbers only (deploy time, RTO, error rates). Source of CV bullets; the no-invented-numbers rule enforced by structure. |
| `docs/decisions.md` | Already in the pack; small day-to-day calls that do not merit a full ADR. Promote to an ADR when a call has real alternatives and consequences. |
| `docs/runbooks/` | Operational procedures (Project E produces the definitive set; other repos hold at least "how to bootstrap from cold" and "how to tear down safely"). |
| `docs/screenshots/` | The evidence trail, since infrastructure gets torn down. |

### ADR template (use exactly this, keep each under a page)

```markdown
# ADR-0001: Use Origin Access Control instead of public S3 website hosting

Date: 2026-07-XX
Status: Accepted

## Context
What situation forced a decision, and what constraints applied (cost cap,
solo operation, teardown model, keyword value).

## Options considered
1. Public S3 website endpoint: simplest, but no TLS at the bucket, no
   private bucket, weaker security story.
2. CloudFront + OAC (chosen).
3. CloudFront + legacy OAI: superseded by OAC, AWS recommends against.

## Decision
Option 2, because the bucket stays private, TLS terminates at the edge,
and OAC is the current AWS-recommended pattern.

## Consequences
Positive: private bucket, HSTS possible, matches production practice.
Negative: invalidations needed on deploy; slightly more Terraform.
```

First ADRs each project should write: A: OAC vs public bucket; wildcard cert vs per-subdomain; Astro vs plain HTML. B: Helm vs Kustomize retirement; CI PR tag-bump vs ArgoCD Image Updater; auto-sync dev vs manual prod. C: separate repo vs directory; SSM connection vs SSH keys; Ansible-vs-Terraform boundary. D: read-only action groups (no auto-remediation); templated queries vs free-form; region/cross-region inference. E: RTO/RPO targets chosen and why; which Security Hub standards and why only those.

### Terraform-specific

| Item | Why |
|---|---|
| `versions.tf` with `required_version` and pinned provider versions | An unpinned provider upgrade breaking prod is a classic incident; pinning is table stakes. |
| `variables.tf` with descriptions and validation blocks | Self-documenting inputs; validation catches bad values at plan time. |
| `outputs.tf` with descriptions | The contract other tooling (Ansible dynamic inventory, CI) consumes. |
| Directory-per-environment (`envs/dev`, `envs/prod`) over workspaces | Explicit, diffable, matches how most UK employers structure it; record as an ADR. |
| `.tflint.hcl`, `.checkov.yaml` | Configured linting shows the tools are used deliberately (rule exceptions documented), not just installed. |

### Testing, in proportion

Do not build a test pyramid for a portfolio repo; do show the categories exist: `terraform validate` + tflint + Checkov as static tests in CI; one smoke test per deploy (curl the endpoint, check the trace appears, run one Logs Insights query) scripted in `tests/smoke.sh` and called by CI post-deploy; the k6 load script (Projects B, D, E) as the performance check; ansible-lint plus the idempotency second-run as Project C's test story. If asked in interview about Terratest or kitchen-terraform: know what they are, say the smoke-test trade-off was a scope decision, and that is an ADR too.

## 3. Order of scaffolding (Claude Code, Sprint 1, every repo)

1. `git init` via `gh repo create`, default branch `main`, branch protection: require PR, no force-push.
2. LICENSE, .gitignore, .editorconfig, README stub, CLAUDE.md, PRD.md.
3. `.tool-versions`, pre-commit install, Makefile skeleton.
4. `.github/`: PR template, dependabot, CODEOWNERS, lint workflow (green on the scaffold commit).
5. `docs/adr/0001` for the first real architecture decision, then build.

The scaffold itself is one PR titled "chore: production repo baseline" and its self-review checklist is the first demonstration of the discipline.
