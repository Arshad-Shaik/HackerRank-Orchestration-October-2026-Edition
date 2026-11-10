# HackerRank-Orchestration-October-2026-Edition
👻 Ghost Data Still Exists: AI Deletion-Audit Engine

![Final Rank: #1906](https://img.shields.io/badge/Global_Rank-%231906_%2F_4094-success?style=for-the-badge)

*An AI-powered data compliance engine built to verify the removal of Personally Identifiable Information (PII) across distributed microservices.*

<!-- Add your certificate image here -->
<img width="1440" height="980" alt="HackerRank Orchestrate October 2026 Edition" src="https://github.com/user-attachments/assets/1f862684-8f4d-4ad4-a666-7193052734bb" />

## 📌 Overview
This project was built during an intense 24-hour sprint for the **HackerRank Orchestrate (Oct 2026)** hackathon. The challenge required building an agentic audit system capable of parsing unstructured data (PDFs, images, raw logs) and structured data (SQLite dumps, JSON manifests) to detect database replication drift, active synchronization flows, and non-compliant account tombstones.

The evaluation included a live technical interview with an AI Judge to defend architectural trade-offs, alongside automated grading of the generated `output.csv`.

## 🏗️ Architecture & Approach
I utilized a **Hybrid Deterministic-AI Design**, utilizing Google Antigravity as an agentic pair-programmer to scaffold boilerplate and design regex extractors. 

* **Time-Travel Audit (`audit_at`):** Evaluated compliance precisely at a designated cutoff timestamp by inspecting raw storage rather than trusting API status claims.
* **Multi-Phase Lifecycle:** Enforced a strict order of operations: `FINDING` $\to$ `REMEDIATION` $\to$ `VERIFICATION`.
* **Canonical Sorting:** Ensured deterministic output by sorting `steps_json` by phase, operation rank, `system_id`, and `resource_id`.
* **Intent Classification:** Implemented a fast heuristic English keyword list for rapid classification of non-deletion requests (e.g., password resets, support queries) as `NOT_APPLICABLE`.

## 🐛 Key Bugs Solved During Development
Working with messy, multi-source data dumps required aggressive debugging. Key fixes included:

1. **SQL Replica Quoted Identifier Mismatch:** Initial parsers failed on SQLite dumps using double quotes (`"table_name"`). I enhanced the regex to handle both double quotes and backticks, recovering previously missed replica evidence.
2. **SQL Values Comma-Splitting Corruption:** Naive string splitting by comma broke embedded JSON strings containing internal commas (e.g., `{"full_name": "...", "email": "..."}`). Replaced this with targeted regex extractors for PII fields.
3. **Multi-Source Evidence Ingestion Gaps:** Discovered that snapshot JSONs nested data under a top-level `"snapshots"` array. Adding full JSON traversal unlocked missing tombstone records and security events.
4. **Log Trace Correlation:** User queries and completion events were separated across log files. I implemented a trace grouping mechanism using `trace_id` to unify parameters with their respective resource IDs.
5. **Historical Email Hash Resolution:** Certain service events logged SHA-256 hashes instead of plain-text emails. I augmented the identity resolution pipeline to map and extract historical `email_hash` values.

## ⚖️ Trade-offs & Future Iterations
Given the 24-hour deadline, I prioritized speed and determinism over full dynamic AI parsing:
* **Static Rule Parsing:** The engine relied on hardcoded resource prefixes rather than dynamically reading `deletion_rules.csv`.
* **English-Only Intent:** I accepted the trade-off of using a static English keyword list, meaning foreign-language requests (e.g., Spanish "dar de baja") could trigger false positives. 

## 🛠️ Tech Stack
* **Language:** Python 3
* **AI Tooling:** Google Antigravity (LLM Code Generation & Multimodal OCR)
* **Data Processing:** JSON, SQLite, CSV, Regex

---
*Built by [Arshad Wasib Shaik](https://github.com/Arshad-Shaik) for HackerRank Orchestrate.*
