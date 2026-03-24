# Dynamic Application Security Testing with Snyk
### A Practical Guide to Enterprise Vulnerability Management

Welcome to the **Snyk DAST & Vulnerability Management** project! This repository documents my hands-on, day-to-day use of Snyk as a core part of an enterprise application security program. It covers the full lifecycle of vulnerability management — from detection and triage to remediation, SLA tracking, and developer workflow integration.

---

## 🔍 Overview

[Snyk](https://snyk.io/) is a developer-first security platform used for identifying and remediating vulnerabilities across open-source dependencies, container images, infrastructure as code, and application code. In this role, I serve as an **Organization Admin** responsible for the full operational scope of Snyk within the security program.

```
Security Engineer (Org Admin)
        │
        ├── Project Management (Prod / Non-Prod)
        ├── Vulnerability Triage & SLA Enforcement
        ├── Jira Integration & Ticket Lifecycle
        ├── GitHub PR Automation
        ├── Exploitability Assessment
        ├── Ignore Rules & False Positive Management
        └── Access Provisioning (Admins & Collaborators)
```

---

## 🧰 Tools & Integrations

| Tool | Purpose |
|------|---------|
| **Snyk** | Core DAST/SCA platform — vulnerability scanning, triage, remediation |
| **Jira** | Issue tracking — auto-generated and manually managed tickets |
| **GitHub** | Source control — automated fix PRs, SCM integration |
| **Snyk CLI** | Pipeline scanning, local testing, policy enforcement |

---

## 🏗️ Core Responsibilities & Workflows

### 1. Organization Administration

As Org Admin, I manage access control across the Snyk organization:

- **Provision access** for new admins and collaborators, assigning appropriate roles (Org Admin, Collaborator, Viewer)
- **Manage service accounts** for CI/CD pipeline integrations
- **Configure organization-level settings** including notification thresholds, severity policies, and integration tokens
- **Onboard new teams** and align them with existing project structures and SLA policies

```
Snyk Org Admin
    ├── Admins       → Full access: manage settings, projects, integrations
    ├── Collaborators → View/triage: test projects, manage ignores within scope
    └── Viewers       → Read-only: dashboards and reports
```

---

### 2. Project Management — Prod vs. Non-Prod

Projects are segmented by environment to enforce appropriate risk thresholds and SLA timelines:

- **Production projects** — monitored continuously with strict SLAs; Critical/High findings require immediate action
- **Non-Production projects** — scanned on commit/PR with more flexible remediation windows
- Projects are tagged and organized by environment, team, and application to enable reporting and prioritization at scale

> Separating Prod from Non-Prod allows security teams to focus remediation urgency where it matters most — without creating noise that leads to alert fatigue in lower-risk environments.

---

### 3. Vulnerability Tracking & Remediation SLAs

Vulnerabilities are tracked from detection through closure against defined SLA thresholds:

| Severity | Production SLA | Non-Production SLA |
|----------|---------------|-------------------|
| Critical | 3 days | 7 days |
| High | 7 days | 30 days |
| Medium | 30 days | 90 days |
| Low | 90 days | Best effort |

**Workflow:**
1. Snyk detects a vulnerability during a scan (CI pipeline, scheduled monitor, or PR check)
2. Issue is triaged — severity confirmed, context assessed (reachability, exploitability, environment)
3. Jira ticket created (automated or manual) and assigned to the responsible team
4. Remediation tracked through closure; SLA breach triggers escalation
5. Verification scan confirms fix prior to ticket closure

---

### 4. Exploitability Assessment

Not all vulnerabilities carry equal real-world risk. Each finding is assessed using Snyk's **Risk Score** and contextual factors:

- **Snyk's Exploit Maturity** — is a known exploit publicly available? (Mature, Proof of Concept, No Known Exploit)
- **Reachability analysis** — is the vulnerable function actually called in the application's code path?
- **CVSS vector analysis** — attack complexity, authentication requirements, impact scope
- **Environmental context** — is the affected component exposed to the internet, or internal only?

This triage process ensures engineering effort is directed at the vulnerabilities most likely to be exploited, not just those with the highest theoretical score.

---

### 5. Ignore Rules & False Positive Management

Not every finding requires a fix. I manage a structured ignore policy for:

- **False positives** — findings flagged due to library naming conflicts or scanner misidentification; documented and ignored with justification
- **Unremediated vulnerabilities** — no fix available from the upstream maintainer; ignored with expiry date and reviewed on a defined cadence
- **Accepted risk** — risk formally accepted by the application owner; business justification documented within the ignore rule

All ignores include:
- A written reason (visible in Snyk UI and audit log)
- An expiry date (typically 30–90 days) to enforce re-review
- The identity of the user who applied the ignore (enforced by Org Admin role)

---

### 6. Jira Integration — Ticket Creation & Lifecycle Management

Snyk is integrated with Jira to automate the creation of security tickets and eliminate manual handoff friction:

**Automatic ticket creation** is configured for Critical and High severity findings — a Jira issue is opened the moment a vulnerability is detected in a monitored project.

**Manual ticket creation** is used for Medium/Low findings that have been triaged and confirmed for remediation.

Each Jira ticket includes:
- CVE/CWE identifiers
- Affected package, version, and fix version
- Snyk issue URL for full context
- SLA due date based on severity and environment
- Recommended remediation steps pulled directly from Snyk's database

The Jira integration is bidirectional — ticket status updates in Jira are reflected in Snyk's issue view, and closing a ticket after remediation triggers a re-scan to verify the fix.

---

### 7. Automated Fix PRs via GitHub Integration

Snyk's GitHub integration enables **automatic pull request generation** for dependency vulnerabilities where an upstream fix is available:

- When Snyk identifies a vulnerable dependency with a known fix, it opens a PR directly in the affected GitHub repository
- PRs include a full changelog, CVSS score, and a link to the Snyk advisory
- Fix PRs are reviewed and merged by the responsible engineering team; security team monitors merge status and tracks against SLA
- **Upgrade PRs** are also generated proactively for outdated dependencies, even before a CVE is published

```
Snyk detects vuln with available fix
            │
            ▼
Auto PR opened in GitHub repo
  ├── Diff: package version bump
  ├── Description: CVE, CVSS, fix summary
  └── Status check: Snyk re-scans PR branch
            │
            ▼
Engineer reviews → merges → Snyk verifies → issue closed
```

---

## 📊 Example Scan & Remediation Lifecycle

```
Scan 1 (Baseline)     → Establishes vulnerability baseline for the project
Scan 2 (Post-deploy)  → New Critical identified: lodash prototype pollution (CVE-2019-10744)
                         → Jira ticket auto-created, SLA clock starts (3 days, Prod)
                         → Snyk opens fix PR: lodash 4.17.11 → 4.17.21
Scan 3 (Post-merge)   → Vulnerability resolved; Jira ticket closed; SLA met
Scan 4 (Weekly cycle) → No new Criticals; 2 Mediums triaged, ignored with expiry
```

---

## 🛡️ Compliance & Reporting

- **Weekly vulnerability reports** distributed to security leadership — open issues by severity, SLA compliance rate, trending over time
- **Audit logs** maintained for all ignore rules, access changes, and policy modifications (Snyk Org Admin audit trail)
- Supports internal compliance requirements and AppSec program metrics

---

## 💡 Key Takeaways

This project demonstrates real-world, enterprise-scale application security operations using Snyk, including:

- End-to-end vulnerability management from detection through verified remediation
- Risk-based triage using exploitability data — not just raw severity scores
- Seamless developer integration through automated Jira tickets and GitHub fix PRs
- Governance and accountability via structured ignore policies and SLA enforcement
- Access control and security program administration at the organizational level

---

## 📚 References

- [Snyk Documentation](https://docs.snyk.io/)
- [Snyk Exploit Maturity](https://docs.snyk.io/manage-risk/prioritize-issues-for-fixing/priority-score)
- [Snyk Jira Integration](https://docs.snyk.io/integrate-with-snyk/jira-and-slack-integrations/jira-integration)
- [Snyk GitHub Integration & Fix PRs](https://docs.snyk.io/integrate-with-snyk/git-repositories-scms-integrations-with-snyk/snyk-github-integration)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
---
