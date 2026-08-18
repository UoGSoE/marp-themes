---
marp: true
theme: uofg
paginate: true
header: 'School of Computing Science'
footer: 'University of Glasgow'
---

<!--
  THEME SWITCHING
  ===============
  Change the `theme:` line above to switch between:
    - uofg           — Pure UofG brand (Noto Sans, university blues)
    - uofg-creative  — Brand-creative (Literata serif headings, warmer palette)
    - personal       — Unconstrained dark theme (Bricolage Grotesque, terracotta)

  Build with (the file must come BEFORE --theme-set, or marp-cli builds nothing):
    npx @marp-team/marp-cli examples/demo.md --theme-set ./themes/

  Or for live preview:
    npx @marp-team/marp-cli -p examples/demo.md --theme-set ./themes/
-->

<!-- _class: lead -->

# Building Better Developer Tooling
## A practical approach to internal platforms

April 2026

---

## Why internal tooling matters

Most developer time isn't spent writing features — it's spent navigating systems, waiting for builds, and searching for context.

Good internal tooling reduces that friction:

- **Faster feedback loops** — seconds, not minutes
- **Self-service infrastructure** — no tickets required
- **Consistent environments** — works on my machine, and yours
- **Discoverable documentation** — where the code already is

---

## The current landscape

| Tool | Purpose | Adoption |
|------|---------|----------|
| GitLab CI | Build & deploy pipelines | 92% of teams |
| Docker Swarm | Container orchestration | All services |
| Lando | Local development | 78% of teams |
| Laravel Forge | Server provisioning | Legacy only |

> We've standardised on Docker Swarm for production, but local development still varies across teams.

---

### Architecture overview

## How our platform fits together

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  Developer   │────▶│  GitLab CI   │────▶│   Docker     │
│  Workstation │     │  Pipeline    │     │   Swarm      │
└─────────────┘     └──────────────┘     └─────────────┘
       │                    │                    │
       ▼                    ▼                    ▼
  Local Lando         Build & Test         Production
  Environment          Artefacts           Deployment
```

---

<!-- _class: divider -->

# Key principles
## What we've learned building internal tools

---

## 1. Start with the pain

Don't build platforms in the abstract. Find where developers are losing time *right now* and fix that first.

**Discovery methods:**
1. Shadow a developer for a morning
2. Measure CI pipeline wait times
3. Count support tickets by category
4. Ask: "What did you Google three times this week?"

The answers are usually mundane — and that's the point. Boring problems compound.

---

## 2. Make the right thing the easy thing

If the secure, tested, well-documented path requires *more* effort than the shortcut, developers will take the shortcut every time.

```yaml
# Bad: 47-line CI config that every team copy-pastes and modifies
stages:
  - build
  - test
  - deploy
# ... (imagine 44 more lines of YAML)

# Good: one line that does the right thing
include: '/templates/laravel-app.yml'
```

---

## 3. Code examples beat documentation

```php
// A working example teaches more than a page of docs
class CreateDeployment
{
    public function handle(Project $project): Deployment
    {
        return $project->deployments()->create([
            'commit_sha' => $project->latestCommit(),
            'environment' => 'production',
            'triggered_by' => auth()->user(),
        ]);
    }
}
```

Developers learn by reading code. Put well-commented examples where they'll find them — in the repo, not in Confluence.

---

## Measuring impact

How do we know the platform is working?

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Time to first deploy | 3 days | 2 hours | -93% |
| CI pipeline duration | 18 min | 6 min | -67% |
| Support tickets/week | 23 | 8 | -65% |
| Dev satisfaction (NPS) | 34 | 71 | +109% |

The numbers matter, but so does the qualitative feedback. When developers stop complaining about infrastructure, you know it's working.

---

> The best infrastructure is invisible. When it works well, nobody notices it. When it breaks, everyone does.

---

<!-- _class: lead -->

# What's next?

## Roadmap for 2026-27

---

## Coming soon

- **Self-service databases** — spin up PostgreSQL instances without a ticket
- **Preview environments** — every merge request gets a live URL
- **Unified logging** — one place to search all application logs
- **Cost dashboards** — teams see what their services actually cost

### Timeline

Phase 1 (Q3 2026): Preview environments and unified logging
Phase 2 (Q4 2026): Self-service databases
Phase 3 (Q1 2027): Cost visibility and optimisation tooling

---

<!-- _class: lead -->

# Thanks
## Questions?

user@example.ac.uk
