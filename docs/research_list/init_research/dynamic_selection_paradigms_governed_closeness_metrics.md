# Dynamic Selection Paradigms & Governed Closeness Metrics in AI Decision Systems

## Executive Summary

This research note analyzes two primary candidate selection paradigms:
1. **Static Thresholding:** Applying a fixed parameter cutoff score ($S_{min}$) to select all qualifying applicants or fill a static quota.
2. **Dynamic "Role Model" Priority Queueing:** Maintaining a top-$K$ priority queue where the top candidate acts as a dynamic "Role Model" benchmark ($K_{max}$), updated continuously as superior candidates arrive during a streaming evaluation process.

This document demonstrates how combining streaming selection with **Policy-Bounded Admissibility** strengthens the selection pipeline against keyword gaming and prompt injection while formalizing a multi-tier **Governed Closeness Metric ($M_G$)**.

---

## 1. Theoretical Analysis of Selection Paradigms

```
PARADIGM A: Static Thresholding
[ Candidates ] ---> [ Fixed Cutoff S_min ] ---> [ Accept / Reject ]
* Problem: Vulnerable to score drift; early sub-optimal candidates lock capacity.

PARADIGM B: Dynamic "Role Model" Priority Queue
[ Candidate Stream ] ---> [ Evaluate against Role Model K_max ] ---> [ Insert into Top-K Queue ]
                                       │                                         │
                                       └─── Updates Benchmark if Score > K_max ──┘
```

### Comparative Evaluation

| Dimension | Paradigm A: Static Thresholding | Paradigm B: Dynamic "Role Model" Priority Queue |
| :--- | :--- | :--- |
| **Mathematical Basis** | Parametric Cutoff / $k$-Suppression | Optimal Stopping Theory / Streaming Max Priority Queue |
| **Adaptability** | Rigid; relies on historical calibration | Adaptive; continuously elevates standards as pool quality grows |
| **Order Dependency** | Low (Independent per applicant) | High (Streaming queue order matters for benchmark updates) |
| **Capacity Management** | Risk of under-filling or over-filling | Strictly bounded to top-$K$ candidates |
| **Vulnerability to Gaming** | Keyword gamers lock slots early | **False Role-Model Trap:** A fake 99% score corrupts future evaluations |

### Why Strategy B Supports Governed AI Decision Research

In real-world enterprise recruitment, applicants arrive sequentially over time (streaming data). Dynamic benchmark replacement ensures the hiring bar dynamically scales upward as higher-tier candidates apply. 

However, Strategy B introduces a critical risk: **The False Role-Model Trap**. If an adversarial candidate uses prompt injection or white-text keyword stuffing, a raw AI model will rate them 99% close. They will capture the "Role Model" spot, lowering evaluation precision or displacing valid candidates.

---

## 2. Solving the "Closeness/Perfection" Problem

Traditional ATS and AI screeners calculate closeness using vector cosine similarity:

$$\text{Sim}(R, J) = \frac{\vec{v}_R \cdot \vec{v}_J}{\|\vec{v}_R\| \|\vec{v}_J\|}$$

This measures lexical overlap and semantic proximity, but fails to distinguish between **authentic experience** and **crafted buzzwords**.

### The Governed Closeness Metric ($M_G$)

To prevent benchmark poisoning while keeping the dynamic priority queue intact, we replace raw vector similarity with a **Multi-Tier Governed Admissibility Distance Metric**:

$$M_G(C, J) = \mathbf{P}_{hard}(C, J) \times \left[ w_1 \cdot \mathcal{E}_{provenance}(C) + w_2 \cdot \mathcal{S}_{semantic}(C, J) - w_3 \cdot \mathcal{U}_{uncertainty}(C) \right]$$

```
+-----------------------------------------------------------------------------------+
|                        GOVERNED CLOSENESS SCORE ENGINE (M_G)                      |
|                                                                                   |
|  1. Boolean Policy Gate  ---> P_hard(C, J) ∈ {0, 1}   [Out-of-Model Rule Check]   |
|  2. Evidence Lineage    ---> E_provenance(C) ∈ [0, 1] [Verified Proof Ratio]      |
|  3. Semantic Alignment  ---> S_semantic(C, J) ∈ [0, 1] [Contextual Depth Match]   |
|  4. Uncertainty Penalty ---> U_uncertainty(C) ∈ [0, 1][Missing / Ambiguous Data]  |
+-----------------------------------------------------------------------------------+
```

### Component Breakdown

1. **Deterministic Boolean Gate ($\mathbf{P}_{hard} \in \{0, 1\}$):**
   - Evaluated **out-of-model** by a rule engine (e.g., Open Policy Agent).
   - If a candidate lacks a non-negotiable requirement (e.g., mandatory security license or degree), $\mathbf{P}_{hard} = 0$. The overall score instantly drops to $0\%$, blocking entry into the priority queue regardless of keyword match.

2. **Evidence Provenance Index ($\mathcal{E}_{provenance} \in [0, 1]$):**
   - Measures the proportion of claimed skills backed by verifiable source documents (e.g., verified employment dates, GitHub repositories, or transcripts).

3. **Contextual Semantic Alignment ($\mathcal{S}_{semantic} \in [0, 1]$):**
   - Evaluates natural language depth and contextual skill relevance computed **only on verified claims**.

4. **Epistemic Uncertainty Penalty ($\mathcal{U}_{uncertainty} \in [0, 1]$):**
   - Penalizes candidates with incomplete information, unverified gaps, or high model ambiguity.

---

## 3. The Policy-Bounded Priority Queue (PBPQ) Algorithm

By combining $M_G$ scoring with dynamic benchmarking, candidate streaming becomes secure against wordplay and prompt injection.

```python
class GovernedCandidateQueue:
    def __init__(self, capacity_k: int):
        self.capacity = capacity_k
        self.queue = []  # Priority Queue sorted by M_G score
        self.role_model = None

    def process_application(self, candidate, job_policy):
        # Step 1: Evaluate Deterministic Hard Constraints (Out-of-Model)
        p_hard = evaluate_opa_policy(candidate, job_policy.hard_constraints)
        if p_hard == 0:
            return "REJECTED_POLICY_VIOLATION"

        # Step 2: Compute Governed Closeness Score (M_G)
        e_prov = compute_evidence_provenance(candidate)
        s_sem = compute_verified_semantic_match(candidate, job_policy)
        u_unc = compute_epistemic_uncertainty(candidate)
        
        m_g_score = p_hard * (0.4 * e_prov + 0.5 * s_sem - 0.1 * u_unc)

        candidate_record = {
            "candidate_id": candidate.id,
            "m_g_score": m_g_score,
            "evidence_ledger": e_prov
        }

        # Step 3: Insert into Top-K Priority Queue
        self.queue.append(candidate_record)
        self.queue.sort(key=lambda x: x["m_g_score"], reverse=True)

        # Truncate to capacity K
        if len(self.queue) > self.capacity:
            evicted = self.queue.pop()

        # Step 4: Update Dynamic Role Model Benchmark
        self.role_model = self.queue[0]
        return "ADMITTED_TO_QUEUE"
```

---

## 4. Integration into Future Research Blueprint

To preserve these concepts for publication and system implementation, this framework introduces the following formal contributions:

1. **Formulation of the Policy-Bounded Priority Queue (PBPQ):** Bridges streaming algorithms in computer science with AI safety and runtime governance.
2. **De-coupling Selection Logic from Model Embeddings:** Proves that dynamic role-model selection can operate safely when gated by deterministic policies ($\mathbf{P}_{hard}$) and evidence provenance ($\mathcal{E}_{provenance}$).
3. **Formal Metric ($M_G$) for AI Decision Benchmarking:** Provides a mathematically defensible alternative to raw LLM preference scores in human resources and automated decision systems.