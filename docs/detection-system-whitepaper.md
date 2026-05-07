# Detection System Redesign (Whitepaper)

## Purpose
This redesign separates **scam activity**, **attacker tooling**, and **detection automation** so investigators can quickly answer three core questions:

1. What scam is happening?
2. What attacker tool is being used?
3. How was it detected?

---

## Core Data Model

The system has three first-class entities:

| Entity | Meaning | Question it Answers |
|---|---|---|
| **Campaign** | A specific scam wave active in a period | “What operation is happening now?” |
| **Kit** | A reusable phishing/scam toolkit | “What attack engine is this?” |
| **Workflow** | A detection rule / hunt query / automation | “How did we find it?” |

### 1) Campaign (Scam Wave)
A **campaign** represents a live or historical operation.

**Examples**
- Fake Microsoft login pages (May 2026)
- ENBD phishing campaign targeting Thailand
- Fake investment scam wave

**Campaign tracks**
- Targets (brands, regions, user groups)
- Start and end dates
- Evidence and analyst notes
- URLs and domains
- Template fingerprints
- Favicon variants

Think of campaign as a **case file** for one operation.

### 2) Kit (Attacker Tool)
A **kit** represents reusable attacker tooling.

**Examples**
- Tycoon 2FA
- FEMITBOT
- Darcula
- Evilginx

A single kit may target many brands (Microsoft, ENBD, Binance, Netflix, etc.).

- **Campaign = scam wave**
- **Kit = reusable attack engine**

### 3) Workflow (Detection Rule)
A **workflow** is an automated search/detection process.

**Examples**
- Search domains containing `enbd`
- Scan URLs from Telegram channels
- Identify suspicious login forms

Workflows find candidates and produce detections.

---

## Relationship Rules (Key Improvement)

Each workflow has exactly one scope:

- **Campaign-scoped**, or
- **Kit-scoped**, or
- **Brand-scoped**, or
- **Unscoped** (exploration mode)

### Scope examples
- Campaign-scoped: “Find more URLs related to ENBD Thailand wave.”
- Kit-scoped: “Find any websites using FEMITBOT patterns.”
- Brand-scoped: “Find Microsoft lookalike login pages.”
- Unscoped: broad hunting before attribution.

### Why this matters
When a workflow is scoped, every URL it finds inherits that scope automatically.

Example:
- Workflow scope = Campaign `ENBD Thailand Wave May 2026`
- Result = URL is auto-associated with that campaign

This removes manual grouping and improves consistency.

---

## Investigation Lifecycle

### Step 1 — Explore
Analysts run broad workflows with no strict attribution.

### Step 2 — Promote
Once a coherent cluster appears, analysts create a campaign.

Example campaign:
- `ENBD Thailand Wave — May 2026`

### Step 3 — Attribute
Analysts link the campaign to a known kit after deeper analysis.

Example:
- Campaign attributed to `FEMITBOT`

### Step 4 — Scale
Add more workflows:
- Campaign-specific workflows for operation expansion
- Kit-global workflows for cross-brand discovery

---

## Detection Pipeline

Every URL moves through three layers:

### Layer 1 — Backend Analysis
Extract technical and contextual signals:
- HTML/DOM structure
- Suspicious form behavior
- Keyword and brand token features
- AI-assisted content classification
- Login-page indicators
- Hostname and domain risk features

### Layer 2 — Matching
Compare extracted signals against:
- Known templates
- Favicon fingerprints
- Workflow conditions
- Brand indicators

### Layer 3 — Tagging
Apply multi-dimensional tags automatically.

Example tags for one URL:
- Brand: Microsoft
- Kit: FEMITBOT
- Campaign: Webex Q1 2026

---

## Traceability and Auditability

This model upgrades detection from a flat event stream (“we found a URL”) into a linked evidence graph:

- **Which workflow** found it
- **Why** it matched
- **Which campaign** it belongs to
- **Which kit** it is associated with

Result: cleaner investigations, faster triage, and stronger audit trails.

---

## Short Summary

Separate detection intelligence into:

- **Campaigns** = scam waves
- **Kits** = reusable attacker tools
- **Workflows** = automated hunting rules

Then connect them with strict scope rules so detections are automatically organized and explainable.
