# ARVIVATE: Master System Architecture & Workflow Blueprint

**Role:** B2B Financial & Legal Due Diligence Engine (India)

**Project Director:** 100% Decision Authority (Zero AI Assumptions)

**Target Environment:** Zero-Budget Cloud-Offloaded Architecture optimized for Low-Spec Hardware (Intel Core i3 10th Gen, 12GB RAM, Intel UHD Graphics)

## 1. Executive Vision & Core Constraints (Stage 1 Foundation)

* **Product Mission:** Deliver institutional-grade financial, legal, and risk due diligence reports on Indian companies (Listed & Private/SME) for B2B investors, lenders, and analysts.

* **Zero-Budget Mandate:** ₹0 infrastructure and API cost using free-tier cloud APIs, open-source tools, and serverless free tiers.

* **Hardware Protection Constraint:** Local machine (i3 10th Gen, 12GB RAM) must never run heavy local LLMs or multiple parallel headless browsers. All heavy AI inference runs on cloud APIs; local execution is restricted to lightweight Python orchestration, text chunking, regex sanitization, and targeted single-tab scraping.

* **Zero-Hallucination Standard:** Financial numbers, legal cases, and ratios must be 100% verifiable. If data is missing or unreadable, the system never guesses.

## 2. System Tech Stack & Infrastructure Architecture

| Layer | Selected Technology | Architectural Purpose & Hardware Benefit | 
| ----- | ----- | ----- | 
| **Orchestration Engine** | Custom Lightweight Python State Pipeline | Replaces heavy frameworks (like CrewAI/LangChain) to keep RAM usage under \~50MB and give 100% deterministic control. | 
| **AI Brain (LLM Layer)** | Multi-Provider Free Cloud API Fallback Router | Primary: Google Gemini Free Tier (`aistudio.google.com`) for large PDF context; Fallbacks: Groq / OpenRouter free models to prevent downtime. | 
| **Document Processing** | Local Lightweight PDF Chunker (RAG) | Extracts only relevant financial tables (Balance Sheet, P&L, Cash Flow, Auditor Notes) locally instead of flooding APIs with 300-page PDFs. | 
| **Data Sanitization** | Local Regex JSON Sanitizers + Unit Normalizer | Strips broken LLM markdown, enforces strict JSON schemas, and converts `₹ in Lakhs` to `₹ in Crores` before math runs. | 
| **Math & Ratios** | Pure Python Deterministic Calculator | Executes all financial ratios (Debt-to-Equity, ROE, Margins, YoY Growth) with 100% mathematical accuracy (0% AI math). | 
| **Web & Legal Sourcing** | Hybrid Scrape-on-Demand (Lightweight Search + Custom GitHub Browser Tool) | Uses 0MB-RAM open-index search first; wakes up the custom GitHub browser tool *only* for specific locked URLs (e.g., NCLT orders). | 
| **Database & Caching** | Supabase (Free Tier PostgreSQL) | Stores verified company profiles, financial baselines, and historical reports using a 90% cached / 10% live-check hybrid model. | 
| **Frontend & Hosting** | Next.js (React) + Tailwind CSS on Vercel (Free Tier) | Clean B2B dashboard with real-time step tracking and non-blocking background updates. | 

## 3. Director-Locked Workflow Rules (Loophole Elimination Matrix)

Every operational rule below was explicitly decided by the Project Director to eliminate system crashes, token waste, RAM overload, and data inaccuracies:

1. **Two-Step Identity Confirmation (Search** $\rightarrow$ **Confirm CIN** $\rightarrow$ **Run):**

   * *Rule:* Typing a company name never triggers heavy AI agents immediately. A 1-second lookup displays matching legal entities with their official **CIN (Corporate Identification Number)** and exchange ticker.

   * *Loophole Eliminated:* Prevents wasting free API tokens and polluting Supabase with data from a similarly named wrong company.

2. **Instant Show + Background Update ("The Dynamic Refresh"):**

   * *Rule:* If a company exists in Supabase cache, the saved baseline report renders in `0.1s`. Simultaneously, a non-blocking background worker checks for fresh legal/news risks and pushes live alerts to the UI if new events are found.

   * *Loophole Eliminated:* Eliminates user wait times on cached companies while ensuring investors never act on stale legal risk data.

3. **The 5-Second PDF Sourcing Fallback:**

   * *Rule:* For uncached companies, Arvivate checks official exchange/open endpoints (BSE/NSE) for up to **5 seconds**. If an official Annual Report is found, it auto-downloads. If not found (or if it is a private company), the pipeline gracefully pauses and prompts the user to upload the PDF manually.

   * *Loophole Eliminated:* Prevents endless web-scraping loops, CAPTCHA traps, and downloading wrong marketing brochures.

4. **Target-Locked URL Browsing ("The Sniper"):**

   * *Rule:* Broad discovery (news, court case mentions) runs via lightweight HTTP search queries. The heavy GitHub Browser Tool is triggered *only* when the Manager passes a specific, target-locked URL (such as an NCLT order page), extracts the text, and immediately terminates the browser instance.

   * *Loophole Eliminated:* Protects the 12GB RAM / i3 CPU from freezing due to multiple open Chromium instances.

5. **Batched Async Execution ("The Traffic Light"):**

   * *Rule:* Agents run in controlled batches rather than all at once. **Batch 1** (News + Legal Agents) runs concurrently $\rightarrow$ mandatory cooldown pause $\rightarrow$ **Batch 2** (PDF RAG + Financial Analyst + Judge Agent) runs.

   * *Loophole Eliminated:* Prevents `HTTP 429 (Too Many Requests)` rate-limit bans on free cloud APIs while keeping total runtime around 30–45 seconds.

6. **Targeted Micro-Search + Strict `"Not Disclosed"` Lock:**

   * *Rule:* If a core financial metric (e.g., Total Debt or Net Profit) is missing from the PDF, the agent executes **one** targeted micro-search (e.g., *"Company Name FY25 revenue filing/news"*). If verified, it tags the number as `"Sourced via Public Filing/News"`; if still unavailable, it strictly locks the field as `"Not Disclosed"`.

   * *Loophole Eliminated:* 0% AI hallucination or estimated/guessed numbers.

7. **Local Temp Checkpoint + Final Cloud Save:**

   * *Rule:* As each batch finishes, progress is written to a local temporary file (`temp_checkpoint.json`). Only after the entire pipeline succeeds does the system push the final JSON to Supabase and delete the local temp file.

   * *Loophole Eliminated:* Preserves Supabase free-tier write quotas and ensures mid-pipeline API crashes resume from the last completed step without burning earlier tokens.

8. **Unit Normalizer + Pure Python Calculator:**

   * *Rule:* AI agents are strictly forbidden from calculating percentages or ratios. AI extracts raw figures and their stated units (`Lakhs` or `Crores`). The Unit Normalizer standardizes all figures to `Crores`, and pure Python formulas compute all financial ratios.

   * *Loophole Eliminated:* Eliminates AI arithmetic errors and 100x scaling errors caused by Indian Balance Sheets mixing Lakhs and Crores.

9. **Live Step-by-Step Status Bar ("The Tracker"):**

   * *Rule:* During the 30–45 second analysis of a new company, the Next.js UI polls the backend checkpoint status every 2 seconds and updates a visual checklist (`✅ Company Verified` $\rightarrow$ `✅ Legal & NCLT Checked` $\rightarrow$ `⏳ Reading Annual Report...` $\rightarrow$ `⬜ Calculating Ratios`).

   * *Loophole Eliminated:* Eliminates blank-screen anxiety and user drop-offs during active processing.

## 4. End-to-End Operational Workflow (Step-by-Step)

```
[User Enters Name] 
       │
       ▼
(Step 1: Identity Lock) ──► User selects exact Company + CIN
       │
       ▼
(Step 2: Supabase Gatekeeper)
       ├──► [CACHE HIT]: Render 90% Baseline Report in 0.1s ──► Trigger Background Live Legal/News Check ──► Push Live Alert if new risk found
       │
       └──► [CACHE MISS]: Activate Live UI Step Tracker (⬜ ➔ ⏳ ➔ ✅)
                 │
                 ▼
       (Step 3: 5-Second PDF Sourcing)
                 ├──► Listed & Found (<5s): Auto-download Annual Report PDF
                 └──► Private / Not Found: Pause gracefully ──► Prompt User PDF Upload
                 │
                 ▼
       (Step 4: Batch 1 — External Intelligence)
                 ├──► Lightweight Search (News + Legal/NCLT mentions)
                 ├──► Target-Locked URLs found? ──► Wake GitHub Browser Sniper ──► Scrape & Close immediately
                 └──► Save progress to `temp_checkpoint.json`
                 │
                 ▼ (Cooldown Pause for Free API Rate Limits)
       (Step 5: Batch 2 — Internal Financial Extraction)
                 ├──► Local PDF Chunker extracts Balance Sheet, P&L, Cash Flow, Auditor Notes
                 ├──► AI extracts raw numbers + units (Lakhs/Crores)
                 ├──► Missing critical metric? ──► Run 1 Targeted Micro-Search ──► Found: Tag Source | Missing: Lock "Not Disclosed"
                 └──► Save progress to `temp_checkpoint.json`
                 │
                 ▼
       (Step 6: Deterministic Sanitization & Math)
                 ├──► Regex Sanitizer cleans AI JSON output
                 ├──► Unit Normalizer converts all numbers to Crores
                 └──► Pure Python Calculator computes Ratios & YoY Growth
                 │
                 ▼
       (Step 7: Risk Scoring & Final Publish)
                 ├──► Judge Agent synthesizes Financial Health + Legal Red Flags + Auditor Remarks
                 ├──► Save complete verified JSON to Supabase Cloud
                 ├──► Delete `temp_checkpoint.json`
                 └──► Render Final B2B Dashboard on Next.js

```

## 5. Agent Roster & Failure-Handling Protocols ("If This Happens, What Does the Agent Do?")

| Agent / Module | Primary Responsibility | Failure Scenario / Edge Case | Exact Recovery Protocol | 
| ----- | ----- | ----- | ----- | 
| **Identity Resolver** | Maps user query to official Indian CIN & Ticker. | Multiple companies share similar names (e.g., *Apex Solutions*). | Halts pipeline; forces user selection from CIN list before spending any AI tokens. | 
| **Cache Manager** | Checks Supabase for existing company record. | Cached report is >15 days old, and a new NCLT insolvency petition was filed yesterday. | Displays cached financials immediately (`0.1s`), runs live Legal Agent in background, and injects a red banner alert into the live UI. | 
| **Document Retriever** | Fetches Annual Report PDF. | Company is Private Limited or BSE/NSE link times out after 5 seconds. | Aborts auto-scrape at 5.0s; updates UI Tracker to `"Awaiting PDF Upload"` and waits for user file input. | 
| **Legal & News Scout** | Hunts litigation, NCLT, SEBI, and default news. | Important court order is behind a JS-heavy or CAPTCHA-protected portal. | Passes exact URL to **GitHub Browser Sniper**; extracts order text in single headless tab and kills browser process immediately to free RAM. | 
| **PDF RAG Extractor** | Reads financial tables from uploaded/fetched PDF. | PDF is 350 pages long (exceeds free API context or slows down i3 CPU). | Local chunker filters pages by financial keywords (`Balance Sheet`, `Profit and Loss`, `Independent Auditor`) and sends only relevant chunks. | 
| **Data Guard (Sanitizer)** | Validates extracted metrics and JSON structure. | Free LLM returns broken JSON or leaves `Total Debt` blank/ambiguous. | Regex sanitizer repairs JSON; triggers 1 micro-search for `Total Debt`; if still unverified, writes `"Not Disclosed"` and lowers Data Confidence Score. | 
| **Unit & Math Engine** | Normalizes currency scales and calculates ratios. | Revenue is reported in `₹ Lakhs` while Debt footnote is in `₹ Crores`. | Unit Normalizer detects unit tags and divides Lakhs by `100` to match Crores before Python executes division/ratio formulas. | 
| **LLM API Router** | Routes prompts to free cloud models. | Primary Gemini Free API returns `HTTP 429 (Rate Limit)` or `503 Timeout`. | Automatically rotates request to secondary free provider (Groq / OpenRouter) and resumes from `temp_checkpoint.json`. | 
| **Judge & Publisher** | Assigns final risk verdict and saves report. | Internet drops right before final save. | All extracted data remains safe in local `temp_checkpoint.json`; upon retry, skips Steps 3–6 and completes Step 7 immediately. | 
