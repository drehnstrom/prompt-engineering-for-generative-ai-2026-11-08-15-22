<!-- course-title: Prompt Engineering for Generative AI -->
<!-- course-theme: roi-alt-theme -->

<!-- layout: title -->
![ROI Logo](images/roi-logo-with-name.png)

Prompt Engineering for Generative AI

# Chapter 1: Prompt Engineering Essentials

---

# Chapter 1: Objectives

- Apply structured prompt components, context framing, and iterative refinement techniques in Gemini
- Define prompt engineering and explain how structured prompts improve generative AI responses
- Identify and combine core prompt components including role, task, context, and constraints
- Specify direct instructions and explicit output formats for predictable model outputs
- Provide few-shot demonstrations to guide tone, style, and structure
- Eliminate ambiguous wording and reduce hallucinations with grounded prompts
- Apply an iterative refinement workflow to evaluate and troubleshoot model responses
- Construct and test structured prompts hands-on using Google Gemini

---

<!-- layout: navigation -->
# Chapter 1

- **Introduction to Prompt Engineering**
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- Direct Instructions and Formatting Output
- Few-Shot Prompting with Examples
- Avoiding Ambiguity and Mitigating Hallucinations
- Iterative Prompt Refinement
- Lab: Engineering Effective Prompts in Gemini

---

# What Is Prompt Engineering?

- Prompt engineering is the practice of structuring inputs to guide generative AI models toward accurate, useful results
- Models generate answers probabilistically rather than retrieving static database records
- Unstructured inputs leave intent open to interpretation, producing generic or off-target answers
- Systematic prompt design turns generative models into dependable tools for professional tasks

> [!NOTE]
> Large language models predict the next sequence of words based on patterns in training data and the exact context provided in your prompt.

---

<!-- layout: 2-column -->
# Casual Queries vs Engineered Prompts

### Casual Querying
- Treats the model like an informal search engine
- Relies on assumed context and unstated goals
- Produces generic, unpredictable, or verbose replies
- Requires repetitive follow-up prompts to fix defects

### Engineered Prompting
- Explicitly establishes role, scope, and rules
- Supplies necessary domain context and source data
- Delivers structured, consistent, and ready-to-use output
- Creates repeatable prompt patterns for ongoing workflows

---

# How Generative Models Process Prompts

- Models generate text token by token based on statistical probabilities
- Your prompt acts as conditioning context that narrows the probability distribution
  - Clear parameters steer the model toward specific, domain-relevant language
  - Missing parameters force the model to fill gaps with generic assumptions
- Effective prompt engineering controls three critical dimensions:
  - Focus: The exact problem, question, or domain to address
  - Scope: Boundaries, length limits, and what to exclude
  - Presentation: The exact structure (lists, tables, JSON, or paragraphs)

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- **Core Elements of a Prompt: Role, Task, Context, and Constraints**
- Direct Instructions and Formatting Output
- Few-Shot Prompting with Examples
- Avoiding Ambiguity and Mitigating Hallucinations
- Iterative Prompt Refinement
- Lab: Engineering Effective Prompts in Gemini

---

<!-- layout: card-layout -->
# The Four Core Prompt Elements

### Role
- Assign a specific persona, altitude, and professional background.
- Framing the model as an expert sets expectations for vocabulary and depth.

### Task
- Define the primary action and core deliverable using explicit verbs.
- Focus on one central goal per prompt to prevent fragmented responses.

### Context
- Supply background facts, target audience, and business environment.
- Context grounds the model in your scenario rather than general assumptions.

### Constraints
- Establish length limits, forbidden phrases, tone, and formatting rules.
- Constraints eliminate unwanted filler and keep answers directly actionable.

---

# Anatomy of a Structured Prompt

- Combining the four core elements eliminates guesswork and sets clear boundaries

```text
Role:        You are a senior data engineer mentoring junior team members.
Task:        Explain the difference between batch and stream processing.
Context:     Our team builds analytics pipelines for retail inventory tracking.
Constraints: Provide three concise bullet points. Include one retail
             example for each. Limit total response to under 150 words.
```

- The model immediately adopts the intended altitude, industry context, and format
- Without these four components, the model must guess audience seniority and technical depth

---

<!-- layout: 2-column -->
# Impact of Missing Prompt Elements

### Incomplete Prompt
- Prompt: “Explain how to optimize database performance.”
- Result: Generic advice spanning multiple database systems
- Tone: Broad introductory overview lacking technical depth
- Deliverable: Several pages of unstructured prose requiring heavy manual editing

### Complete Structured Prompt
- Prompt: Specifies PostgreSQL, high-throughput queries, role, and a Markdown table
- Result: Five targeted indexing and caching recommendations
- Tone: Senior database engineer code review
- Deliverable: Clear table comparing implementation effort, latency impact, and trade-offs

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- **Direct Instructions and Formatting Output**
- Few-Shot Prompting with Examples
- Avoiding Ambiguity and Mitigating Hallucinations
- Iterative Prompt Refinement
- Lab: Engineering Effective Prompts in Gemini

---

# Direct Instructions and Action Verbs

- Generative models respond best to imperative, directive language
  - Conversational requests like “Could you take a look at this?” add ambiguity
  - Direct directives like “Analyze this clause for termination liability” set clear focus
- Replace vague requests with precise action verbs
  - Use *Extract*, *Classify*, *Summarize*, *Compare*, *Audit*, or *Synthesize*
  - Avoid ambiguous verbs such as *Process*, *Handle*, or *Review*
- Order multi-step instructions chronologically so the model follows a logical progression

> [!TIP]
> Conversational filler such as “please” or “thank you” consumes context space without providing semantic guidance. Keep instructions direct and assertive.

---

<!-- layout: 3-column -->
# Output Formatting Strategies

### Markdown Tables
- Ideal for comparisons and feature trade-offs
- Specify exact column headers (such as Feature, Benefit, Risk)
- Keep complex comparisons readable at a glance

### Structured Data Schemas
- Essential for feeding output into automated downstream pipelines
- Specify JSON, YAML, or CSV with explicit keys
- Instruct the model to omit conversational Markdown text

### Scannable Bullet Lists
- Ideal for executive briefs and incident reviews
- Constrain bullet counts and sentence lengths
- Mandate bold lead-in terms for quick scanning

---

# Enforcing Output Structure with Schemas

- Supplying an explicit schema produces predictable, machine-readable output

```markdown
Task: Extract customer sentiment and primary issue from the support ticket.
Output Format: Return valid JSON matching this schema:
{
  "customer_sentiment": "Positive" | "Neutral" | "Negative",
  "urgency_level": "Low" | "Medium" | "High",
  "primary_issue": "string (under 10 words)",
  "recommended_action": "string (one sentence)"
}
Do not include any conversational explanation or text outside the JSON block.
```

- Explicit schema keys eliminate field ambiguity during downstream parsing
- Negative constraints (“Do not include conversational explanation”) suppress introductory chatter

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- Direct Instructions and Formatting Output
- **Few-Shot Prompting with Examples**
- Avoiding Ambiguity and Mitigating Hallucinations
- Iterative Prompt Refinement
- Lab: Engineering Effective Prompts in Gemini

---

<!-- layout: 2-column -->
# Zero-Shot vs Few-Shot Prompting

### Zero-Shot Prompting
- Relies solely on instructions without example input-output pairs
- Effective for common tasks and straightforward summarization
- Depends entirely on pre-trained model associations
- May require extra conversational turns to correct formatting drift

### Few-Shot Prompting
- Provides two to five concrete demonstrations inside the prompt
- Teaches custom formatting, subtle tone, and taxonomies by example
- Substantially improves output consistency across repeated runs
- Clarifies complex requirements faster than lengthy prose instructions

---

# Anatomy of Few-Shot Demonstrations

- Format demonstrations using consistent labels and clear delimiters

```text
Classify customer review tone and identify the affected product category.

Input: "The checkout button froze and charged my card twice!"
Output: Sentiment: Negative | Category: Billing | Urgency: Immediate

Input: "Arrived two days early, packaging was completely intact."
Output: Sentiment: Positive | Category: Shipping | Urgency: Standard

Input: "Sound quality is crisp, but headband feels tight after an hour."
Output: Sentiment: Neutral | Category: Hardware Ergonomics | Urgency: Standard

Input: "Where can I find the updated PDF user manual for model X-200?"
Output:
```

- The model infers taxonomy, structure, and brevity directly from the demonstrations
- The consistent pattern acts as an unwritten contract for the final prompt turn

---

# Few-Shot Best Practices and Pitfalls

- Choose quality over volume: two to four clean examples outperform ten noisy ones
- Maintain strict consistency across every demonstration
  - Use identical delimiters (such as `Input:` and `Output:`) throughout
  - Keep label naming, casing, and punctuation uniform across all examples
- Include balanced coverage across categories and boundary conditions
  - Provide both positive and negative cases to avoid biasing the model toward one label
  - Include borderline examples to illustrate how edge cases should be handled

> [!WARNING]
> If all your few-shot examples share an unintended pattern (such as every output starting with the same word), the model will replicate that pattern regardless of your instructions.

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- Direct Instructions and Formatting Output
- Few-Shot Prompting with Examples
- **Avoiding Ambiguity and Mitigating Hallucinations**
- Iterative Prompt Refinement
- Lab: Engineering Effective Prompts in Gemini

---

<!-- layout: 2-column -->
# Understanding and Mitigating Hallucinations

### Why Models Hallucinate
- Models generate statistically likely completions rather than verified facts
- When knowledge is missing, models fabricate convincing details to fill gaps
- Open-ended prompts invite ungrounded extrapolation
- Models rarely admit uncertainty unless explicitly instructed to do so

### How Grounding Mitigates Hallucination
- Restricts the model’s knowledge boundaries to provided source text
- Grants explicit permission to state when information is missing
- Imposes negative constraints against guessing or external assumptions
- Enables human reviewers to verify every claim against the reference text

---

# Grounding Prompts with Reference Text

- Direct the model to answer exclusively from the provided source context

```markdown
You are an HR policy assistant. Answer the employee question using ONLY
the handbook excerpt provided below.

Rules:
1. Base your answer strictly on the provided handbook text.
2. If the excerpt does not contain the answer, reply exactly:
   "This information is not covered in the provided handbook excerpt."
3. Do not assume, extrapolate, or reference outside employment policies.

Handbook Excerpt:
"Full-time employees receive 15 days of paid vacation annually, accrued
monthly at 1.25 days. Vacation days do not roll over past March 31."

Question: "Can I roll over unused vacation days into November next year?"
```

- Supplying an explicit fallback phrase prevents the model from inventing plausible answers
- Bounding context eliminates liability from fabricated company policy interpretations

---

<!-- layout: card-layout -->
# Defensive Prompting Guardrails

### Strict Negative Constraints
- Explicitly forbid assumptions, extrapolation, and unauthorized formatting styles.
- State what the model must never do (for example: “Do not invent statistics or employee names”).

### Verifiable Citations
- Require the model to quote exact supporting sentences from the source context.
- Directly cited evidence makes factual hallucinations easy for reviewers to detect.

### Staged Reasoning Steps
- Direct the model to extract key facts before producing its final evaluation.
- Separating evidence extraction from conclusions prevents premature assumptions.

### Explicit Fallback Directives
- Instruct the model to return a standardized “information not found” phrase when unsure.
- Eliminates the pressure for the model to fill informational gaps with speculation.

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- Direct Instructions and Formatting Output
- Few-Shot Prompting with Examples
- Avoiding Ambiguity and Mitigating Hallucinations
- **Iterative Prompt Refinement**
- Lab: Engineering Effective Prompts in Gemini

---

# The Prompt Refinement Loop

- Prompt engineering is an iterative testing discipline, not a one-time guess
- Step 1: Draft initial prompt with role, task, context, and clear constraints
- Step 2: Test the prompt against representative sample inputs in Google Gemini
- Step 3: Evaluate output against success criteria (accuracy, tone, structure, and brevity)
- Step 4: Diagnose defects and adjust specific prompt components
  - If output wanders: strengthen negative constraints and narrow task scope
  - If facts are fabricated: add reference text and strict fallback instructions
  - If formatting varies: introduce an explicit schema or few-shot examples
- Step 5: Retest against sample inputs and compare against baseline results

---

<!-- layout: card-layout -->
# Diagnosing and Fixing Common Defects

### Output Is Too Verbose
- Defect: Model includes conversational filler, unsolicited advice, or repetitive explanations.
- Remedy: Add negative constraints: “Begin response immediately. Maximum 3 bullet points.”

### Formatting Is Inconsistent
- Defect: Model alternates between Markdown tables, paragraphs, and unordered lists.
- Remedy: Provide an explicit JSON schema or two concrete few-shot demonstrations.

### Output Invents Facts
- Defect: Model hallucinates dates, names, product features, or unsupported metrics.
- Remedy: Supply closed-domain reference text and mandate a strict “not found” fallback phrase.

### Tone Is Inappropriate
- Defect: Model speaks at the wrong altitude or uses casual jargon for executive readers.
- Remedy: Define the persona role and explicitly specify target audience expertise.

---

<!-- layout: 2-column -->
# Managing Prompts as Technical Assets

### Testing Discipline
- Test prompts across multiple diverse inputs, including challenging edge cases
- Track model version and date, as provider updates can shift model behavior
- Build evaluation checklists to evaluate responses consistently across team members
- Document known failure modes and the specific constraints added to resolve them

### Versioning and Sharing
- Store production prompt templates in version-controlled repositories
- Separate static prompt templates from dynamic runtime user variables
- Share validated prompt libraries across teams to standardize organizational outputs
- Treat prompt refinement with the same rigor as application code reviews

---

<!-- layout: navigation -->
# Chapter 1

- Introduction to Prompt Engineering
- Core Elements of a Prompt: Role, Task, Context, and Constraints
- Direct Instructions and Formatting Output
- Few-Shot Prompting with Examples
- Avoiding Ambiguity and Mitigating Hallucinations
- Iterative Prompt Refinement
- **Lab: Engineering Effective Prompts in Gemini**

---

# Engineering Effective Prompts in Gemini

Lab link: TODO

---

# What You Learned

- Applied structured prompt components, context framing, and iterative refinement techniques in Gemini
- Defined prompt engineering and explained how structured prompts improve generative AI responses
- Identified and combined core prompt components including role, task, context, and constraints
- Specified direct instructions and explicit output formats for predictable model outputs
- Provided few-shot demonstrations to guide tone, style, and structure
- Eliminated ambiguous wording and reduced hallucinations with grounded prompts
- Applied an iterative refinement workflow to evaluate and troubleshoot model responses
- Constructed and tested structured prompts hands-on using Google Gemini

---

# Quiz 1 of 3

**Which prompt component is primarily responsible for establishing boundaries, length limits, and negative rules?**

- A. Role
- B. Context
- C. Task
- D. Constraints

---

# Quiz 1: Answer

**Which prompt component is primarily responsible for establishing boundaries, length limits, and negative rules?**

**Correct: D.** Constraints

- Constraints establish explicit boundaries such as length limits, forbidden phrases, and formatting rules
- Role defines the persona, altitude, and professional perspective of the model
- Task specifies the core action verb and primary objective to accomplish
- Context supplies necessary domain background, user scenarios, and reference data

---

# Quiz 2 of 3

**When should a prompt designer use few-shot prompting instead of zero-shot prompting?**

- A. When the model needs to browse external websites for real-time stock prices
- B. When output requires subtle stylistic nuances, specialized classification labels, or consistent custom syntax
- C. When the model needs to run Python code in a local operating system terminal
- D. When the prompt contains sensitive credentials that must not be stored in training data

---

# Quiz 2: Answer

**When should a prompt designer use few-shot prompting instead of zero-shot prompting?**

**Correct: B.** When output requires subtle stylistic nuances, specialized classification labels, or consistent custom syntax

- Few-shot examples teach formatting structure, classification schemes, and tone by demonstration
- Zero-shot prompting works well for standard general-knowledge tasks and straightforward summarization
- Few-shot examples do not provide real-time web browsing or local terminal execution capabilities
- Demonstrations guide probabilistic pattern completion without needing lengthy abstract rules

---

<!-- layout: 2-column -->
# Quiz 3 of 3: Discussion

### Prompt
Your team plans to use Google Gemini to summarize internal technical post-mortem reports for executive leadership. Early trial prompts produced long, overly detailed paragraphs full of engineering jargon that executives found unhelpful.

### Discuss
- Which prompt elements would you adjust first to fix altitude and length?
- How would you structure constraints and formatting to guarantee concise executive summaries?
- What few-shot or grounding strategies would prevent the model from misrepresenting root causes?

---

<!-- layout: 2-column -->
# Quiz 3: Discussion Points

**Your team plans to use Google Gemini to summarize internal technical post-mortem reports for executive leadership.**

### Strong Answers Mention
- Defining an explicit Role (senior engineering liaison) and Audience Context (non-technical executive leadership)
- Imposing strict Constraints on length (such as three bullet points, under 150 words total) and forbidding unexplained jargon
- Supplying an explicit output structure with bold takeaway headers (Impact, Root Cause, Mitigation)
- Grounding the summary strictly in the post-mortem text with an explicit rule against speculating on unstated causes

### Watch For
- Relying on vague instructions like “make it concise and executive-ready” without explicit constraints
- Skipping grounding rules, allowing the model to hallucinate or assume system root causes
- Adding more post-mortem data without adjusting formatting schemas or audience context

---

# Questions and Answers

Questions?
