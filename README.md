# Cloud Security Audit & Compliance Automation Platform

Fully autonomous, event-driven pipeline that scans an entire AWS environment, classifies every finding against CIS Benchmarks, stores a persistent audit trail, runs AI-powered security analysis, and delivers a formatted compliance report — with zero manual input.

**What used to take hours runs in under 2 minutes.**

---

## Demo

[▶ Loom Walkthrough](https://www.loom.com/share/ab966bb76ebd4c44bef1ec6b0c906fdc)

---

## The Problem

Manually auditing AWS infrastructure is a significant engineering bottleneck. Security teams spend hours extracting metadata, parsing raw JSON, classifying findings, and writing compliance reports — a process that is slow, error-prone, and impossible to scale continuously.

---

## Architecture

Three decoupled workflows run in sequence:

```
[PROD] SecurityFlow_Core_Engine_v1
  → AWS CLI scan (IAM, S3, CloudTrail, EC2, RDS)
  → Finding classifier
  → Supabase (persistent audit trail)
  → DLQ (error capture)
        ↓
Cloud_report_generation (Langflow)
  → AI security analysis (Groq / Llama 3)
  → Risk score calculation (0–100)
  → Structured finding aggregation
        ↓
generate-report
  → Formatted compliance report output
  → Prioritized remediation roadmap
```

---

## Security Coverage

| Domain | Checks |
|---|---|
| IAM | Overpermissioned roles, wildcard policies, unused credentials, MFA enforcement |
| S3 | Public bucket exposure, ACL misconfigurations, encryption-at-rest status |
| CloudTrail | Logging gaps, trail integrity, multi-region coverage |
| EC2 | Security group exposure, open ingress rules, public instance detection |
| RDS | Public accessibility, encryption status, backup configuration |

---

## Compliance Mapping

Findings are classified against:
- **CIS AWS Foundations Benchmark** (v1.5)
- **NIST SP 800-53** control families
- **AWS Security Hub** finding standards

---

## Risk Scoring Logic

The AI analysis engine produces a risk score from 0 to 100 using a weighted severity model:

| Finding Severity | Weight |
|---|---|
| CRITICAL (public data exposure, no encryption) | 40 pts |
| HIGH (IAM over-permission, logging gaps) | 25 pts |
| MEDIUM (weak policies, unrotated keys) | 15 pts |
| LOW (informational, best practice gaps) | 5 pts |

Final score = sum of weighted findings, normalized to 100.
Risk level thresholds: **LOW** (0–39) · **MEDIUM** (40–69) · **HIGH** (70–100)

---

## Report Output

Each generated report contains:
- Overall risk score and risk level
- Per-service finding breakdown
- CIS Benchmark compliance gap list
- Prioritized remediation roadmap (ordered by severity)
- Timestamp and scan metadata for audit trail

---

## Engineering Highlights

**Decoupled pipeline**
Three independent workflows communicate via Supabase. Each stage can fail and be retried independently without corrupting the audit trail.

**Persistent audit trail**
Every scan writes findings to Supabase before analysis begins. If the AI stage fails, the raw findings are preserved and the report can be regenerated without re-scanning.

**Dead Letter Queue**
AWS CLI failures are captured in a DLQ table in Supabase. Failed scans are logged with error context for manual review and replay — no silent pipeline failures.

**Stateless scan execution**
Each scan is fully stateless — no local files, no in-memory state. All intermediate results pass through Supabase, making the pipeline fully reproducible.

---

## Failure Handling

| Failure | Behaviour |
|---|---|
| AWS CLI scan failure | Logged to DLQ with error context; audit trail entry marked as incomplete |
| AI analysis timeout | Report generation retried independently; raw findings preserved in Supabase |
| Langflow unreachable | Core engine completes and exits cleanly; report generation queued for retry |

---

## Setup

**Prerequisites**
- n8n instance (self-hosted)
- AWS CLI configured with audit-role credentials (read-only policy recommended)
- Supabase project with audit trail schema
- Groq API key
- Langflow instance

**Steps**
```bash
# 1. Import workflows
# Import all three .json files into n8n in order:
# SecurityFlow_Core_Engine_v1.json
# Cloud_report_generation.json (Langflow)
# generate-report.json

# 2. Connect credentials
# AWS CLI, Supabase, Groq API

# 3. Configure Supabase schema
# Run /docs/supabase_schema.sql to create audit trail tables

# 4. Set AWS scan scope
# Update the scan node with your target AWS account ID and regions

# 5. Activate
```

**.env.example**
```
GROQ_API_KEY=your_key_here
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_anon_key
AWS_PROFILE=audit-role
```

---

## Stack

- **Orchestration:** n8n (self-hosted)
- **Cloud Scanning:** AWS CLI
- **AI Analysis:** Groq / Llama 3
- **Workflow AI:** Langflow
- **Persistence:** Supabase (PostgreSQL)
- **Infrastructure:** Docker

---

## License

MIT
