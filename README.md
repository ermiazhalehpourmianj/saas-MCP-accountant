# Secure SaaS Accountant (MCP-Ready)

Rules over vibes.  
This is a secure, rules-first SaaS accountant that ingests bank-style transactions, classifies them with deterministic logic, and syncs them into a clean UI + Notion-friendly structure — all wired to be orchestrated by MCP tools.

![Secure SaaS Accountant UI](docs/demo-ui.png)

---

## What this project does

This repo is an **MVP for a real SaaS accountant**, designed like a production system:

- Reads **transaction feeds** (mock TD / PayPal–style data for now)
- Stores them in a **PostgreSQL/SQLite-style schema**
- Runs a **logical rule engine** to:
  - auto-categorize merchants (e.g., UBER → Transport)
  - bucket expenses (Food, Transport, Entertainment, etc.)
  - attach budgets and utilization
- Exposes a **web UI** to visualize:
  - total spend
  - spend per bucket
  - recent categorized transactions
- Exposes a clean HTTP API + MCP tool surface so an AI agent can:
  - ingest transactions
  - apply rules
  - sync to Notion
  - generate reports

By default it runs entirely **locally** with **mock data**, but the architecture is built to plug in **Plaid/Flinks + Notion** in a secure, read-only way.

---

## Features

- 🔐 **Security-first architecture**
  - Designed around read-only aggregators (Plaid/Flinks).
  - No banking credentials ever touch the app.
  - Minimal PII, encrypted storage in a real deployment.

- 🧠 **Logical programming accountant**
  - Priority-based rules engine (`pattern` + `field` → `category` + `bucket`).
  - Deterministic, explainable classifications.
  - Budgets per bucket with simple utilization stats.

- 🧩 **MCP-ready**
  - Clean tool surface to:
    - fetch transactions
    - apply rules
    - sync to Notion
    - generate monthly reports
  - Built to be driven by an LLM agent, but doesn’t depend on one.

- 📊 **Modern UI**
  - Minimal HTML + CSS frontend served by FastAPI.
  - Shows:
    - headline metrics
    - bucket breakdowns
    - recent transactions table.

- 🧱 **MVP that maps to a real SaaS**
  - Clear boundaries:
    - data layer (aggregator → DB)
    - logic layer (rules + budgets)
    - sync layer (Notion / reports)
  - Easy to extend to multi-tenant, proper auth, and real open banking.

---

## Architecture

The system is split into three main layers:

### 1. Data Layer

- Transaction ingestion from a provider abstraction (mock JSON for now).
- Normalized transaction schema:

  ```json
  {
    "id": "txn_123",
    "date": "2025-11-29",
    "amount": -23.45,
    "currency": "CAD",
    "merchant": "UBER *TRIP",
    "raw_category": "Travel",
    "source_system": "TD_CHEQUING"
  }
