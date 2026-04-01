# 🔒 Autonomous Cloud Compliance & AI Auditing Engine

> **Stack:** n8n · Docker · Python/Flask · AWS CLI · Langflow · Supabase · Groq  
> **Status:** Production  
> **Loom Walkthrough:** https://www.loom.com/share/ab966bb76ebd4c44bef1ec6b0c906fdc

---

## 🧩 The Problem

Manually auditing AWS infrastructure is a massive engineering bottleneck. Security teams spend hours extracting metadata from every service, parsing raw JSON, classifying findings, and writing compliance reports — a process that is slow, error-prone, and impossible to scale.

---

## ⚙️ The Solution

A fully autonomous, decoupled event-driven pipeline that scans an entire AWS environment, classifies every finding, stores a persistent audit trail, runs AI-powered security analysis, and delivers a formatted compliance report — with zero manual input.

**What used to take hours now runs in under 2 minutes.**

---

## 🏗️ Architecture Overview

This system is composed of 3 separate workflows working together:

```
[PROD] SecurityFlow_Core_Engine_v1  →  Cloud_report_generation (Langflow)  →  generate-report
        ↓                                        ↓
   Supabase (audit trail)              DLQ (error handling)
```

---

### Report includes:
- Overall Risk Score (0–100)
- Risk Level (LOW / MEDIUM / HIGH)
- CIS Benchmark findings
- Compliance gaps
- Prioritized remediation roadmap

---

## 📊 Business Value

- Eliminates hours of manual JSON parsing and infrastructure documentation
- Guarantees continuous compliance readiness
- Removes human error from security audits
- Produces audit-ready reports on demand
- Reduces cost of third-party cloud security assessments

