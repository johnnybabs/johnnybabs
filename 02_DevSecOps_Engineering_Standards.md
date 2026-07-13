# DevSecOps Engineering Standards

Added 11 July 2026. Adapted from John's "Senior Cloud, DevOps & DevSecOps Engineer" requirements prompt (original preserved verbatim at `reference/DevSecOps_Prompt_Original.md`). This file is copied into every project repo alongside `PRD.md`, `CLAUDE.md` and `01_Production_Repo_Standards.md`, and every project CLAUDE.md binds to it. Claude Code: read this before Sprint 1 of any project and treat Tier 1 as blocking.

## How the standard applies to a solo portfolio lab

The original prompt describes an enterprise with a security team, a compliance officer and a platform organisation. This lab is one engineer, a £50 monthly cap and a teardown model. The standard is therefore applied in three tiers. Nothing is silently dropped; anything not implemented is either demonstrated at reduced scale or explicitly excepted with a reason, because "documented, approved exception" is itself the mechanism the original prompt mandates.

- Tier 1, enforced: implemented in full in every repo. Violations block the sprint DoD.
- Tier 2, demonstrated: implemented at lab scale, faithfully enough that the pattern is real and the interview story is honest.
- Tier 3, documented design: not implemented (cost, headcount or scope); instead an ADR or `docs/enterprise-scale.md` entry states what production would require and why it is out of scope here. These entries are deliverables, not apologies.

Every repo carries `docs/security-exceptions.md`: finding or requirement, reason for deferral, owner (John), expiry or review date. An exception without an expiry date is a Tier 1 violation.

## The four questions (Tier 1)

Before Sprint 1 of every project, answer in `docs/threat-model.md`, in writing:

1. What breaks this?
2. What does it cost?
3. Who fixes it when it fails, and do they have everything they need?
4. Who attacks this, how, and what do they gain?

The five design questions (business problem, simplest solution, reuse over build, 10x/100x, attacker blast radius) are answered per significant decision inside ADRs, using the trade-off matrix below.

## Threat model (Tier 1)

Every repo has `docs/threat-model.md` completed before the first deployment to AWS, reviewed whenever the architecture changes, structured as:

- Trust boundaries, data flows, entry points, assets (a table each; Mermaid diagram welcome but the tables are the document).
- STRIDE table: each threat, one named mitigation, how the mitigation is verified, or an entry in the exceptions register.
- Residual risk list, signed off by John dated.

Lab-appropriate scale: the VidCast threat model will be 2 to 3 pages; the website's about 1. A one-page honest threat model beats a 20-page template dump.

## Trade-off matrices (Tier 1)

Every ADR for a decision with cost, operational or security impact evaluates at least three alternatives against: monthly cost, implementation time, operational complexity, scalability ceiling, security posture, attack surface introduced, existing expertise, compliance fit. The `01_Production_Repo_Standards.md` ADR template gains this matrix; if the matrix does not exist, the decision is not final.

## Security requirements

Tier 1, enforced everywhere:

- No credentials in code, config, tfvars, CI variables or logs. Runtime retrieval via IAM roles (IRSA on EKS, OIDC in CI, instance profiles on EC2). Secrets in Secrets Manager or SSM Parameter Store (SecureString), each with a named owner and rotation note.
- Least privilege IAM: specific actions on specific resources. No `*FullAccess` managed policies in any repo, lab or not; the effective-permissions diff is included in any PR that touches IAM.
- Identity-based network rules: security group sources are other security groups wherever the source is a known AWS resource (the portal's 5 chained SGs already do this; keep that bar).
- Human approval before production-affecting changes: ArgoCD manual sync on prod, `workflow_dispatch` gates, typed confirmation for destroys.
- Auditability: every deploy answers who, what commit, what changed, what outcome, from GitHub Actions logs plus CloudTrail. CloudTrail stays enabled in the account at all times, including through teardowns.
- SSH disabled as the management path on EC2: SSM Session Manager with session logging (Project C already specifies the SSM connection plugin; this makes it a rule, not a preference).
- S3 account-level Block Public Access on; the website bucket stays private behind OAC.
- Encryption at rest on by default everywhere (S3, EBS, RDS, DynamoDB, CloudWatch Logs). AWS-managed keys are the lab baseline, exactly as the original prompt permits; one demonstration of a customer-managed KMS key with annual rotation and CloudTrail key-usage logging is done in Project C (the Secrets Manager secret) so the CMK pattern is real. TLS 1.2 minimum, ACM-automated certificates only.

Tier 2, demonstrated:

- Zero trust: every service-to-service call authenticated. Lab demonstration: IRSA per service (no shared node role for app pods), RabbitMQ per-service credentials, the webhook Lambda validating a shared-secret header. mTLS/service mesh is Tier 3, recorded in `docs/enterprise-scale.md` with the Istio/App Mesh sentence you would say in an interview.
- Security observability: alerts on failed authentication patterns, IAM policy changes and root usage (CloudTrail metric filters plus SNS, cheap and permanent); GuardDuty findings routed to email while enabled (Project E); infrastructure drift detected by ArgoCD self-heal (VidCast) and flagged `terraform plan` diffs elsewhere.

Tier 3, documented design: organisation-wide SCPs, centralised immutable log account, security tooling account separation, Macie, WAF. One entry each in `docs/enterprise-scale.md`, with the multi-account landing-zone story pointing at the deferred Project F.

## Shift-left pipeline gates (Tier 1 where marked)

| Check | Tool | Gate | Tier |
|---|---|---|---|
| Secret scanning | Gitleaks (pre-commit and CI) | Block, zero tolerance | 1 |
| IaC scanning | Checkov + tflint (pre-commit and CI) | Block on critical | 1 |
| Container scanning | Trivy | Block on CRITICAL/HIGH (existing VidCast gate, keep) | 1 |
| SAST | Bandit (Python) or Semgrep | Block on high/critical | 1 (add to VidCast and Lambda repos) |
| SCA | Dependabot alerts + pip-audit/npm audit in CI | Block on critical, warn on high | 1 |
| DAST | OWASP ZAP baseline scan against the weekend staging deploy | Findings reviewed, logged in docs | 2 (VidCast and website only) |
| Licence scanning | none | Exception: solo MIT-licensed lab, no proprietary codebase | 3 (exceptions register) |

Findings tracking: criticals fixed before the sprint DoD; highs within the project; anything deferred goes to `docs/security-exceptions.md` with an expiry. Lab SLA table: critical before next deploy, high 7 days, medium 30 days, low noted.

## Supply chain security

Tier 1: every GitHub Action pinned to a full commit SHA, never a tag; dependencies pinned exactly (`requirements.txt` with `==` and hashes where practical, lockfiles committed); Dependabot updates flowing through the same gates; base images from official registries with digests pinned; SBOM generated per build with Syft and stored as a build artefact, so "which services contain this CVE" is answerable with grep.

Tier 2: ephemeral runners are inherent to GitHub-hosted Actions; say so in docs rather than building runner infrastructure.

Tier 3: image signing and admission control (Cosign, Kyverno) stay the deferred January wave from the roadmap; exception entry with expiry January 2027. Build-once-deploy-many: VidCast already promotes by image tag through ArgoCD values, document that this satisfies the principle; no rebuilds between environments.

## Incident response, DR, SLOs

- Each repo's `docs/runbooks/` includes a leaked-credential runbook written BEFORE first deploy (revoke, rotate, audit CloudTrail for use, rebuild), per the original prompt's rule that the response is written before the secret is used. Ten lines is fine; existing and tested is the bar.
- Project E is the standard's incident-response and DR chapter made real: detection, containment, postmortem, tabletop-equivalent chaos drill, DR metrics table (RTO/RPO declared then measured, backup verification dated, drill dated). Other repos reference it rather than duplicating.
- SLOs: VidCast keeps its SLO/error-budget dashboard as the implementation; the PRD's measured targets are recorded in `docs/metrics.md`. MTTD/MTTR get real values from the Project E chaos day. Error-budget policy sentence in the VidCast README: budget exhausted means reliability work precedes features, and security incidents consume the budget.
- Cost: stop/start scheduling for any non-production instance that survives an evening; lifecycle policies and log retention set on day one (7-day CloudWatch retention is already the Project D rule); every ADR carries its cost line; security tooling costs appear in Project E's cost table.

## The baseline questions (Tier 1, adapted)

Before any project is declared done, its README answers, with links to evidence:

1. Can a new engineer deploy this safely from the README alone?
2. Can a failure be diagnosed from logs and dashboards without shelling into anything?
3. Does the cost table say what it costs and why?
4. Can CloudTrail plus Actions logs say who changed what and when?
5. Does teardown remove everything, provably?
6. Can a non-technical reader understand why this design was chosen (ADR index)?
7. Does the threat model name the threats, and does each have a mitigation or an exception?
8. Is there a written response for a leaked credential and a failed deploy?
9. What happens if a dependency (upstream image, action, provider) is compromised, and how would you know?

If any answer is no, the project is not finished, exactly as the original prompt puts it.
