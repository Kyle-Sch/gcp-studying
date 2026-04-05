# 15. SRE Practices

## DevOps vs SRE

| | DevOps | SRE |
|--|--------|-----|
| What it is | Philosophy / culture | Concrete implementation of DevOps |
| Created by | Industry | Google (Benjamin Treynor Sloss, 2003) |
| Key output | Cultural practices | Error budgets, SLOs, toil reduction |

## Five DevOps Pillars â†’ SRE Practices

| Pillar | SRE Implementation |
|--------|--------------------|
| Reduce organizational silos | Shared SLOs + error budgets; shared production ownership |
| Accept failure as normal | **Blameless postmortems**; psychological safety |
| Implement gradual change | **CI/CD** + **canarying** |
| Leverage tooling & automation | Eliminate **toil**; automate everything automatable |
| Measure everything | SLIs, SLOs, error budgets, OKRs, toil tracking |

## Blameless Postmortem

A written record of an incident containing:
1. Incident details + timeline
2. Actions taken to mitigate/resolve
3. Impact assessment
4. Trigger + all root causes (use the **5 Whys** technique)
5. Follow-up actions to prevent recurrence

> **Blameless** = focuses on systemic causes, not individual blame

## What is Toil?

Toil is work that is **ALL of these**:
- Manual
- Repetitive
- Automatable
- Tactical (not strategic)
- Has no enduring value
- Scales linearly with service growth

> SRE rule: keep toil **below 50%** of an SRE's time. The rest is engineering work.

## Canarying

Deploy a change to a **small, representative subset** of users first:
1. Canary group must be large enough to be representative
2. Small enough that if it breaks, the whole service isn't harmed
3. Simple enough to monitor and roll back quickly

## CI/CD

| | Definition |
|--|-----------|
| **CI** (Continuous Integration) | Build, integrate, and test code in dev environment continuously |
| **CD** (Continuous Delivery) | Deploy to production frequently, at the cadence the business chooses |

## OKRs
- Google's goal-setting framework: **Objectives + Key Results**
- Graded **0.0 to 1.0** â€” optimal score is **60â€“70%** (stretch goals, not performance reviews)
- Graded publicly; cross-functional teams share OKRs
