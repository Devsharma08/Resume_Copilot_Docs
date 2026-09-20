### this document will be about understanding GADS and how this can be used and improve 

### GADS

 **It is an AI resume screener where the AI can suggest things, but it is not allowed to make unchecked decisions.**

Think of it as a **junior recruiter + fact checker + rule engine + human escalation system**.

---

# 1. First, what problem is GADS trying to solve?

A normal AI resume screener might do this:

**Resume → AI reads it → AI gives score → shortlist**

The problem is that an LLM can:

* misunderstand a resume
* believe exaggerated claims
* overlook requirements
* make inconsistent decisions
* give a high score to a candidate based on weak evidence


GADS adds several layers of control:

```text
Resume
   ↓
AI extracts information
   ↓
Check how trustworthy the information is
   ↓
Apply fixed hiring rules
   ↓
Calculate controlled candidate-match score
   ↓
Certain? → automate
Uncertain? → human review
```

So the philosophy is:

> **AI can interpret information, but rules and evidence control the final decision.**

---

# 2. Stage 1 — LLM Extraction

### In simple words:

The AI first **reads the resume and converts it into structured information**.

Suppose a resume says:

> "Developed scalable React applications for 2 years and worked with AWS."

The LLM might convert that into:

```json
{
  "skills": ["React", "AWS"],
  "experience": {
    "React": "2 years"
  }
}
```

Instead of treating the whole resume as a giant block of text, GADS turns it into **specific claims**.

### Think of it like this

A human recruiter reads:

> "I am an excellent React developer with strong cloud expertise."

The system asks:

> What exactly is being claimed here?

It extracts:

* React → claimed skill
* cloud expertise → claimed skill
* potentially 2 years → claimed experience

At this stage, **the AI is only extracting claims.**

It is **not deciding whether those claims are true**.

---

# 3. Stage 2 — Evidence Provenance Scoring

This is one of the most important ideas.

### "Provenance" simply means:

> **Where did this information come from, and how trustworthy is it?**

Imagine two candidates.

### Candidate A

Resume:

> "React Developer — 3 years"

No work samples, no employment record, no supporting information.

### Candidate B

Resume:

> "React Developer — 3 years"

And there is supporting evidence such as:

* verified employment history
* GitHub projects
* portfolio
* certificate
* previous application records

Both candidates claim:

```text
React = 3 years
```

But GADS doesn't necessarily treat those claims equally.

It asks:

> **How strong is the evidence behind this claim?**

That's the idea behind:

### `E_prov = Evidence Provenance`

You can think of it as:

```text
Strong evidence → higher trust
Weak evidence   → lower trust
No evidence     → very low trust
```

So instead of:

```text
"Candidate says React → therefore React = true"
```

you get:

```text
"Candidate says React
        ↓
What is the evidence?
        ↓
How trustworthy is that evidence?"
```

---

# 4. Stage 3 — Deterministic OPA Policy Adjudication

This sounds complicated but is actually very simple.

**OPA = Open Policy Agent**

Think of OPA as a **strict rule checker**.

The LLM is flexible.

OPA is not.

For example, company rules might say:

```text
Degree required = Yes
Minimum experience = 2 years
Python required = Yes
Location restriction = No
```

OPA evaluates the candidate against those rules.

For example:

```text
Candidate:
Degree: Yes
Experience: 3 years
Python: Yes
Location: Yes

OPA:
PASS
```

Another candidate:

```text
Candidate:
Degree: No
Experience: 5 years
Python: Yes

OPA:
FAIL
```

The important idea is:

> **The LLM does not get to change the hiring rules.**

The rules are explicitly written in something like **Rego**, OPA's policy language.

So:

```text
LLM → "I think this candidate looks good."

OPA → "Does this candidate satisfy the actual rules?"
```

This separation is extremely important.

---

# 5. What does `P_hard` mean?

`P_hard` is basically a **hard yes/no gate**.

Imagine the company says:

> "A Bachelor's degree is mandatory."

Then:

```text
Degree present → P_hard = TRUE
Degree missing → P_hard = FALSE
```

Even if the AI thinks:

> "Wow, this candidate has amazing projects!"

the hard rule still matters.

So conceptually:

```text
AI score = 95

BUT

Mandatory requirement = FAIL

→ candidate cannot simply bypass the requirement
```

That's what the **hard boolean gate** is protecting against.

---

# 6. Stage 4 — Uncertainty-Based Human Routing

This is another very important idea.

AI shouldn't pretend to know everything.

Suppose a resume says:

> "Worked extensively with AWS."

What does that mean?

Maybe:

* 3 years professionally
* 3 months
* one college project
* watched an AWS course

The AI might not be sure.

Instead of inventing certainty, GADS says:

> **"I'm not confident enough. Send this case to a human."**

So the system creates something like:

```text
High confidence
      ↓
Automated processing

Low confidence
      ↓
Human review
```

This is what the **uncertainty-based human routing** means.

It's basically:

> **Don't automate decisions when the system knows that the evidence is unclear.**

---

# 7. Now the interesting part — Governed Closeness Metric `M_G`

This is the system's **overall candidate-to-job matching score**.

But it's not just:

```text
AI similarity = 87%
```

Instead, it considers multiple things.

You mentioned:

```text
M_G =
Evidence Provenance
+ Semantic Alignment
- Uncertainty Penalty
```

with a hard policy gate.

Let's translate each part.

---

## `S_sem` — Semantic Alignment

This means:

> **How similar is the candidate's actual experience to what the job requires?**

Example:

Job:

```text
React
Node.js
MongoDB
AWS
```

Candidate:

```text
React
Node.js
MongoDB
AWS
```

High semantic alignment.

Another candidate:

```text
Java
Spring
Oracle
```

Lower alignment.

This can use embeddings/LLMs to understand that:

```text
"React developer"

and

"Built frontend applications using React.js"
```

are semantically related even though the wording is different.

---

# 8. `E_prov` — Evidence Provenance

We already discussed this.

It asks:

> **How well supported are the candidate's claims?**

For example:

```text
Claim: "3 years React"

Evidence:
Verified employment → strong
Portfolio → medium/strong
Resume only → weaker
```

So two candidates with identical semantic matches might get different scores because their evidence quality differs.

---

# 9. `U_unc` — Epistemic Uncertainty

This sounds scary, but the idea is simple.

### Epistemic uncertainty means:

> **The system isn't sure because it doesn't have enough knowledge/evidence.**

For example:

```text
Candidate says:
"Expert in Kubernetes"

Evidence:
No projects
No employment history
No technical assessment
```

The AI shouldn't confidently assume:

```text
Kubernetes = expert
```

So uncertainty increases.

Conceptually:

```text
More uncertainty → bigger penalty
Less uncertainty → smaller penalty
```

---

# 10. Putting `M_G` together

Imagine:

```text
Candidate A

Semantic alignment:     90
Evidence quality:       85
Uncertainty:             5
Hard requirements:    PASS
```

Candidate B:

```text
Semantic alignment:     92
Evidence quality:       40
Uncertainty:            35
Hard requirements:    PASS
```

A normal AI system might simply say:

```text
B = 92
A = 90
```

GADS says:

> "Wait. Candidate B looks slightly more similar, but the evidence is much weaker and uncertainty is much higher."

So the final governed score can reflect that.

The important concept isn't the exact mathematical formula; it's the **design philosophy**:

```text
Similarity alone is NOT enough.
```

You want:

```text
Match quality
+
Evidence quality
+
Confidence
+
Policy compliance
```

---

# 11. Why the hard Boolean `P_hard` matters

Imagine:

```text
M_G = 94
```

Sounds great.

But the job requires:

```text
Security certification = mandatory
```

Candidate doesn't have it.

Then:

```text
P_hard = FALSE
```

So the system shouldn't simply say:

> "94/100, hire this person."

Instead:

```text
High similarity
BUT
mandatory condition failed
```

This is an important safety mechanism.

---

# 12. PBPQ — Policy-Bounded Priority Queue

This is probably the most confusing part.

Forget the fancy name for a moment.

It's basically:

> **A continuously updated shortlist that only allows candidates that obey the rules.**

Suppose 10,000 resumes are coming into the system.

You only want the **top 20 candidates**.

A basic system might do:

```text
Process all 10,000
↓
Sort them
↓
Take top 20
```

PBPQ is designed to work more like a **live priority queue**.

As candidates arrive:

```text
Candidate #1 → evaluate
Candidate #2 → evaluate
Candidate #3 → evaluate
...
```

The system continuously maintains:

```text
Current Top 20
```

rather than waiting until everything is processed.

That's the **streaming top-K** part.

---

# 13. What is the "Role Model" benchmark?

This isn't a human employee model.

Think of it as a **reference standard for the role**.

Suppose you're hiring a Senior React Developer.

The system establishes a benchmark representing what a strong match looks like:

```text
React       █████████
Node.js     ████████
TypeScript  ███████
Testing     ██████
AWS         █████
```

Candidate scores are compared against this evolving benchmark.

The point is to avoid the ranking being determined purely by whatever candidates happen to appear first.

---

# 14. What does "defends against adversarial corruption" mean?

This means:

> **What happens if somebody intentionally tries to game the system?**

Imagine someone modifies a resume to contain hundreds of keywords:

```text
React React React React
AWS AWS AWS AWS
Python Python Python
```

A naive ATS might think:

> "Wow! This candidate matches everything!"

GADS tries to prevent that by looking at:

```text
Actual evidence
+
semantic relevance
+
policy compliance
+
uncertainty
```

So keyword stuffing shouldn't automatically produce a high ranking.

That's the idea behind **adversarial corruption defense**.

---

# 15. Database Schema

Now we get to the engineering side.

The system uses something like:

```text
FastAPI
    ↓
PostgreSQL
    ↓
OPA / Rego
```

You can think of it like this:

### FastAPI

The backend.

```text
Frontend
   ↓
FastAPI
```

It handles APIs such as:

```text
/upload-resume
/analyze-candidate
/get-score
/get-audit-log
```

---

### PostgreSQL

Stores everything.

Instead of keeping:

```text
Candidate → score = 84
```

it can store much more detail.

---

# 16. `EvidenceLedger`

Think of this as a **receipt book**.

For every important claim, it records:

```text
Claim:
"Candidate has 3 years React experience"

Source:
Resume page 2

Evidence:
Employment section

Confidence:
0.82

Verification:
Unverified
```

So later you can ask:

> "Why did the system think this candidate had React experience?"

And the system can show the evidence.

---

# 17. `ComplianceCertificate`

Think of this as a **policy pass certificate**.

For example:

```text
Candidate: 1023

Required degree: PASS
Minimum experience: PASS
Required skill: PASS

Policy version: v4

Result: COMPLIANT
```

This gives you a structured record of:

> **Which rules were checked and what the result was.**

---

# 18. `AuditTrailLog`

This is basically the system's **history book**.

Imagine:

```text
10:31 — Resume uploaded
10:32 — LLM extracted experience
10:32 — Evidence scored
10:33 — OPA policy evaluated
10:33 — M_G calculated
10:33 — Candidate added to Top-K
```

This is extremely useful when someone asks:

> "Why did the system make this decision?"

Instead of saying:

> "The AI said so."

you can inspect the chain of events.

---

# 19. Browser Simulator

The final component is essentially a **visual playground**.

Instead of reading logs, you can watch:

```text
Candidate enters
      ↓
M_G changes
      ↓
Candidate enters Top-K
      ↓
Another candidate arrives
      ↓
Someone gets pushed out
```

You could visualize things like:

```text
Candidate A   91.2
Candidate B   89.8
Candidate C   87.4
Candidate D   84.1
Candidate E   82.7
```

and show why their scores changed.

---

# 20. The entire GADS system in one example

Let's say you're hiring:

### Backend Developer

Requirements:

```text
Node.js
PostgreSQL
2+ years experience
Bachelor's degree
```

Candidate submits:

> "3 years Node.js development, worked with PostgreSQL and AWS."

---

### Step 1 — LLM extraction

System extracts:

```text
Node.js → 3 years
PostgreSQL → yes
AWS → yes
```

---

### Step 2 — Evidence provenance

System checks:

```text
Node.js claim → employment history
PostgreSQL claim → project
AWS claim → resume only
```

So evidence quality might differ.

---

### Step 3 — OPA rules

Rules:

```text
Node.js → required ✅
PostgreSQL → required ✅
2+ years → required ✅
Degree → required ❌
```

Therefore:

```text
P_hard = FALSE
```

---

### Step 4 — `M_G`

Even if the semantic match is excellent:

```text
Semantic alignment = high
Evidence = good
Uncertainty = low
```

The hard policy requirement still fails.

---

### Step 5 — Decision

Instead of blindly saying:

```text
Hire
```

the system can say:

```text
Policy requirement failed.
Human review may be required.
```

That's the governance part.

---

# 21. So what makes GADS different from a normal AI ATS?

A normal system might effectively do:

```text
Resume
  ↓
LLM
  ↓
Score
  ↓
Rank
```

GADS tries to do:

```text
             ┌───────────────┐
Resume ────→ │ LLM Extraction│
             └───────┬───────┘
                     ↓
             ┌─────────────────┐
             │ Evidence Check  │
             └───────┬─────────┘
                     ↓
             ┌─────────────────┐
             │ OPA Policy      │
             │ Hard Rules      │
             └───────┬─────────┘
                     ↓
             ┌─────────────────┐
             │ Governed Match  │
             │ Score M_G       │
             └───────┬─────────┘
                     ↓
              ┌──────┴──────┐
              ↓             ↓
          Confident      Uncertain
              ↓             ↓
          Automated      Human
          processing      review
```

And alongside all of this:

```text
EvidenceLedger
      +
ComplianceCertificate
      +
AuditTrailLog
```

create an **audit trail**.

---

# 22. The simplest possible definition

You can describe GADS to someone non-technical as:

> **"GADS is a controlled AI hiring system where AI extracts information from resumes, checks the evidence behind those claims, applies fixed hiring rules, calculates a confidence-aware match score, and sends uncertain cases to humans instead of blindly trusting the AI."**

Or even simpler:

> **AI reads → evidence verifies → rules decide → uncertainty triggers humans → everything gets recorded.**

That's essentially the whole architecture in one sentence.


### Problems with the current system
Yes. The architecture is **much more controlled than a normal LLM-based ATS**, but I see several places where it can still fail—some quite seriously.

The biggest issue is this:

> **GADS controls how the AI makes a decision, but it does not automatically guarantee that the information entering the decision is correct, complete, or fair.**

Here are the main failure points.

### 1. The LLM can extract the wrong thing

Your entire pipeline starts with:

```text
Resume → LLM extraction
```

So if extraction is wrong, everything downstream can be perfectly governed and still produce the wrong result.

Example:

> "Worked on a team migrating a legacy Python service to Node.js."

The extractor might incorrectly produce:

```text
Node.js experience = 3 years
```

when the candidate never actually spent three years doing Node.js.

OPA won't catch this.

It will simply say:

```text
Node.js requirement → PASS
```

because it trusts the structured claim it receives.

So **governance after extraction cannot fully fix extraction errors**.

---

### 2. Evidence provenance can be gamed

This is probably one of your biggest weaknesses.

Suppose the resume says:

> "Expert in Kubernetes, AWS, Docker, Terraform."

Your system may correctly ask:

> "Where did this claim come from?"

But a provenance system answers:

> "Where did the claim come from?"

It does **not necessarily answer**:

> "Is the claim actually true?"

A resume itself is still a source.

So you could end up with:

```text
Evidence source = Resume
Provenance = known
```

and mistakenly interpret that as stronger evidence than it really is.

You need to distinguish:

```text
Source traceability
        ≠
Truth verification
```

That's a very important distinction.

---

### 3. Missing evidence can unfairly hurt good candidates

Imagine:

### Candidate A

Has:

* GitHub
* portfolio
* certificates
* detailed employment history

### Candidate B

Has:

* excellent real-world experience
* confidential company projects
* no public GitHub
* minimal online presence

Your provenance system may naturally give A stronger evidence.

But B isn't necessarily less qualified.

So:

```text
Less evidence available
```

can accidentally become:

```text
Less qualified
```

That's a dangerous assumption.

Your system needs to distinguish:

> **"I don't have evidence"**

from

> **"The candidate does not have the skill."**

Those are completely different statements.

---

# 4. `M_G` can create false mathematical confidence

This is another major weakness.

Suppose you have:

```text
E_prov = 0.80
S_sem  = 0.90
U_unc  = 0.10
```

and produce:

```text
M_G = 0.86
```

That **looks scientifically precise**.

But what exactly does `0.86` mean?

Is the candidate actually 86% qualified?

Probably not.

You're converting several uncertain judgments into one number.

That creates **false precision**.

A hiring manager could see:

```text
Candidate A → 87.4
Candidate B → 85.9
```

and assume A is meaningfully better.

The actual difference may be meaningless.

So you need to be careful not to turn:

> **heuristic signals**

into:

> **apparently objective numerical truth.**

---

# 5. Uncertainty itself can be incorrectly estimated

Your design depends heavily on:

```text
U_unc
```

But how does the system know that it's uncertain?

LLMs are often capable of producing confident-looking answers even when they're wrong.

So you might get:

```text
Wrong answer
+
High confidence
```

instead of:

```text
Wrong answer
+
High uncertainty
```

That breaks the human-routing mechanism.

Your architecture assumes:

> **The AI knows when it doesn't know.**

That's not guaranteed.

This means your uncertainty model needs **independent calibration**, not just an LLM saying "confidence = 0.92."

---

# 6. OPA can only enforce the rules you wrote

OPA is deterministic, which is good.

But this creates another problem.

Suppose the job requirement says:

```text
3 years Node.js
```

Candidate has:

```text
2 years Node.js
+
1 year Bun
+
1 year Deno
```

A human might reasonably consider that relevant backend JavaScript experience.

OPA might simply say:

```text
2 < 3

FAIL
```

So you get:

```text
Flexible human judgment
        ↓
rigid machine rule
```

That's not necessarily bad—but it means **policy design becomes extremely important**.

Bad policies produce consistently bad decisions.

---

# 7. Your semantic model can still be biased

`S_sem` sounds powerful:

> "How semantically close is this candidate to the job?"

But semantic similarity isn't the same thing as job suitability.

Example:

Job description:

```text
React
Node.js
AWS
Kubernetes
```

Resume:

```text
React
Node.js
AWS
Kubernetes
```

Very high semantic similarity.

But maybe the candidate only used those technologies in college projects.

Another candidate:

```text
Built large-scale distributed systems
Led backend migration
Designed production infrastructure
```

but uses slightly different technology names.

The first candidate could receive the higher semantic score even though the second may have more relevant real-world experience.

So:

```text
Semantic similarity ≠ competence
```

---

# 8. The "Role Model" benchmark can become self-reinforcing

This is a particularly interesting risk.

Suppose your role model is created from historically successful candidates.

Then:

```text
Historical candidates
      ↓
Role Model
      ↓
New candidate ranking
      ↓
Future hiring data
      ↓
Updated Role Model
```

You can accidentally create a feedback loop.

The system keeps selecting people who resemble previous hires.

Eventually:

> "similar to people we historically hired"

becomes:

> "what the system considers good."

That can make the system less capable of recognizing **non-traditional but qualified candidates**.

---

# 9. PBPQ can produce unstable rankings

Your streaming top-K design is efficient, but consider:

```text
Candidate 1 → score 86
Candidate 2 → score 85
Candidate 3 → score 84
```

Candidate 3 gets evaluated later and new evidence changes the benchmark.

Suddenly:

```text
Candidate 1 → 86
Candidate 2 → 82
Candidate 3 → 91
```

Now the ordering changes.

That's okay—but if your benchmark itself is dynamic, you need to guarantee that previous scores remain comparable.

Otherwise you can get:

```text
Same candidate
Same resume
Different arrival time
Different ranking
```

That is a reproducibility problem.

---

# 10. Adversarial resumes are still difficult

You specifically mention defense against adversarial corruption.

That's good, but consider a sophisticated attacker.

Instead of keyword stuffing:

```text
React React React React
```

they could write natural-looking evidence:

> "Architected a highly scalable React-based microfrontend platform serving 10M users..."

The text may look legitimate.

Unless you have independent evidence, the system has no fundamental way to know whether that statement is true.

So your system is much better at defending against:

> **AI manipulation**

than against:

> **real-world lies.**

Those are different problems.

---

# 11. Human-in-the-loop can become the new bottleneck

Your architecture says:

```text
Low confidence → Human review
```

Sounds great.

But imagine:

```text
100,000 resumes
        ↓
15% uncertain
        ↓
15,000 human reviews
```

Now you've moved the bottleneck from AI to humans.

Worse, humans may start doing this:

> "The system usually gets it right, so I'll just approve what it says."

Then human review becomes ceremonial rather than meaningful.

That's a classic **automation bias** problem.

---

# 12. Your audit trail proves what happened, not that it was correct

This is another subtle distinction.

Your:

```text
EvidenceLedger
ComplianceCertificate
AuditTrailLog
```

are excellent for explainability.

But:

```text
Auditability ≠ correctness
```

You could have a beautifully documented decision:

```text
10:31 extracted skill
10:32 provenance calculated
10:33 OPA evaluated
10:34 score generated
10:35 candidate rejected
```

Every step is recorded perfectly.

And the decision could still be wrong.

So don't sell the audit trail as proof that the system made a correct decision.

It's proof of **how the decision was produced**.

---

# 13. Multi-stage errors can compound

This is perhaps the biggest architectural problem.

Suppose:

```text
LLM extraction accuracy        = 95%
Evidence classification        = 95%
Semantic matching              = 95%
Uncertainty estimation        = 90%
Policy mapping                 = 99%
```

Those percentages are hypothetical, but look at the chain.

One stage's mistake becomes the next stage's input.

So:

```text
Stage 1 wrong
      ↓
Stage 2 trusts wrong information
      ↓
Stage 3 applies policy correctly
      ↓
Stage 4 calculates score correctly
      ↓
Final answer = wrong
```

This is why **"deterministic downstream processing" doesn't automatically make an AI pipeline reliable**.

---

# 14. There is a deeper problem: you are scoring the representation, not the person

This is the philosophical weakness of almost every resume-screening system.

The actual person is:

```text
Candidate
```

But your system sees:

```text
Resume
 ↓
Extracted claims
 ↓
Evidence
 ↓
Embeddings
 ↓
Scores
```

You're ultimately ranking a **machine-generated representation of the candidate**.

A candidate who writes excellent resumes can therefore appear stronger than someone with the same capability but worse documentation.

So you should explicitly recognize:

> **GADS is primarily a system for governing resume-based decisions, not a system for measuring human capability directly.**

That's an important distinction.

---

# Where I would attack this system first

If I were stress-testing your architecture, I'd focus on these five areas:

| Risk                        | Why it matters                                              |
| --------------------------- | ----------------------------------------------------------- |
| **LLM extraction errors**   | Everything downstream depends on the extracted facts        |
| **Evidence ≠ truth**        | A traceable claim can still be false                        |
| **Uncertainty calibration** | Human routing fails if confidence estimates are wrong       |
| **Dynamic Role Model**      | Can create feedback loops and unstable rankings             |
| **False precision of M_G**  | A numerical score can look more objective than it really is |

---

# The biggest conceptual fix I'd make

I'd change the mental model from:

```text
AI → Evidence → Score → Decision
```

to:

```text
AI → Claims
       ↓
Independent Evidence
       ↓
Policy Checks
       ↓
Uncertainty
       ↓
Decision Support
```

And importantly, I'd separate three things:

```text
CLAIM
"Candidate says they know Kubernetes"

EVIDENCE
"Here is what supports that claim"

VERIFICATION
"Here is independent evidence that confirms it"
```

Those three should **not be treated as the same thing**.

That one change would make your architecture substantially more defensible.

### My overall assessment

The architecture is interesting because it addresses a real weakness of LLM-only hiring systems: **unchecked AI decisions**.

But its biggest weakness is that it can become **a very well-governed machine for making decisions from imperfect inputs**.

In other words:

> **GADS can make the decision process more controlled, traceable, and reproducible without necessarily making the underlying hiring decision more correct.**

That distinction is probably the most important thing to address if this is being presented as a serious research/engineering system.

Yes. I think the three additions you are proposing can make GADS substantially stronger, but I would modify all three slightly.

The most important change is this:

> **Don't build GADS around “Does the resume mention the skill?” Build it around “What is the strongest defensible evidence that the candidate possesses this capability?”**

That turns your system from an intelligent ATS into something closer to a **skill-evidence reasoning system**.

I’ll break your ideas down and then give you a revised architecture.

---

# 1. Your tree idea is good — but make it a graph

You proposed something like:

```text
Programming
   │
   └── JavaScript
          │
          └── Node.js
                 │
                 ├── Express
                 ├── Fastify
                 └── NestJS
```

This is a very useful idea because technologies aren't isolated.

For example:

```text
React
  ↓
JavaScript / TypeScript
```

and:

```text
Express
  ↓
Node.js
  ↓
JavaScript
```

So if a resume contains **strong evidence of Express development**, we can reasonably infer some evidence of Node.js experience.

But I would **not call this a tree** in the actual implementation.

Call it a:

> **Skill Dependency & Evidence Graph**

because technologies can have multiple parents and relationships.

For example:

```text
                    JavaScript
                   /     |     \
                  /      |      \
              React    Node.js   TypeScript
                          |
                   ┌──────┼───────┐
                   ↓      ↓       ↓
               Express  Fastify  NestJS
                   |
                REST APIs
```

Real skill ecosystems are graphs, not clean trees.

Existing workforce taxonomies already use hierarchical and relational skill structures. ESCO, for example, maintains skill hierarchies and explicit skill-to-skill and occupation-to-skill relationships; O*NET similarly provides software, transferable-skill, knowledge, ability, and occupation competency frameworks. ([ESCO][1])

---

# 2. The crucial idea: distinguish a claim from inferred evidence

This is where I think your idea can significantly improve the original GADS.

Suppose the resume says:

> "Developed REST APIs using Express.js."

A naive system says:

```text
Express = YES
Node.js = YES
JavaScript = YES
```

That is too aggressive.

Your system should instead construct something like:

```text
Candidate claims:
    Express.js → direct claim/evidence

Evidence-backed inference:
    Node.js → inferred from Express usage

Weaker inference:
    JavaScript → inferred from Node/Express code

Unknown:
    Advanced JavaScript → NOT established
```

That's much more defensible.

### In other words:

```text
Express evidence
     ↓
strong inference → Node.js
     ↓
moderate inference → JavaScript
     ↓
DO NOT automatically infer → Advanced JS
```

This is extremely important.

Knowing one technology does **not prove mastery of everything underneath it**.

For example:

```text
Knows React
   ↓
reasonable inference → JavaScript familiarity

NOT reasonable:
Knows React
   ↓
therefore expert JavaScript
```

---

# 3. I'd create four evidence levels

Instead of having just a single `E_prov` number, I'd give every skill claim an **evidence status**.

### Level 0 — Unclaimed

Nothing indicates the skill.

```text
Java
→ no information
```

### Level 1 — Self-Claimed

The candidate explicitly says:

> "Java — proficient"

Store:

```text
skill: Java
source: resume
evidence_type: SELF_CLAIM
```

But don't treat that as verification.

### Level 2 — Inferred

The system finds evidence of a dependent technology.

Example:

```text
Express project
      ↓
Node.js inferred
      ↓
JavaScript inferred
```

### Level 3 — Artifact-backed

There is actual supporting material:

```text
GitHub repository
Portfolio
Project files
Technical assessment
Code sample
Work artifact
```

### Level 4 — Verified/Assessed

Strongest evidence:

```text
Structured coding assessment
Verified employment evidence
Credential verification
Employer-confirmed experience
Controlled technical evaluation
```

So your candidate profile becomes:

```text
JavaScript
   ├── Self claim: YES
   ├── Inferred from Node: YES
   ├── Code artifact: YES
   └── Assessment: 78%
```

That is **far more informative than:**

```text
JavaScript = 87%
```

---

# 4. And now you can solve one of the problems we discussed earlier

Remember the problem:

> "I don't have evidence" ≠ "candidate doesn't have the skill."

Your graph can explicitly represent this.

For example:

```text
Python
   status: UNKNOWN
```

doesn't mean:

```text
Python = NO
```

It means:

```text
No sufficient evidence found.
```

That's a major improvement.

You could have:

```text
NOT_CLAIMED
CLAIMED
INFERRED
SUPPORTED
VERIFIED
ASSESSED
```

rather than a simplistic:

```text
YES / NO
```

---

# 5. Evidence propagation needs a safety mechanism

Here's where I'd be very careful.

Suppose:

```text
NestJS
 ↓
Express
 ↓
Node
 ↓
JavaScript
 ↓
Programming
```

If you let evidence propagate indefinitely, you'll eventually get:

```text
Candidate knows NestJS

Therefore:
Programming = 100%
JavaScript = 95%
Node = 98%
Express = 99%
Backend = 94%
Software engineering = 92%
```

That's nonsense.

So you need **evidence decay and inference caps**.

Conceptually:

```text
Direct evidence
     ↓ 100%
First-level inference
     ↓ 70–90%
Second-level inference
     ↓ 40–60%
Third-level inference
     ↓
Don't propagate
```

The exact numbers should be empirically validated rather than arbitrarily fixed.

A useful conceptual equation would be:

```text
InferredEvidence(child)
=
DirectEvidence(parent)
× RelationStrength
× ContextRelevance
× DepthDecay
```

with a maximum ceiling for inferred evidence.

For example:

```text
Express evidence = 0.90

Express → Node relation = 0.90

Node inference ≈ 0.81

Node → JavaScript = 0.75

JavaScript inference ≈ 0.61
```

But:

```text
Advanced JavaScript ≠ established
```

because the graph shouldn't confuse **dependency** with **proficiency**.

---

# 6. Even better: define different relationship types

Your graph shouldn't simply say:

```text
Node → JavaScript
```

It should say what the relationship means.

For example:

```text
Express ──requires/use-of──→ Node.js

Node.js ──implemented-with──→ JavaScript

React ──commonly-uses──→ JavaScript

TypeScript ──extends/typed-superset-of──→ JavaScript
```

And:

```text
Express ──does-not-prove──→ Advanced Node.js
```

That's useful because the inference engine knows **what can and cannot be concluded**.

I'd define relationships such as:

```text
PREREQUISITE
DEPENDS_ON
USES
SUBTYPE_OF
ALIAS_OF
TRANSFERABLE_TO
EVIDENCE_OF
NOT_SUFFICIENT_FOR
```

That last one is especially useful.

---

# 7. Your "core language" idea is exactly right

You said:

> for core languages we need to take user's claim.

I agree.

Suppose:

```text
Python
Java
C++
Go
Rust
```

The candidate says:

> "Python — advanced."

Your system shouldn't invent evidence merely because the candidate knows Django.

Instead:

```text
Python
 ├── SELF_CLAIM: Advanced
 ├── PROJECT_EVIDENCE: Django project
 ├── CODE_EVIDENCE: Available
 └── ASSESSMENT: Not available
```

Then GADS can say:

> **Claim supported by indirect evidence, but not formally verified.**

That's much more honest.

---

# 8. You could make this a "Claim → Evidence → Verification" chain

I think this should become one of the signature features of your system.

For every important candidate capability:

```text
CLAIM
  ↓
EVIDENCE
  ↓
INFERENCE
  ↓
VERIFICATION
```

Example:

```text
Candidate claims:
"3 years of Node.js"

        ↓

Evidence:
"Backend API project"

        ↓

Artifact:
package.json
Express dependencies
Node runtime
source files

        ↓

Inference:
Node.js usage = supported

        ↓

Verification:
employment/technical assessment = unavailable

        ↓

Final state:
SUPPORTED
not VERIFIED
```

This is much more powerful than traditional resume keyword matching.

---

# 9. Now your second idea — adaptability

I like this idea, but I would **change the terminology**.

You said:

> "excited for learning new technology"

I would **not let an AI infer excitement or personality from a resume**.

That's because something like:

> "Passionate about learning new technologies"

is almost useless as evidence.

Someone can write that without having demonstrated it.

And the opposite is also possible: someone may be excellent at learning technology without using those words.

Instead create:

# `G_adapt` — Demonstrated Adaptability / Learning Capacity

And measure **observable behavior**, not personality.

---

# 10. What should count as adaptability?

For example:

### Technology transition

```text
C++ → Python → JavaScript → Rust
```

That's evidence of technology transitions.

### Domain transition

```text
Frontend
   ↓
Backend
   ↓
Cloud
   ↓
AI
```

Potential evidence of broader adaptation.

### Learning under constraints

Suppose the candidate built:

> "A WebGPU application despite having no previous WebGPU experience."

That's much more useful evidence than:

> "I'm passionate about technology."

### Structured assessment

You could even give the candidate:

> "Here's a framework you've never used. Build a small API."

Then observe:

```text
Learning time
Problem solving
Documentation usage
Implementation quality
Error recovery
Final result
```

That's much stronger.

---

# 11. So I'd separate three things

Instead of:

```text
Adaptability = AI thinks candidate is adaptable
```

use:

```text
G_adapt
├── Technology Transition Evidence
├── Transferable Skill Evidence
├── Learning Velocity
├── Novel-Technology Assessment
└── Self-Reported Learning Preference
```

And importantly:

```text
Self-report
```

should be the weakest component.

---

# 12. Don't use "enthusiasm" as a hidden personality score

This matters for the credibility of your system.

A resume might contain:

> "Passionate developer."

The LLM shouldn't translate that into:

```text
Enthusiasm = 95
```

That's essentially psychological inference from text.

Instead:

```text
Claim:
"Passionate about learning"

Evidence:
No supporting evidence

Result:
SELF_REPORTED
```

Whereas:

```text
Candidate:
Learned Rust
Built project
Published implementation
Passed assessment

Result:
OBSERVED_ADAPTABILITY
```

That is defensible.

This is also consistent with a broader AI governance principle: systems should be evaluated for validity, uncertainty, human-AI interaction, and real-world performance rather than assuming a model's output is intrinsically trustworthy. NIST specifically emphasizes testing, measurement, uncertainty, benchmarking, and documenting human oversight. ([NIST AI Resource Center][2])

---

# 13. Now the third idea: your "perfect candidate"

This is where I would make the biggest modification.

Your instinct is:

> "Why don't we create one perfect candidate and compare every student against them?"

The intuition is good.

But **don't create a perfect candidate.**

Create a:

# `Role Blueprint`

or

# `Role Reference Profile`

Why?

Because there is rarely one correct representation of a perfect candidate.

---

# 14. Suppose the job is MERN developer

You could define:

```text
ROLE BLUEPRINT

Hard requirements
├── JavaScript
├── Node.js
├── React
└── MongoDB

Core competencies
├── REST APIs
├── Git
├── Debugging
└── Testing

Preferred
├── Docker
├── AWS
└── CI/CD

Adaptability
├── Technology transition
└── New-framework learning

Additional value
└── Open-source / leadership / research / etc.
```

Now you have a **structured target**.

You're no longer saying:

> "Candidate should look like Dev X."

You're saying:

> "This role has these capability requirements."

That's much better.

---

# 15. And you identified the biggest weakness yourself

You said:

> "drawback will be that we can not set achievements outside of requirements"

Exactly.

If you create a perfect resume like:

```text
React
Node
MongoDB
AWS
Docker
Kubernetes
...
```

then your system may treat:

```text
Candidate knows Rust
Candidate built an ML system
Candidate has published research
Candidate has open-source contributions
```

as irrelevant.

That would be a mistake.

But I wouldn't solve this by putting everything into the required profile.

Instead create a separate dimension:

# `A_extra` — Additional Value

So:

```text
Role Fit
+
Additional Value
```

becomes:

```text
Candidate A

Role requirements:
Excellent

Additional value:
Low
```

versus:

```text
Candidate B

Role requirements:
Excellent

Additional value:
High
```

Both remain strong matches.

But B gets additional recognition.

---

# 16. Do NOT allow extra achievements to compensate for hard failures

This distinction is crucial.

Suppose:

```text
Job requires:
Bachelor's degree
```

Candidate:

```text
No degree
+
5 open-source projects
+
3 hackathon wins
+
great GitHub
```

You don't want:

```text
Extra achievements = +20

Degree missing = -10

Final = PASS
```

because your hard requirement has become meaningless.

Instead:

```text
P_hard = FALSE

Additional Value = HIGH

Status = Policy exception / human review
```

The extra achievements can be displayed to the reviewer, but they shouldn't silently override the policy.

This keeps your governance layer intact.

---

# 17. Your benchmark should therefore have layers

I'd structure your Role Blueprint like this:

```text
                 ROLE BLUEPRINT
                       │
        ┌──────────────┼───────────────┐
        ↓              ↓               ↓
   HARD GATES      CORE TARGET      OPTIONAL
        │              │               │
   must satisfy    expected level    differentiators
        │              │               │
        └──────────────┼───────────────┘
                       ↓
               ADAPTABILITY
                       ↓
               ADDITIONAL VALUE
```

Then candidates aren't compared to a person.

They're compared against a **role specification**.

---

# 18. I would actually use TWO benchmarks

This is even better.

Instead of:

```text
Perfect Candidate
```

use:

```text
Minimum Acceptable Profile
```

and

```text
Target Role Profile
```

For example:

```text
JavaScript

Minimum:
Intermediate

Target:
Advanced
```

Then:

```text
Node.js

Minimum:
Basic production experience

Target:
Strong production experience
```

This handles candidates who exceed requirements without turning "more is always better" into the rule.

---

# 19. This also solves overqualification

Imagine:

```text
Job:
Junior Backend Developer
```

Target:

```text
Node.js = intermediate
Postgres = intermediate
Docker = basic
```

Candidate has:

```text
Node.js = expert
Kubernetes = expert
Distributed systems = expert
AWS = expert
```

A naive "perfect candidate distance" system might say:

> Candidate is extremely far above the target.

That's actually not necessarily bad or good—it just means they exceed the requested level.

Your role blueprint should represent **minimum and desired ranges**, not a single point.

Something like:

```text
Skill level

0──────1──────2──────3──────4──────5
       Minimum └──Target──┘
```

Candidate can fall:

```text
Below minimum
Within target
Above target
```

This is much more realistic.

---

# 20. Now let's solve "achievements outside requirements"

I would divide them into three buckets.

### A. Directly relevant

Job:

```text
Node.js
```

Candidate:

```text
Built high-scale Node.js API
```

This should strengthen the core evidence.

### B. Transferable

Job:

```text
Node.js
```

Candidate:

```text
Built high-scale Go backend
```

This shouldn't be counted as Node experience.

But it could contribute to:

```text
Backend competency
Adaptability
Systems experience
```

### C. Independent value

Candidate:

```text
Published ML research
Won hackathons
Contributed to open source
Led a technical community
```

These shouldn't artificially increase Node.js skill.

But they can appear under:

```text
Additional Value
```

That's a cleaner approach.

---

# 21. This gives you a much richer candidate representation

Instead of:

```text
Candidate Score = 87
```

you'd have something like:

```text
Candidate
│
├── Requirement Fit
│   ├── JavaScript       ✓
│   ├── Node.js          ✓
│   ├── Express          ✓
│   ├── PostgreSQL       ~
│   └── Docker           ✕
│
├── Evidence Strength
│   ├── Verified
│   ├── Artifact-backed
│   ├── Inferred
│   └── Self-claimed
│
├── Adaptability
│   ├── New technology transitions
│   ├── Cross-domain learning
│   └── Novel technology assessment
│
├── Additional Value
│   ├── Open source
│   ├── Research
│   └── Leadership
│
└── Uncertainty
    ├── PostgreSQL
    └── Docker
```

Then your final score is **a summary of a richer state**, rather than the state itself.

---

# 22. This changes your `M_G` significantly

Your original idea was roughly:

```text
M_G =
Evidence
+
Semantic Alignment
-
Uncertainty
```

I'd evolve it into:

```text
                    ┌───────────────┐
                    │ Hard Policy   │
                    │    P_hard     │
                    └───────┬───────┘
                            │
                    must pass first
                            ↓
              ┌─────────────────────────┐
              │     Role Fit            │
              │       R_fit              │
              └────────────┬────────────┘
                           +
              ┌─────────────────────────┐
              │ Evidence Strength       │
              │       E_prov             │
              └────────────┬────────────┘
                           +
              ┌─────────────────────────┐
              │ Adaptability             │
              │       G_adapt            │
              └────────────┬────────────┘
                           +
              ┌─────────────────────────┐
              │ Additional Value        │
              │       A_extra            │
              └────────────┬────────────┘
                           -
              ┌─────────────────────────┐
              │ Uncertainty              │
              │       U_unc              │
              └────────────┬────────────┘
                           ↓
                         M_G
```

But there's an important design principle:

> **`P_hard` should be a gate, not merely another weighted feature.**

So:

```text
P_hard = FALSE
```

should usually produce:

```text
NOT_ELIGIBLE
```

or:

```text
POLICY_EXCEPTION
```

rather than allowing enough bonus points to overcome it.

---

# 23. What I would do with the priority queue

Your original idea:

> Compare students against an assumed perfect candidate.

I'd change it to:

```text
Role Blueprint
      ↓
Candidate evaluation
      ↓
Policy eligibility
      ↓
M_G
      ↓
Priority Queue
```

The priority queue then contains:

```text
Candidate A
Candidate B
Candidate C
...
```

ordered by their **governed role-fit score**.

But the Role Blueprint is fixed for the job.

That's important.

I would **not continuously redefine the ideal candidate based on whoever happens to be in the queue**.

Otherwise:

```text
Candidate population
        ↓
changes benchmark
        ↓
benchmark changes scores
        ↓
scores change candidate population
```

That can create a feedback loop.

---

# 24. Your "Role Model" can still exist — just change what it is

You can retain your original concept.

Call it:

> **Role Reference Model (RRM)**

But instead of representing:

> "The perfect human"

it represents:

> **"The expected capability distribution for this role."**

For example:

```text
RRM: Backend Developer

JavaScript     → Core
Node.js        → Core
Postgres       → Core
Docker         → Preferred
AWS            → Preferred
Kubernetes     → Optional

Debugging      → High importance
API design     → High importance
Learning agility → Moderate importance
```

Now you're comparing:

```text
Candidate
      ↓
against
Role Reference Model
```

rather than:

```text
Candidate
      ↓
against
another candidate's resume
```

That is much cleaner scientifically.

---

# 25. There's another thing I'd add: substitution paths

Your graph can become powerful here.

Suppose a job says:

```text
Express.js
```

Candidate has:

```text
Fastify
```

A naive ATS says:

```text
Express = missing
```

Your graph can understand:

```text
Node.js
 ├── Express
 ├── Fastify
 └── NestJS
```

Then:

```text
Fastify
 ↓
Node.js backend experience
 ↓
Relevant transfer
```

The candidate still doesn't get:

```text
Express = verified
```

but might get:

```text
Express requirement:
Adjacent capability detected
```

That is much more intelligent.

The employer can define whether a requirement permits substitution:

```text
EXACT
FAMILY_EQUIVALENT
TRANSFERABLE
NO_SUBSTITUTION
```

This is similar in spirit to established skills frameworks that distinguish relationships between skills and occupations rather than relying solely on exact keyword matches. ([ESCO][3])

---

# 26. Your schema should evolve accordingly

Your current:

```text
EvidenceLedger
ComplianceCertificate
AuditTrailLog
```

is good.

I'd add:

### `SkillNode`

```text
skill_id
name
category
level
domain
```

### `SkillRelation`

```text
source_skill
target_skill
relation_type
strength
max_inference_depth
```

Example:

```text
Express → Node
relation = DEPENDS_ON
strength = 0.9
```

### `CandidateClaim`

```text
candidate_id
skill_id
claim_type
source
text_span
timestamp
```

### `EvidenceItem`

```text
evidence_id
claim_id
source_type
source_location
provenance
verification_status
confidence
```

### `EvidenceInference`

This is important.

```text
inference_id
candidate_id
target_skill
source_skill
relation
inference_strength
reason
depth
```

Now you can literally explain:

> "Node.js evidence was inferred from Express usage in Project X."

That's excellent explainability.

### `RoleBlueprint`

```text
role_id
skill_id
requirement_type
minimum_level
target_level
substitution_policy
evidence_requirement
```

### `AdaptabilityEvidence`

```text
candidate_id
event
technology
previous_skill
new_skill
learning_evidence
assessment_result
```

### `AdditionalValue`

```text
candidate_id
category
evidence
relevance
impact
```

---

# 27. Your final decision explanation can become incredibly good

Instead of:

> Candidate score: 84

GADS could show:

```text
Candidate: C1024

POLICY
✓ All mandatory requirements satisfied

CORE FIT
JavaScript          Strong
Node.js              Strong
Express              Verified
PostgreSQL            Moderate
Docker                Missing

EVIDENCE
✓ 3 artifact-backed skills
✓ 2 verified claims
~ 2 inferred claims
! 2 self-reported claims

ADAPTABILITY
✓ Transitioned from PHP → Node.js
✓ Learned Docker during project
~ No formal learning assessment

ADDITIONAL VALUE
✓ Open-source contribution
✓ Distributed systems project

UNCERTAINTY
PostgreSQL proficiency: Moderate uncertainty
Docker proficiency: High uncertainty
```

**That is much more useful to a recruiter than 84.**

---

# 28. And this changes the purpose of `M_G`

I wouldn't make `M_G` the **decision**.

I'd make it a **compressed ranking signal**.

That's a subtle but important distinction.

The actual system state is:

```text
Claim graph
+
Evidence graph
+
Policy state
+
Adaptability evidence
+
Uncertainty
+
Additional value
```

Then:

```text
M_G
```

is just the number that lets the priority queue efficiently order candidates.

So:

> **The score is not the truth. It is a summary of a structured evidence state.**

That's a much stronger conceptual foundation.

---

# 29. A complete revised GADS architecture

I'd now design the system like this:

```text
                         JOB DESCRIPTION
                                │
                                ↓
                    ┌─────────────────────┐
                    │ Role Blueprint      │
                    │                     │
                    │ Hard Requirements   │
                    │ Core Skills         │
                    │ Target Levels       │
                    │ Optional Skills     │
                    │ Substitution Rules  │
                    └──────────┬──────────┘
                               │
                               ↓
                    ┌─────────────────────┐
                    │ Skill Dependency    │
                    │ & Evidence Graph    │
                    └──────────┬──────────┘
                               │
                               │
RESUME ──→ LLM EXTRACTION ─────┘
              │
              ↓
       Candidate Claims
              │
              ↓
      Evidence Collection
              │
      ┌───────┼────────┐
      ↓       ↓        ↓
   Resume   Artifacts  Assessments
      │       │        │
      └───────┼────────┘
              ↓
       Evidence Ledger
              │
              ↓
      Evidence Inference
      through Skill Graph
              │
              ↓
       ┌───────────────┐
       │ OPA / POLICY  │
       │ Hard Gates    │
       └───────┬───────┘
               │
          PASS / FAIL
               │
               ↓
     ┌───────────────────────┐
     │ Governed Evaluation   │
     │                       │
     │ Role Fit              │
     │ Evidence              │
     │ Adaptability          │
     │ Additional Value      │
     │ Uncertainty            │
     └───────────┬───────────┘
                 │
                 ↓
               M_G
                 │
                 ↓
       Policy-Bounded Priority
              Queue
                 │
          ┌──────┴───────┐
          ↓              ↓
    High confidence   Uncertain
          ↓              ↓
      Shortlist      Human Review
```

And everything generates:

```text
EvidenceLedger
ComplianceCertificate
AuditTrailLog
```

---

# 30. There is one major thing I would NOT do

I would not use:

```text
"AI thinks this person is adaptable"
```

or:

```text
"AI thinks this person is enthusiastic"
```

or:

```text
"AI thinks this person will learn quickly"
```

Those are much harder to substantiate than technical capability.

You can measure:

```text
Demonstrated technology transitions
Observed learning behavior
Assessment performance
Transferable skills
```

You should be much more cautious about measuring:

```text
Personality
Motivation
Excitement
Culture fit
Attitude
```

from resume text.

Recent research continues to show why this matters: a 2026 preprint found that subtle sociocultural markers in otherwise anonymized resumes could materially change LLM-based hiring outputs, while another 2026 preprint found model-generation-dependent demographic disparities in paired resume screening. These are research findings rather than a definitive statement about every model, but they're good reasons to treat non-job-relevant textual signals as potentially dangerous features. ([arXiv][4])

---

# 31. One more improvement: separate "qualification" from "potential"

This is probably the best conceptual addition I can give you.

Your system currently mixes:

```text
"What can this person do today?"
```

with:

```text
"What could this person potentially learn?"
```

Those aren't the same.

So create two axes:

### `Q_current`

Current demonstrated capability.

```text
What can the candidate prove today?
```

### `G_adapt`

Growth/adaptation evidence.

```text
How much evidence is there that the candidate can successfully move into unfamiliar technologies?
```

Now you can represent:

```text
Candidate A
Q_current = Very High
G_adapt = Moderate
```

and:

```text
Candidate B
Q_current = Moderate
G_adapt = Very High
```

Neither number needs to pretend to be the other.

That is **much more informative than a single 0–100 number**.

---

# 32. My recommended final scoring model

Conceptually:

```text
                ┌───────────────┐
                │   P_hard      │
                │ Policy Gate   │
                └───────┬───────┘
                        │
                 Must satisfy
                        ↓
           ┌─────────────────────┐
           │     Q_current       │
           │ Current Capability  │
           └──────────┬──────────┘
                      +
           ┌─────────────────────┐
           │     E_prov          │
           │ Evidence Strength   │
           └──────────┬──────────┘
                      +
           ┌─────────────────────┐
           │     G_adapt         │
           │ Adaptability        │
           └──────────┬──────────┘
                      +
           ┌─────────────────────┐
           │     A_extra         │
           │ Additional Value    │
           └──────────┬──────────┘
                      -
           ┌─────────────────────┐
           │     U_unc           │
           │ Uncertainty         │
           └──────────┬──────────┘
                      ↓
                    M_G
```

But I'd actually expose **both**:

```text
Candidate Profile
────────────────────────────
Current Capability: 86
Evidence Strength: 91
Adaptability Evidence: 78
Additional Value: High
Uncertainty: 12
Policy: PASS
────────────────────────────
Governed Match: 88
```

Rather than pretending:

```text
Candidate = 88% qualified
```

---

# 33. What I think your strongest research contribution could become

Originally the claim was roughly:

> "We built a governed AI resume screener."

That's interesting but somewhat broad.

Your refined concept could become:

> **A provenance-aware skill reasoning system that separates self-claims, evidence-backed capabilities, dependency-based skill inference, demonstrated adaptability, and policy eligibility before producing a governed ranking.**

That is considerably more interesting.

The key novel components could be:

### 1. Skill-Evidence Graph

```text
Technology relationships
+
claim provenance
+
bounded inference
```

### 2. Evidence State Model

```text
Self-Claim
→ Inferred
→ Supported
→ Verified
→ Assessed
```

### 3. Current Capability vs Adaptability

```text
Q_current ≠ G_adapt
```

### 4. Role Reference Model

```text
Role Blueprint
rather than
Perfect Candidate
```

### 5. Additional Value Layer

```text
Role Fit
+
bounded extra achievements
```

### 6. Governed Priority Queue

```text
P_hard
→ M_G
→ Top-K
→ Human escalation
```

---

# 34. And one thing I would add to make this genuinely research-grade

Don't just demonstrate that GADS **works**.

Build the simulator so you can deliberately break it.

For example:

```text
Experiment 1:
Keyword stuffing

Experiment 2:
False skill claims

Experiment 3:
Missing evidence

Experiment 4:
Technology substitution
Express → Fastify

Experiment 5:
Nontraditional candidate
Strong transferable skills, different stack

Experiment 6:
High current skill + low adaptability evidence

Experiment 7:
Low current skill + strong adaptability evidence

Experiment 8:
Extraordinary achievements outside JD

Experiment 9:
Changing candidate arrival order

Experiment 10:
Subtle demographic/non-job-related resume changes
```

Then measure:

```text
Ranking stability
Evidence accuracy
Inference error
Uncertainty calibration
Human escalation rate
False positive / false negative rates
Policy violations
Subgroup disparities
```

NIST's AI RMF explicitly emphasizes test/evaluation/validation, uncertainty measurement, benchmarking, documentation, and human-oversight metrics, which fits this kind of experimental evaluation very well. ([NIST][5])

And I would **not use historical “who got hired” as your only ground truth**. Historical hiring outcomes can contain the biases and limitations of the previous hiring process; research on algorithmic hiring has specifically highlighted the importance of choices around prediction targets, data, validation, and bias evaluation. ([Microsoft][6])

---

## The version I'd ultimately pitch

Your whole system can now be summarized as:

> **GADS doesn't ask only “How well does this resume match the job?” It asks “What does the candidate claim, what evidence supports the claim, what capabilities can safely be inferred from related technologies, what has actually been demonstrated, how adaptable is the candidate based on observable evidence, what additional value exists beyond the job requirements, and which parts remain uncertain?”**

Then:

```text
                    GADS
                      │
         ┌────────────┼─────────────┐
         ↓            ↓             ↓
   CLAIM GRAPH    EVIDENCE GRAPH  ROLE BLUEPRINT
         │            │             │
         └────────────┼─────────────┘
                      ↓
                POLICY GATE
                      ↓
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
 Current Skill    Adaptability   Extra Value
       │              │              │
       └──────────────┼──────────────┘
                      ↓
                 Uncertainty
                      ↓
                    M_G
                      ↓
                    PBPQ
                      ↓
              Human Escalation
```

**The biggest conceptual upgrade is replacing "perfect candidate" with a `Role Blueprint`, and replacing "resume evidence" with a `Claim → Evidence → Inference → Verification` graph.**

That gives you a system that can recognize **both conventional candidates and unconventional candidates**, while still preventing unsupported claims from receiving the same status as verified evidence.

[1]: https://esco.ec.europa.eu/en/classification?utm_source=chatgpt.com "The ESCO Classification | European Skills, Competences, Qualifications and Occupations (ESCO)"
[2]: https://airc.nist.gov/airmf-resources/playbook/measure/?utm_source=chatgpt.com "Measure - AIRC"
[3]: https://esco.ec.europa.eu/en/about-esco/escopedia/escopedia/two-pillar-structure-esco?utm_source=chatgpt.com "Two pillar structure of ESCO | European Skills, Competences, Qualifications and Occupations (ESCO)"
[4]: https://arxiv.org/abs/2603.05189?utm_source=chatgpt.com "Small Changes, Big Impact: Demographic Bias in LLM-Based Hiring Through Subtle Sociocultural Markers in Anonymised Resumes"
[5]: https://www.nist.gov/itl/ai-risk-management-framework?utm_source=chatgpt.com "AI Risk Management Framework | NIST"
[6]: https://www.microsoft.com/en-us/research/publication/mitigating-bias-in-algorithmic-hiring-evaluating-claims-and-practices/?utm_source=chatgpt.com "Mitigating bias in algorithmic hiring: evaluating claims and practices - Microsoft Research"


Yes. I checked the current platform rules and privacy framework, and this changes my recommendation in an important way:

> **The idea of using external professional evidence is strong. The idea of implementing it as generic “social scraping” is not.**

For your GADS architecture, I would **keep the evidence-enrichment concept but remove unrestricted scraping from the core design**.

## 1. Your underlying idea is valid

Your reasoning is sound:

```text
Candidate claims:
"I know Node.js"

        ↓

Resume evidence
        +
GitHub evidence
        +
Portfolio evidence
        +
LinkedIn professional evidence
        ↓

Stronger claim verification
```

That addresses a real weakness we identified earlier:

> A resume claim alone does not establish that the claimed skill actually exists.

External professional artifacts can provide additional evidence.

For example:

```text
Resume:
"Built Node.js backend"

GitHub:
Express/Fastify project

Portfolio:
Architecture + demo

Assessment:
Passed Node.js task
```

Now the claim has multiple supporting signals.

So **the research/engineering idea is good**.

---

# 2. But I would NOT call the component "social scraping"

That's the first thing I'd change.

Your system should say:

> **External Evidence Enrichment**

or:

> **Candidate-Authorized Professional Evidence**

rather than:

> Social-media scraping.

Why?

Because "scraping" implies:

```text
Find candidate
↓
crawl their accounts
↓
collect whatever is available
↓
feed everything into AI
```

That is exactly the direction that creates the biggest technical, privacy, and platform-policy problems.

---

# 3. LinkedIn is the biggest problem

I verified LinkedIn's current rules.

LinkedIn explicitly prohibits unauthorized automated scraping/copying of profiles and other service data. Its current User Agreement also prohibits bypassing access controls and unauthorized automated methods. ([LinkedIn][1])

More importantly for **your exact use case**, LinkedIn's API terms say that API content cannot be used as input to reports, scores, or decisions determining **employment eligibility**, unless LinkedIn expressly permits that in writing under separate terms. ([LinkedIn][2])

That means:

```text
LinkedIn profile
      ↓
API
      ↓
GADS candidate score
```

is not something I'd design as a normal production feature based on the current public LinkedIn API terms.

LinkedIn does have OAuth/member-authorization flows for approved API access, but authorization by the candidate does **not by itself remove the separate platform restriction on using LinkedIn content for employment decisions**. ([Microsoft Learn][3])

So I would **not build your research proposal around "we scrape LinkedIn and use it to score candidates."**

---

# 4. GitHub is more nuanced

GitHub is different.

GitHub's current terms distinguish scraping from API access, and public repository content is generally publicly accessible. But GitHub's terms also restrict using API data for spam, including selling personal information to recruiters/headhunters/job boards, and API use is governed by additional terms. ([GitHub Docs][4])

So I'd avoid:

```text
GADS
 ↓
mass search GitHub
 ↓
collect thousands of developers
 ↓
build candidate profiles automatically
```

Instead, use:

```text
Candidate
 ↓
provides GitHub account / repository
 ↓
candidate-authorized or explicitly submitted evidence
 ↓
GADS analyzes relevant technical artifacts
```

That's a much better architecture.

---

# 5. Your private-account concern is absolutely real

You correctly identified the main fairness problem:

Suppose:

### Candidate A

```text
Public GitHub
Public portfolio
Public LinkedIn
```

### Candidate B

```text
Private GitHub
Private LinkedIn
No portfolio
```

A badly designed system could conclude:

```text
A → lots of evidence
B → little evidence
```

and accidentally turn that into:

```text
A → better candidate
B → worse candidate
```

That is wrong.

The system must enforce:

> **Unavailable evidence is not negative evidence.**

This should be a hard rule in GADS.

For example:

```text
GitHub:
UNAVAILABLE

Meaning:
No observable evidence

NOT:
GitHub skill absent
```

This distinction should be encoded in your data model, not merely documented.

---

# 6. I would introduce three separate states

For every claim:

```text
SUPPORTED
```

```text
UNVERIFIED
```

```text
UNOBSERVABLE
```

Example:

```text
Node.js

SUPPORTED
Evidence:
GitHub + portfolio + assessment
```

versus:

```text
Node.js

UNVERIFIED
Evidence:
Resume self-claim only
```

versus:

```text
Node.js

UNOBSERVABLE
Evidence:
No external artifact supplied
```

And critically:

```text
UNOBSERVABLE ≠ FALSE
```

That's one of the strongest design principles you can add.

---

# 7. Don't make a public profile mandatory

This is where I would improve your concept substantially.

Instead of:

```text
Candidate
 ↓
System searches internet
 ↓
Find evidence
```

do:

```text
Candidate
 ↓
Claims skills
 ↓
GADS asks:
"Would you like to provide evidence?"
 ↓
Candidate selects evidence source
```

Possible options:

```text
GitHub
Portfolio
Technical blog
Project repository
Certificate
Project artifact
Work sample
Technical assessment
Employment verification
```

Now the candidate doesn't have to maintain a public digital identity.

---

# 8. This leads to a better concept: Evidence Equivalence

This is something I would add to your research design.

Suppose the claim is:

> **"Candidate knows Kubernetes."**

You should not say:

```text
GitHub = required
```

Instead define acceptable evidence classes:

```text
Kubernetes claim
        │
 ┌──────┼───────────┬────────────┐
 ↓      ↓           ↓            ↓
Code  Portfolio  Assessment  Work Artifact
```

All of them can potentially support the same claim.

So:

```text
GitHub unavailable
```

doesn't matter if:

```text
Technical assessment = strong
```

This makes your system much more inclusive.

---

# 9. The most important change: make the candidate the source selector

Instead of the system asking:

> "What information can I find about this person?"

ask:

> **"What professional evidence has this candidate chosen to submit for this claim?"**

That gives you:

```text
Candidate
   ↓
Claim
   ↓
Evidence selection
   ↓
Evidence verification
```

rather than:

```text
Candidate
   ↓
Internet surveillance
   ↓
AI interpretation
```

The first is far more aligned with your original **governed AI** philosophy.

---

# 10. I'd create an "Evidence Gateway"

Your architecture could now have:

```text
                    Evidence Gateway
                           │
       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
Candidate URL       Connected Source     Direct Upload
       │                   │                   │
    Portfolio            GitHub           Project ZIP
    Blog                 supported        Certificate
    Demo                 connector        Assessment
       │                   │                   │
       └───────────────────┼───────────────────┘
                           ↓
                    Evidence Processor
```

The gateway controls:

* what source is accessed
* why it is accessed
* what data fields are needed
* what the candidate authorized
* when it was accessed
* how long it is retained

That is much stronger than a crawler.

---

# 11. And it fits your earlier Evidence Ledger perfectly

Suppose candidate says:

> "3 years of Node.js."

Your ledger might contain:

```text
Claim:
Node.js experience

Source:
Candidate resume

External evidence:
Candidate-provided GitHub repository

Artifact:
ecommerce-api

Evidence:
Express + Node runtime + server code

Identity linkage:
Candidate-provided GitHub URL

Evidence type:
Artifact-backed

Strength:
Strong

Freshness:
Current

Verification:
Partial

Limitation:
Artifact does not independently establish
3 years of professional experience
```

That last field is **very important**.

Your system shouldn't overclaim what the evidence proves.

---

# 12. You should make claims more granular

Instead of:

```text
Claim:
"Knows Node.js"
```

break it into:

```text
C1: Has used Node.js
C2: Has built backend APIs with Node.js
C3: Has production experience with Node.js
C4: Has 3 years of Node.js experience
C5: Has advanced Node.js proficiency
```

Now different evidence can support different claims.

For example:

```text
GitHub
→ C1
→ C2

Employment record
→ C3
→ C4

Technical assessment
→ C5
```

That's much more defensible.

---

# 13. This also solves your earlier skill graph idea

Remember your:

```text
Express
 ↓
Node.js
 ↓
JavaScript
```

model?

Now combine it with external evidence:

```text
Candidate claims:
Express

GitHub evidence:
Express project

Skill graph:
Express
   ↓
Node.js
   ↓
JavaScript
```

But the system records:

```text
Express
→ direct artifact evidence

Node.js
→ dependency-based inference

JavaScript
→ lower-strength inference
```

So you don't suddenly get:

```text
JavaScript = VERIFIED
```

just because a Node repository exists.

---

# 14. Add "Evidence Conflict Detection"

This is one feature I'd definitely add.

Example:

```text
Resume:
Node.js — 4 years

Portfolio:
Node project — 2025

GitHub:
First Node repo — 2025

Candidate assessment:
Strong Node.js
```

The system should not blindly average everything.

Instead:

```text
CLAIM CONFLICT DETECTED

Claim:
4 years Node.js

Supported:
Node.js capability

Not established:
4 years of experience

Action:
Human review
```

That is extremely useful.

---

# 15. I would add `E_con` to your scoring model

Earlier we created:

```text
E_prov = Evidence Provenance
```

Now add:

```text
E_con = Evidence Concordance
```

Meaning:

> How consistently do independent sources support the same claim?

For example:

```text
Resume       ✓ Node.js
Portfolio    ✓ Node.js
GitHub       ✓ Node.js
Assessment   ✓ Node.js
```

High concordance.

Versus:

```text
Resume       ✓
Portfolio    ?
GitHub       ✗
Assessment   ?
```

Lower concordance → higher uncertainty.

This is much more interesting than simply adding more scraped data.

---

# 16. Add `I_link` too

There is another hidden problem:

> How do we know this GitHub account belongs to this candidate?

Don't just use:

```text
Name match = TRUE
```

Instead establish identity linkage using stronger signals:

```text
Candidate-provided GitHub URL
        ↓
Portfolio links to same account
        ↓
OAuth / authorized connection
        ↓
Cross-source consistency
```

Then:

```text
I_link = Identity Linkage Confidence
```

Your evidence pipeline can become:

```text
Evidence strength
×
Identity linkage
×
Relevance
×
Freshness
```

Conceptually.

---

# 17. There's another thing I would absolutely NOT collect

I wouldn't let your crawler/search engine wander through:

```text
Instagram
Facebook
personal X posts
TikTok
personal blogs
photos
political opinions
religion
relationships
personal lifestyle
family information
```

even when technically public.

Those have very weak relationships to professional competence and create huge risks of irrelevant personal characteristics entering an employment decision.

Keep the evidence scope narrow:

```text
Professional capability evidence
```

not:

```text
Everything the internet knows about the person
```

This also fits the privacy direction of India's DPDP framework. The DPDP Act establishes obligations around notice, consent, processing, access/correction/erasure, and related rights; the 2025 Rules require clear, standalone notice that specifies the personal data being processed and the purpose, with an accessible way to withdraw consent. The Rules are subject to phased commencement, so the exact enforceable requirements depend on the relevant provision's effective date. ([India Code][5])

As of September 2026, MeitY's official materials state that the Rules were notified on November 14, 2025, with an 18-month phased implementation timeline. ([MeitY][6])

---

# 18. So I'd change your original proposal from this

```text
Resume
 ↓
Scrape candidate socials
 ↓
Collect everything
 ↓
AI verification
 ↓
Score
```

to:

```text
Resume
 ↓
Candidate Claims
 ↓
Evidence Request
 ↓
Candidate-authorized / candidate-provided Sources
 ↓
Identity Linkage
 ↓
Claim-specific Evidence Extraction
 ↓
Evidence Authenticity
 ↓
Evidence Relevance
 ↓
Evidence Freshness
 ↓
Cross-source Consistency
 ↓
Skill Graph Inference
 ↓
Conflict Detection
 ↓
Uncertainty
 ↓
Policy
 ↓
M_G
```

This fits the rest of your GADS design much better.

---

# 19. And the private-account problem becomes an advantage

Rather than treating privacy as a weakness, make it part of the architecture.

Your system can say:

> **"GADS does not penalize a candidate for having private or unavailable professional records. It instead distinguishes absence of evidence from evidence of absence and provides alternative evidence channels."**

That is actually a strong research/design principle.

For example:

```text
                Candidate Claim
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
       External Evidence      No External Evidence
             │                   │
             ↓                   ↓
       Verified/Partial      Don't penalize
                                 │
                                 ↓
                        Optional verification
                                 │
              ┌──────────┬──────┼─────────┐
              ↓          ↓      ↓         ↓
          Assessment   Upload  Portfolio  Employer
```

That gives every candidate an opportunity to produce evidence.

---

# 20. One further improvement: don't force social evidence at all

I'd make the system **evidence-source agnostic**.

Your system shouldn't fundamentally care whether the evidence came from:

```text
GitHub
```

or:

```text
Portfolio
```

or:

```text
Technical challenge
```

or:

```text
Candidate's project artifact
```

or:

```text
Verified employment record
```

It should care about:

```text
What claim?
What evidence?
How strong?
Who owns it?
How relevant?
How recent?
How independently supported?
How uncertain?
```

That's the real intellectual core of your system.

---

# My verdict on your idea

**The underlying idea: strong.**

**"Scrape candidates' socials automatically": I would remove this from the architecture.**

**"Use candidate-authorized professional artifacts to strengthen claims": keep it.**

The distinction is especially important because LinkedIn's current API terms explicitly restrict using LinkedIn content for employment eligibility/scoring unless separately authorized in writing by LinkedIn. ([LinkedIn][2]) GitHub is more permissive for public content, but its API terms also impose restrictions relevant to recruiter/job-board use. ([GitHub Docs][4])

The version I'd put into the GADS architecture is:

> **External Evidence Enrichment Layer:** A candidate-authorized mechanism for supplying or connecting professional artifacts. The system performs claim-specific evidence extraction, identity linkage, provenance tracking, relevance assessment, freshness checks, cross-source consistency analysis, and conflict detection. Unavailable/private evidence is represented as *unobservable*, not as negative evidence, and candidates are offered alternative verification mechanisms such as technical assessments or submitted artifacts.

That is a much stronger proposal than **"we scrape LinkedIn/GitHub to verify resumes."** It also fits your existing **EvidenceLedger + Skill Graph + Adaptability + Role Blueprint + PBPQ** architecture instead of becoming a separate surveillance subsystem.

[1]: https://eu.linkedin.com/legal/user-agreement?utm_source=chatgpt.com "User Agreement | LinkedIn"
[2]: https://www.linkedin.com/legal/l/api-terms-of-use?utm_source=chatgpt.com "API Terms of Use"
[3]: https://learn.microsoft.com/en-us/linkedin/shared/authentication/authentication?utm_source=chatgpt.com "Authenticating with OAuth 2.0 Overview - LinkedIn | Microsoft Learn"
[4]: https://docs.github.com/en/site-policy/github-terms/github-terms-of-service?utm_source=chatgpt.com "GitHub Terms of Service - GitHub Docs"
[5]: https://www.indiacode.nic.in/bitstream/123456789/22037/1/a2023-22.pdf?utm_source=chatgpt.com "THE DIGITAL PERSONAL DATA PROTECTION ACT, 2023"
[6]: https://www.meity.gov.in/static/uploads/2026/04/46face7d48c8f6a97030f713ad5fdab4.pdf?utm_source=chatgpt.com "COVER _ Meity_2025-26.cdr"


Yes. After looking at the current research landscape, my honest assessment is:

> **This is absolutely strong enough to become a final-year minor project, and it can become a research paper/whitepaper—but only if you turn the architecture into a testable research contribution.**

Right now you have **a strong system concept**. You do not yet have a research paper. The difference is the **evaluation and the precise claim of novelty**.

There is also an important naming issue I discovered: a 2026 paper called **ACRE** explicitly describes its work as a “Governed AI Decision System” and proposes evidence extraction, contradiction detection, policy-bound reasoning, deterministic replay, and cryptographic auditability. So I would **not claim that “Governed AI Decision Systems” itself is your novel contribution**, and I would consider changing the public name/acronym of your project. ([SSRN][1])

## 1. As a B.Tech minor project: yes, definitely

For a final-year project, your architecture is actually more ambitious than necessary.

You could build a very good working prototype with:

```text
Resume
   ↓
LLM Claim Extraction
   ↓
Skill/Evidence Graph
   ↓
Evidence Verification
   ↓
OPA Policy Engine
   ↓
Uncertainty
   ↓
Governed Candidate Score
   ↓
Top-K Queue
   ↓
Human Review
```

with:

```text
FastAPI
PostgreSQL
OPA/Rego
React
LLM
Vector/embedding model
```

That is more than enough for a serious minor project.

The mistake would be trying to implement **every idea you've discussed**:

* social scraping
* GitHub analysis
* LinkedIn
* portfolio crawling
* skill graph
* adaptability
* perfect candidate
* dynamic benchmark
* PBPQ
* OPA
* audit trails
* cryptography
* fairness
* multi-agent LLMs
* technical assessments

all at once.

You would end up with a huge system whose individual components aren't evaluated properly.

---

# 2. As a research paper: yes, but there is a catch

There is already research proposing LLM-based resume-screening systems with multiple agents, extraction/evaluation/summarization, and external knowledge through RAG. So:

> **“We built an LLM-based resume screener” is not novel enough.**

For example, a 2025 paper proposes a context-aware, explainable multi-agent resume-screening framework with RAG and compares its outputs against HR ratings. ([arXiv][2])

There is also substantial current work examining bias in LLM-based resume screening. A June 2026 study evaluated 14 LLMs using paired resumes and found model-generation-dependent demographic effects, demonstrating that simply using a newer LLM does not remove the need for evaluation. ([arXiv][3])

And recent research on human-AI recruiting workflows suggests that even when recruiters remain formally responsible, AI can shape the information and criteria on which they base decisions, which makes meaningful human oversight an actual research issue rather than a checkbox. ([arXiv][4])

So you need a **specific contribution**.

---

# 3. I think I can see your strongest contribution now

After everything you've described, I would **not** make the primary contribution:

> "AI resume screening with governance."

That's too broad.

Instead, your core contribution should be something like:

> **A provenance-aware candidate representation that separates candidate claims, direct evidence, inferred skills, verified evidence, uncertainty, and policy eligibility before ranking candidates.**

That is much more specific.

Your central idea becomes:

```text
                    Candidate
                        │
             ┌──────────┴──────────┐
             ↓                     ↓
         Claims                Artifacts
             │                     │
             └──────────┬──────────┘
                        ↓
                 Evidence Graph
                        │
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
       Direct        Inferred       Conflict
       Evidence      Evidence      Detection
          │             │             │
          └─────────────┼─────────────┘
                        ↓
                    Uncertainty
                        ↓
                   Policy Gate
                        ↓
                    Candidate
                    Evaluation
```

That is much more researchable.

---

# 4. Your strongest idea is actually the distinction between evidence states

I think this should become the heart of the paper.

Instead of:

```text
Node.js = TRUE
```

your system produces:

```text
Node.js

CLAIMED
SUPPORTED
INFERRED
VERIFIED
ASSESSED
UNKNOWN
CONFLICTING
UNOBSERVABLE
```

For example:

```text
Candidate says:
"3 years Node.js"

        ↓

Resume
→ self-claim

GitHub
→ Node project evidence

Portfolio
→ Node backend project

Assessment
→ strong technical performance

        ↓

Conclusion

Node.js capability:
SUPPORTED / HIGH CONFIDENCE

"3 years":
NOT independently verified
```

That is a genuinely interesting research problem:

> **How should an AI decision system represent and reason over claims when different pieces of evidence have different provenance and reliability?**

That is far stronger than another resume-ranking system.

The importance of provenance and explicit sources for trustworthy AI systems is also well established in the broader AI literature. ([arXiv][5])

---

# 5. Your skill graph is your second strong contribution

Your:

```text
Express
 ↓
Node.js
 ↓
JavaScript
```

idea is useful, but you need to be careful about what inference is permitted.

For example:

```text
Express evidence
       ↓
Node.js inference        ✓ reasonable
       ↓
JavaScript inference     ✓ potentially reasonable
       ↓
Advanced JavaScript      ✗ not automatically justified
```

So the paper could formalize:

> **Bounded Skill Inference**

Something like:

```text
Evidence(target skill)
=
Source Evidence
× Relationship Strength
× Context Relevance
× Inference Depth Penalty
```

Not because that exact formula is inherently correct, but because you can **test competing formulations**.

That gives you an actual research question:

> Does bounded skill-graph inference reduce false-positive skill attribution compared with keyword or embedding-only matching?

Now you're doing research.

---

# 6. Your external evidence idea also fits—but differently

The external evidence layer should **not** be your headline contribution.

It should be an **evidence source**.

Think:

```text
                  Evidence Sources
                         │
       ┌─────────────────┼─────────────────┐
       ↓                 ↓                 ↓
Candidate Resume    Candidate Artifact   Assessment
       ↓                 ↓                 ↓
       └─────────────────┼─────────────────┘
                         ↓
                  Evidence Graph
```

GitHub/portfolio can be additional sources where permitted and appropriately authorized.

I would **not build the paper around automatically scraping LinkedIn or private social accounts**. Apart from privacy concerns, LinkedIn's current API terms impose specific restrictions around using LinkedIn API content for employment-eligibility decisions, so that would be a poor foundation for your core contribution. ([NIST][6])

Instead:

> **candidate-provided / candidate-authorized professional evidence**

is much cleaner.

And your rule:

```text
No accessible evidence
        ≠
Evidence that the skill is absent
```

could itself be part of the methodology.

---

# 7. Your adaptability idea should be secondary

I like your adaptability idea, but I wouldn't make:

> "AI predicts whether someone is enthusiastic about learning."

a research feature.

That gets problematic very quickly.

Instead define:

### Demonstrated Adaptability

Observable signals:

```text
technology transitions
cross-stack projects
learning a new framework
novel-technology assessment
successful unfamiliar-task completion
```

Then your candidate might have:

```text
Q_current = current demonstrated capability

G_adapt = demonstrated adaptation capability
```

That distinction is excellent.

You can experimentally ask:

> Does adding adaptability evidence improve identification of candidates who can perform well on unfamiliar technology tasks?

That's a legitimate research question.

---

# 8. Replace "perfect candidate" with "Role Blueprint"

I strongly recommend this.

Don't construct:

```text
Perfect Candidate
```

Construct:

```text
Role Blueprint
```

For example:

```text
Backend Developer

Hard:
Node.js
PostgreSQL

Core:
REST
Testing
Git

Preferred:
Docker
AWS

Adaptability:
Moderate importance

Additional Value:
Open source
Systems projects
Leadership
```

Now candidate comparison becomes:

```text
Candidate
     ↓
Role Blueprint
     ↓
Current capability
+
Evidence
+
Adaptability
+
Additional value
```

This avoids a major flaw in the “perfect resume” concept: candidates can be excellent in ways that weren't explicitly listed in the job description.

---

# 9. This is where your PBPQ becomes interesting

Don't use the queue merely as:

```text
Score candidates
↓
sort
↓
top 10
```

That's ordinary.

Your research question should instead be:

> **Can a policy-bounded priority queue maintain a stable, auditable Top-K shortlist while preventing unsupported or policy-ineligible candidates from entering it?**

Then test it.

For example:

```text
Candidate stream

A → score
B → score
C → score
D → score
...
```

Compare:

```text
Normal Top-K
vs
PBPQ
```

under:

* keyword stuffing
* false skill claims
* contradictory evidence
* missing evidence
* adversarial resumes
* different candidate arrival orders

Now you have something measurable.

---

# 10. The paper should have explicit hypotheses

I'd formulate something close to these.

### H1 — Evidence

> A provenance-aware evidence representation reduces unsupported skill assignments compared with resume-only LLM screening.

Measure:

```text
Claim verification precision
Claim verification recall
Unsupported-claim rate
```

### H2 — Skill graph

> Bounded skill-dependency inference improves recognition of related technologies while reducing over-inference compared with keyword-only and embedding-only approaches.

Measure:

```text
Inference precision
Inference recall
False inference rate
```

### H3 — Governance

> Deterministic policy adjudication eliminates policy inconsistencies present in LLM-only candidate decisions.

Measure:

```text
Policy violation rate
Decision reproducibility
```

OPA should theoretically make the rule layer deterministic, but you still need to test the complete pipeline because upstream extraction can remain wrong.

### H4 — Uncertainty

> Uncertainty-based abstention routes ambiguous cases for human review more effectively than always-forced automated decisions.

Measure:

```text
Abstention rate
Error rate among auto-decisions
Error rate among escalated cases
Calibration
```

### H5 — Evidence enrichment

> Adding candidate-authorized professional artifacts improves claim verification without treating unavailable public profiles as negative evidence.

Measure:

```text
Claim support accuracy
Evidence coverage
False-negative rate for candidates without public artifacts
```

That's already a research paper.

---

# 11. Your baseline is extremely important

This is the thing many student papers miss.

Don't only demonstrate:

> "GADS gives good results."

Compare it against progressively stronger baselines.

### Baseline 1

```text
Keyword matching
```

### Baseline 2

```text
Embedding similarity
```

### Baseline 3

```text
LLM resume scoring
```

### Baseline 4

```text
LLM + evidence
```

### Proposed

```text
LLM
+
Evidence Graph
+
Skill Inference
+
OPA
+
Uncertainty
+
PBPQ
```

Then perform an **ablation study**:

```text
Full GADS

remove provenance
remove skill graph
remove uncertainty
remove OPA
remove external evidence
remove adaptability
```

and measure what breaks.

That's where your paper gets much more convincing.

---

# 12. You need a dataset—but don't start with real people's private data

This is where I would keep the project manageable.

Build an experimental dataset containing:

```text
Job descriptions
+
synthetic candidate resumes
+
candidate claims
+
supporting artifacts
+
contradictory artifacts
+
missing evidence cases
```

For example:

```text
Candidate 001

Resume:
"3 years Node.js"

Evidence:
GitHub Node project
Portfolio
No employment verification

Ground truth:
Node capability = true
3-year duration = unsupported
```

You can deliberately generate controlled test cases.

You can also use public open-source technical artifacts where appropriate.

For a student research project, **controlled synthetic cases are actually useful** because you know the ground truth.

---

# 13. Your simulator becomes an experimental instrument

Your browser simulator isn't just a cool UI.

Turn it into part of your experiment.

Allow:

```text
Evidence corruption
Claim manipulation
Candidate arrival order
Missing evidence
Conflicting evidence
Role requirement changes
Skill graph changes
```

Then show:

```text
M_G
Top-K
Evidence provenance
Uncertainty
Policy violations
```

This makes your simulator a **research testbed** rather than merely a demo.

That is a significant upgrade.

---

# 14. Evaluation metrics I'd use

You should have a table roughly like:

| Dimension            | Metric                                 |
| -------------------- | -------------------------------------- |
| Claim extraction     | Precision / Recall / F1                |
| Evidence attribution | Evidence accuracy                      |
| Skill inference      | Precision / false-inference rate       |
| Uncertainty          | Calibration / Brier or ECE             |
| Policy               | Violation rate                         |
| Ranking              | NDCG / Kendall τ / Top-K precision     |
| Stability            | Ranking change under perturbation      |
| Human routing        | Auto-decision error vs escalation rate |
| Evidence enrichment  | Claim-support improvement              |
| Robustness           | Performance under adversarial claims   |

NIST's current AI RMF guidance strongly supports this style of research: it emphasizes test/evaluation/validation, explicit uncertainty measurement, comparison with baselines, human-oversight measurement, documentation, and ongoing monitoring. NIST's TEVV-Athlon initiative, released as a public draft in August 2026, is also explicitly about systematic evaluation of AI systems and real-world outcomes. ([NIST AI Resource Center][7])

---

# 15. You should also perform a fairness stress test

I would **not make fairness your primary contribution**, because that could become an entire paper itself.

But you should test:

```text
Same qualifications
+
different irrelevant personal attributes
```

and check:

```text
Does M_G change?
Does evidence strength change?
Does ranking change?
```

That's important because current research demonstrates that LLM hiring behavior can change across demographic dimensions and across model generations. ([arXiv][3])

Your system should explicitly avoid using:

```text
religion
politics
gender
ethnicity
photos
marital status
family
personal lifestyle
```

as evidence of professional capability.

---

# 16. What I would NOT put in the first paper

Don't attempt to prove:

> "GADS makes hiring fair."

You can't establish that from a student project.

Don't claim:

> "GADS finds the best candidates."

You would need a valid definition of "best" and strong real-world outcome data.

Don't claim:

> "Our M_G score measures candidate quality."

It measures your **defined evidence-governed match construct**.

Don't claim:

> "Our AI determines whether a candidate is adaptable."

At most:

> **"Our framework evaluates demonstrated evidence related to adaptability."**

These wording differences will make your paper much more scientifically defensible.

---

# 17. What the paper's contribution could actually be

I'd formulate the contribution as approximately:

> **We propose an evidence-governed framework for AI-assisted candidate assessment that represents candidate capabilities as provenance-linked claims rather than unqualified skill labels. The framework combines bounded skill-graph inference, claim-specific evidence evaluation, deterministic policy adjudication, uncertainty-aware abstention, and policy-bounded Top-K ranking.**

Then your experiments ask whether those components actually improve:

```text
accuracy
+
evidence attribution
+
robustness
+
calibration
+
auditability
```

That's a proper research proposition.

---

# 18. A potential paper structure

I'd structure it like:

### 1. Introduction

Problem:

```text
LLM resume screening
      ↓
hallucination
unsupported claims
opaque ranking
policy inconsistency
```

### 2. Related Work

Cover:

* AI resume screening
* LLM recruitment
* skill matching
* knowledge/skill graphs
* AI governance
* provenance
* human-AI decision making
* algorithmic hiring fairness

You already have strong current references to start this section. The literature includes recent LLM-based resume-screening frameworks as well as work examining fairness and human oversight. ([arXiv][2])

### 3. Problem Definition

Formalize:

```text
Candidate
Job
Claims
Evidence
Skill Graph
Policy
Uncertainty
Ranking
```

### 4. Proposed Framework

Your GADS architecture.

### 5. Claim-Evidence Graph

This should be one of the major sections.

### 6. Governed Match Metric

Define:

```text
Q_current
E_prov
E_con
G_adapt
A_extra
U_unc
P_hard
```

### 7. Policy-Bounded Priority Queue

Formal algorithm.

### 8. Experimental Setup

Datasets, baselines, tasks.

### 9. Results

Actual numbers.

### 10. Ablation

This is critical.

### 11. Failure Modes / Limitations

Absolutely include this.

### 12. Ethical / Privacy Considerations

Especially external evidence.

### 13. Conclusion

---

# 19. One thing I would rename

I wouldn't use:

> **Governed AI Decision Systems — GADS**

as the unique research title.

The term is already being used in current work, and the broader concept is not unique. ([SSRN][1])

You could instead name your actual framework around its distinctive contribution.

For example:

### **E-GADS**

**Evidence-Governed AI Decision Support for Candidate Assessment**

or:

### **EGCA**

**Evidence-Governed Candidate Assessment**

or something more research-specific:

### **PGCA**

**Provenance-Governed Candidate Assessment**

My preference conceptually would be something like:

> **Provenance-Governed Candidate Assessment: An Evidence-Graph and Policy-Bounded Framework for AI-Assisted Resume Screening**

That tells the reviewer what is actually novel.

---

# 20. My honest assessment

I'd put it this way:

| Version                                              | My assessment               |
| ---------------------------------------------------- | --------------------------- |
| Current idea as a concept                            | **Strong**                  |
| B.Tech minor project                                 | **Very suitable**           |
| B.Tech major/final project                           | **Very suitable if scoped** |
| Whitepaper                                           | **Definitely possible**     |
| Student research paper                               | **Yes, with experiments**   |
| Novelty as "AI resume screener"                      | **Weak**                    |
| Novelty as evidence/provenance/skill-graph framework | **Much stronger**           |
| Publication-quality today                            | **Not yet**                 |
| Potential after rigorous evaluation                  | **Realistic**               |

The critical point is:

> **Your project's novelty should not be “we use many technologies together.”**

A paper isn't strong because it contains:

```text
LLM + FastAPI + PostgreSQL + OPA + embeddings + GitHub + React
```

That's an engineering stack.

Your research contribution is the **method that determines what counts as evidence, how evidence propagates through a skill graph, how uncertainty affects automation, and how policy-constrained ranking behaves under adversarial or incomplete information.**

---

# 21. And there is actually a very good final-year-project scope

I'd make the first version only:

```text
                PROPOSED PROJECT
                       │
               Candidate Resume
                       │
                       ↓
                Claim Extraction
                       │
                       ↓
                Skill Evidence Graph
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
     Claimed        Inferred       Verified
        │              │              │
        └──────────────┼──────────────┘
                       ↓
                 Evidence Score
                       +
                 Role Blueprint
                       ↓
                  OPA Policies
                       ↓
                  Uncertainty
                       ↓
                    M_G
                       ↓
                     PBPQ
                       ↓
             Human Review Queue
```

Then make **GitHub/portfolio enrichment an optional experimental module**, not a mandatory component.

Adaptability can be a secondary module.

LinkedIn scraping should **not** be part of the core implementation.

---

## The research question I'd ultimately put at the center

I think this is the cleanest formulation of everything you've developed:

> **Can an evidence-governed candidate representation—combining provenance-linked claims, bounded skill inference, deterministic policy constraints, and uncertainty-aware human escalation—produce more reliable and auditable AI-assisted resume screening than conventional semantic or LLM-only ranking?**

Now you have:

**Input → Method → Baselines → Experiments → Metrics → Conclusion.**

That's the transformation from **“we have a cool project idea”** to **“we have a research project.”**

And one final point: don't be afraid to make the paper's conclusion negative in places. If your experiment shows that external evidence improves verification but decreases coverage, or that `M_G` improves auditability but doesn't improve ranking accuracy, **that is still a research result**. NIST's current guidance explicitly treats measurement of errors, uncertainty, human oversight, and limitations as part of trustworthy AI evaluation. ([NIST AI Resource Center][8])

**So yes: I would absolutely pursue this as your final-year minor project, with a paper in mind from day one—but I would narrow the implementation around the Claim → Evidence → Skill Graph → Policy → Uncertainty contribution rather than trying to build a complete automated recruiting company.**

[1]: https://papers.ssrn.com/sol3/Delivery.cfm/6151108.pdf?abstractid=6151108&mirid=1&utm_source=chatgpt.com "ACRE: A Cryptographically Verifiable Framework for Governed AI Decision Pipelines with Deterministic Replay by Emmanuel Dominic :: SSRN"
[2]: https://arxiv.org/abs/2504.02870?utm_source=chatgpt.com "AI Hiring with LLMs: A Context-Aware and Explainable Multi-Agent Framework for Resume Screening"
[3]: https://arxiv.org/abs/2606.28978?utm_source=chatgpt.com "Can LLMs Hire Fairly? Racial Bias in Resume Screening"
[4]: https://arxiv.org/abs/2604.26851?utm_source=chatgpt.com "Resume-ing Control: (Mis)Perceptions of Agency Around GenAI Use in Recruiting Workflows"
[5]: https://arxiv.org/pdf/2308.06374?utm_source=chatgpt.com "Large Language Models and Knowledge Graphs:"
[6]: https://www.nist.gov/itl/ai-risk-management-framework?utm_source=chatgpt.com "AI Risk Management Framework | NIST"
[7]: https://airc.nist.gov/airmf-resources/airmf/5-sec-core/?utm_source=chatgpt.com "AI RMF Core - AIRC"
[8]: https://airc.nist.gov/airmf-resources/playbook/measure/?utm_source=chatgpt.com "Measure - AIRC"
