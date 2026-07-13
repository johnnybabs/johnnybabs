# CLAUDE.md, GitHub profile fix

You are Claude Code operating with John Babalola's GitHub access (`gh` CLI authenticated as `johnnybabs`). Execute this fix in one session. Total time: under an hour. No AWS resources involved.

## Context

John is applying for Cloud DevOps roles in Belfast and remote UK. His GitHub link goes on every CV, but the profile currently shows location "Lagos, Nigeria", no bio, no profile README, no pinned repos, and surfaces junk repositories (`John`, `Hello-World`, `github-slideshow`) and tutorial forks ahead of his real work (`vidcast`, `cbaprints`, `student-portal-ha-aws`).

## Tasks, in order

1. Create the profile repository `johnnybabs/johnnybabs` (public). Copy `PROFILE_README.md` from this folder into it as `README.md`. Before committing, verify every repo link in it resolves (`gh repo view johnnybabs/<name>`); if a repo name differs (for example the CBAPrints repo may not be exactly `cbaprints`), fix the link to the real name, do not invent one.
2. Update profile metadata via `gh api -X PATCH /user`:
   - `location`: "Belfast, United Kingdom"
   - `bio`: "Cloud DevOps Engineer | AWS, Kubernetes, Terraform | Belfast, UK"
   - `blog`: "https://johnbabalola.com"
   - Keep the ORCID link if a websites field allows both; otherwise the blog field takes johnbabalola.com and ORCID stays in the README of academic repos only.
3. Pin repositories in this order using the GraphQL API (pinned items mutation): `vidcast`, the CBAPrints repo, `student-portal-ha-aws`, `johnnybabs` (profile repo). Leave two pin slots free for Projects A and D repos when they ship.
4. Clean up junk: archive (do not delete without asking John) `John`, `Hello-World`, `github-slideshow`. Leave learning forks (`py`, `Complete-Python-3-Bootcamp`, `02456-deep-learning-with-PyTorch`) alone, unpinned.
5. Repo hygiene on the three flagship repos: confirm each has a description and topics set. Suggested topics: `aws`, `terraform`, `kubernetes`, `devops` plus per-repo specifics (`eks`, `github-actions`, `cloudformation`, `serverless`, `high-availability`). Set them with `gh repo edit --add-topic`.
6. Verify: fetch `https://github.com/johnnybabs` and confirm the README renders, pins are correct, location and bio updated. Screenshot for John.

## Guardrails

- Never claim certifications as held; SAA-C03 and AZ-900 are in progress, the README wording already reflects this. Do not "improve" it into a claim.
- Do not delete anything, archive only, and only the three junk repos named above.
- UK English in any text you write. No em dashes as connectors.
- If the CBAPrints or portal repos are private, ask John whether to make them public before pinning; a pinned private repo is invisible to recruiters.
