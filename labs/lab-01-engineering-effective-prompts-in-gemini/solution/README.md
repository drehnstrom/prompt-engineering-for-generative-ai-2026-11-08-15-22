# Solution: Engineering Effective Prompts in Gemini

## End State

A complete suite of tested, high-quality prompt patterns executed in Google Gemini demonstrating:
1. Structured four-element prompt (Role, Task, Context, Constraints) contrasting against an unstructured query.
2. Direct instruction prompting with explicit output schemas (Markdown comparison table and structured JSON).
3. Few-shot classification prompt maintaining uniform delimiter patterns (`Input:` and `Output:`) across balanced classes.
4. Grounded reference prompt enforcing strict closed-domain rules, direct citations, and an exact fallback string for missing data.
5. Iteratively refined prompt demonstrating diagnosis of verbosity and loose structure, resolved through targeted negative constraints and schema enforcement.
6. Reusable prompt template incorporating parameterized variables for repeatable team workflows.

## How to Verify

For each prompt below, paste the prompt text into Google Gemini (at `https://gemini.google.com/` using a standard personal or workspace account) and verify that the output meets the specified success criteria.

### Task 1: Structured Four-Element Prompt Verification

**Prompt:**
```text
Role: You are a principal cloud architect mentoring associate DevOps engineers.
Task: Explain the core architectural difference between containerization and virtual machines.
Context: Our team is migrating monolithic Java microservices to Google Kubernetes Engine.
Constraints:
- Provide exactly three concise bullet points.
- Include one production migration consideration per bullet.
- Do not exceed 150 words total.
- Avoid introductory pleasantries or closing remarks.
```

**Verification Check:**
- Gemini adopts a senior architectural mentor tone.
- Output contains exactly three bullet points.
- Each bullet point includes a production migration consideration relevant to containerization vs VMs.
- Output length is under 150 words with no conversational filler (“Sure, I can help with that”).

### Task 2: Explicit Schemas and Direct Instructions Verification

**Part A: Markdown Table**
**Prompt:**
```markdown
Task: Compare relational databases (PostgreSQL) and document stores (MongoDB) for e-commerce product catalogs.
Output Format: Present the comparison exclusively as a Markdown table with the following four columns:
| Dimension | Relational (PostgreSQL) | Document Store (MongoDB) | Tradeoff Summary |
Row requirements:
- Include exactly four rows: Schema Flexibility, Query Complexity, ACID Compliance, Horizontal Scalability.
- Keep each cell concise (under 20 words).
- Do not output any prose, commentary, or markdown outside the table.
```

**Verification Check:**
- Output consists solely of a clean Markdown table with the four specified headers and four specified rows.
- No conversational introduction or concluding text appears.

**Part B: Strict JSON Schema**
**Prompt:**
```markdown
Task: Extract customer feedback details from the review below.
Customer Review: "The mobile app keeps crashing during checkout after the latest update. Lost my entire shopping cart twice. Fix this immediately!"

Output Format: Return valid JSON matching this schema:
{
  "customer_sentiment": "Positive" | "Neutral" | "Negative",
  "urgency_level": "Low" | "Medium" | "High",
  "primary_issue": "string (under 10 words)",
  "affected_component": "string (1-3 words)",
  "recommended_action": "string (one sentence)"
}

Constraints:
- Do not include markdown code fences or conversational text outside the JSON object.
- Ensure all JSON keys and string values use valid syntax.
```

**Verification Check:**
- Output is a parseable JSON object with keys: `customer_sentiment` (“Negative”), `urgency_level` (“High”), `primary_issue`, `affected_component`, and `recommended_action`.

### Task 3: Few-Shot Classification Verification

**Prompt:**
```text
Classify incoming customer support inquiries by Sentiment, Category, and Urgency. Use only the following allowed categories: Billing, Technical Support, Product Inquiry, Account Access.

Input: "I was charged twice for order #48291 and need a refund immediately."
Output: Sentiment: Negative | Category: Billing | Urgency: High

Input: "Where can I view my billing history from last year?"
Output: Sentiment: Neutral | Category: Account Access | Urgency: Low

Input: "Do you offer educational discounts for university faculty members?"
Output: Sentiment: Neutral | Category: Product Inquiry | Urgency: Low

Input: "The mobile app crashes every time I tap 'Confirm Payment' on iOS 17."
Output: Sentiment: Negative | Category: Technical Support | Urgency: High

Input: "The blue jacket arrived, but the zipper gets stuck halfway up."
Output:
```

**Verification Check:**
- Gemini completes the final turn following the exact delimiter pattern:
  `Sentiment: Negative | Category: Product Inquiry | Urgency: Medium` (or Low/High).
- No extraneous explanations or conversational greetings are generated.

### Task 4: Grounded Reference Text and Fallback Verification

**Prompt:**
```markdown
You are an IT Compliance Assistant. Answer the employee query using ONLY the policy excerpt provided below.

Rules:
1. Base your answer strictly on the provided policy text.
2. If the excerpt does not contain the answer, reply exactly:
   "This information is not covered in the provided policy excerpt."
3. Cite the specific section number and rule title that supports your answer.
4. Do not assume, extrapolate, or use outside IT knowledge.

Policy Excerpt:
Novatech Solutions Remote Work and Equipment Security Policy (Section 4):
1. Hardware Provisioning: Full-time employees receive one standard corporate laptop and one external monitor. Peripherals (keyboard, mouse, headset) are reimbursed up to $250 with manager approval upon hire.
2. Network Security: All work-related network traffic must route through the corporate VPN when connecting from home or public networks. Split-tunneling is strictly prohibited.
3. Password and Authentication: Passwords must contain a minimum of 14 characters, including at least one number and one symbol, and must be updated every 180 days. Multi-factor authentication (MFA) via a corporate authenticator app or hardware key is mandatory for all system access.
4. Software Installation: Employees must not install unapproved third-party software, browser extensions, or cloud storage tools on company devices. All software requests must be submitted through the IT Service Catalog.
5. Lost or Stolen Equipment: Any lost or stolen corporate hardware must be reported to the IT Security Incident Response team within 2 hours of discovery via the emergency hotline at 800-555-0199.

Question: "Can I use my personal Dropbox account to back up project design files?"
```

**Verification Check (Grounding with Citation):**
- Answer states that employees must not install or use unapproved cloud storage tools, referencing Rule 4 (Software Installation).

**Fallback Test (Missing Information):**
- Query: “What is the reimbursement allowance for an ergonomic standing desk?”
- Verification Check: Gemini responds with the exact fallback phrase:
  `This information is not covered in the provided policy excerpt.`

### Task 5: Iterative Refinement Verification

**Refined Prompt:**
```markdown
Role: You are an incident response lead writing an executive update.
Task: Summarize the database outage incident described below.
Context: Audience is non-technical executive leadership requiring immediate risk visibility.
Constraints:
- Provide exactly three bullet points: Root Cause, Customer Impact, and Corrective Action.
- Bold each lead-in label.
- Keep each bullet under 25 words.
- Do not use database internal jargon (such as WAL buffer, fsync, or vacuum).
- Do not include conversational introductory or concluding text.

Incident Log:
"At 03:14 UTC, primary database node db-prod-01 experienced transaction log exhaustion due to unvacuumed write-ahead logs during a batch update. Replication lag on read replicas exceeded threshold, causing customer-facing checkout API timeouts for 22 minutes affecting 1,420 checkout attempts. Failover to db-prod-02 completed at 03:36 UTC. Disk auto-scaling policy has been applied to prevent future write stalls."
```

**Verification Check:**
- Output contains exactly three bullet points with bold lead-ins (**Root Cause**, **Customer Impact**, **Corrective Action**).
- No unexplained database jargon is used.
- Tone is professional, executive-level, and under the specified length constraints.

## Built With

- Google Gemini (Gemini 1.5 Flash / Gemini 2.0 Flash in web console `gemini.google.com`), tested March 2025.
- Compatible with all standard Google accounts (personal and Google Workspace).
- Unverified: Specific commercial enterprise Gemini extensions or custom system instructions configurations that may differ by organization tenant.
