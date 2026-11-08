# Engineering Effective Prompts in Gemini

## Time Required

30 minutes

## Overview

In this lab, you will apply structured prompt engineering techniques in Google Gemini to produce consistent, high-quality generative AI responses. You will build prompts using core components, enforce schema-driven formatting, implement few-shot demonstrations, ground responses in reference documentation to prevent hallucinations, and iteratively diagnose and refine defective prompts.

### You learn how to:
- Construct structured prompts using role, task, context, and constraints.
- Enforce structured output formatting using direct action verbs and schemas.
- Guide tone and classification consistency using few-shot demonstrations.
- Ground prompts in reference text with fallback guardrails to prevent hallucinations.
- Apply an iterative refinement workflow to diagnose and resolve prompt defects.

## Scenario

Your engineering and operations teams are adopting Google Gemini to accelerate documentation, customer inquiry classification, and incident reviews. However, team members currently rely on casual, unstructured queries that produce verbose, inconsistent, and occasionally hallucinated answers. You have been tasked with designing and validating structured prompt patterns that transform unpredictable model output into dependable, production-ready deliverables.

## Lab Instructions

### Task 1: Access Gemini and construct a four-element prompt

In this task, you sign in to Google Gemini, test an unstructured casual query, and contrast it with an engineered prompt incorporating the four core elements: role, task, context, and constraints.

1. Open a new browser tab and navigate to [https://gemini.google.com/](https://gemini.google.com/).

2. Sign in with your personal Google account or organization workspace credentials.

3. Locate the prompt input field at the bottom of the Gemini interface.

4. Submit the following casual prompt to observe how Gemini responds to an unconstrained request:

```text
Explain the difference between containerization and virtual machines.
```

5. Review the generated response. Notice that while the information is generally correct, the response is lengthy, lacks context about your deployment target, and requires significant reading time.

6. Submit a structured prompt that explicitly defines Role, Task, Context, and Constraints:

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

7. Compare the two responses.

> [!NOTE]
> The structured prompt immediately guides the model to adopt the appropriate altitude, focuses directly on your specific migration context, and eliminates unwanted conversational filler.

### Task 2: Direct output formatting with explicit schemas

In this task, you use direct action verbs and strict schema constraints to generate structured outputs formatted as Markdown tables and machine-readable JSON.

1. Start a fresh conversation by clicking **New chat** in the left navigation menu.

> [!TIP]
> Starting a fresh chat clears conversational memory and prevents previous context from altering new test runs.

2. Submit the following prompt directing Gemini to produce a comparison formatted strictly as a Markdown table:

```markdown
Task: Compare relational databases (PostgreSQL) and document stores (MongoDB) for e-commerce product catalogs.
Output Format: Present the comparison exclusively as a Markdown table with the following four columns:
| Dimension | Relational (PostgreSQL) | Document Store (MongoDB) | Tradeoff Summary |
Row requirements:
- Include exactly four rows: Schema Flexibility, Query Complexity, ACID Compliance, Horizontal Scalability.
- Keep each cell concise (under 20 words).
- Do not output any prose, commentary, or markdown outside the table.
```

3. Confirm that the model output is restricted entirely to the requested table without conversational preambles such as “Here is the comparison table you requested.”

4. Click **New chat** to start another clean conversation session.

5. Submit the following prompt enforcing a strict JSON output schema with negative constraints:

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

6. Inspect the resulting JSON object to verify that each key conforms to the specified data types and value options.

> [!IMPORTANT]
> Negative constraints such as “Do not include markdown code fences or conversational text” prevent model chatter when feeding output into automated downstream pipelines.

### Task 3: Guide classification using few-shot demonstrations

In this task, you build a few-shot prompt that teaches Gemini a custom classification scheme and output delimiter structure by providing balanced input and output demonstrations.

1. Click **New chat** to start a clean session.

2. Review the customer inquiries provided in `starter/customer-inquiries.txt`.

3. Enter the following few-shot prompt containing demonstrations with consistent delimiters (`Input:` and `Output:`) across multiple categories:

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

4. Submit the prompt and evaluate the completion provided for the final input turn.

> [!NOTE]
> Gemini automatically matches the pipe-delimited syntax and assigns appropriate values: `Sentiment: Negative | Category: Product Inquiry | Urgency: Medium` (or Low/High) without needing multi-paragraph instructions.

5. Submit a follow-up test in the same session by sending another input line:

```text
Input: "I forgot my password and the reset link emailed to me has already expired."
Output:
```

6. Verify that the model maintains the exact delimiter pattern and categorizes the inquiry as `Account Access`.

### Task 4: Ground prompts in source text to eliminate hallucinations

In this task, you ground Gemini in reference documentation and add defensive guardrails to prevent the model from fabricating policies or details.

1. Click **New chat** to open a new conversation.

2. Review the reference policy text found in `starter/it-policy-excerpt.txt`.

3. Submit the following grounded prompt containing reference text, strict negative constraints, and an explicit fallback directive:

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

4. Verify that the response accurately cites Rule 4 (Software Installation) and explains that unapproved cloud storage tools are prohibited.

5. In the same chat session, ask a question that is deliberately omitted from the policy document:

```text
Question: "What is the reimbursement allowance for an ergonomic standing desk?"
```

6. Observe the model response.

> [!WARNING]
> Without the explicit fallback directive (“reply exactly: This information is not covered…”), models frequently extrapolate standard commercial allowances or invent plausible budget amounts.

7. Confirm that Gemini returned the exact fallback sentence: `This information is not covered in the provided policy excerpt.`

### Task 5: Diagnose and refine a defective prompt

In this task, you diagnose common prompt defects, such as excessive verbosity, missing audience context, and internal jargon, and apply the iterative refinement loop to produce a polished executive summary.

1. Click **New chat** to start a clean session.

2. Submit the following initial prompt based on an incident log:

```text
Summarize this incident for management:
At 03:14 UTC, primary database node db-prod-01 experienced transaction log exhaustion due to unvacuumed write-ahead logs during a batch update. Replication lag on read replicas exceeded threshold, causing customer-facing checkout API timeouts for 22 minutes affecting 1,420 checkout attempts. Failover to db-prod-02 completed at 03:36 UTC. Disk auto-scaling policy has been applied to prevent future write stalls.
```

3. Evaluate the output against the four defect types taught in Chapter 1:
   - **Verbosity:** Did the model generate excess prose or unsolicited operational suggestions?
   - **Audience tone:** Did the summary explain or remove low-level database jargon such as “unvacuumed write-ahead logs” for executive readers?
   - **Structure:** Is the output scannable with consistent takeaway headings, or a dense paragraph?

4. Submit the following refined prompt that addresses each identified defect through explicit role definition, audience context, and rigid constraints:

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

5. Confirm that the new output delivers three focused, bolded bullets with non-technical business language suitable for executive decision-makers.

### Bonus Task 6: Build a reusable prompt template with variables

In this bonus task, you construct a parameterized prompt template that teammates can reuse across varied operational incidents.

1. Write a reusable template in your text editor that isolates variable data from static rules using bracketed placeholders such as `{ROLE}`, `{AUDIENCE}`, `{SOURCE_DATA}`, and `{FORMAT}`.

2. Replace the placeholders with a new operational scenario, such as a scheduled maintenance notification or security advisory:

```markdown
Role: You are an internal IT communications specialist.
Task: Draft a maintenance advisory based on the technical details provided in {MAINTENANCE_DETAILS}.
Context: Distributed to all corporate employees who rely on the enterprise VPN and email services.
Constraints:
- Heading: "Scheduled System Maintenance Advisory".
- Provide three sections: Maintenance Window, Expected Service Impact, and Employee Action Required.
- Total length under 120 words.
- Tone must be professional and reassuring.

Maintenance Details:
"The core authentication cluster will undergo firmware patching this Saturday from 02:00 to 04:00 EST. Single sign-on and corporate VPN will be unavailable during this 2-hour window. Inbound emails will queue and deliver once systems resume."
```

3. Submit the completed template in Gemini and verify that the generated advisory adheres strictly to the constraints.

## Congratulations!

In this lab, you have:
- Constructed structured prompts using role, task, context, and constraints.
- Enforced structured output formatting using direct action verbs and schemas.
- Guided tone and classification consistency using few-shot demonstrations.
- Grounded prompts in reference text with fallback guardrails to prevent hallucinations.
- Applied an iterative refinement workflow to diagnose and resolve prompt defects.
