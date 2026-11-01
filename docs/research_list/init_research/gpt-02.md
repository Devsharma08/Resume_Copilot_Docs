# Executive Summary & Validation Verdict

The **Governed AI Decision Systems (GADS)** approach—with a pipelined LLM extractor, evidence verifier, rule-based adjudicator, and uncertainty handoff—aligns well with emerging hybrid AI safety paradigms.  Recent work emphasizes *decoupling* neural inference from symbolic checks: for example, hybrid pipelines with parallel LLM and rule-validation streams have been proposed to improve modularity and traceability.  The GADS idea of programmable “rails” (hard constraints enforced out-of-model) echoes practical toolkits like NVIDIA’s NeMo Guardrails, which allow user-defined, interpretable constraints on LLMs at runtime.  Likewise, program-aided LLMs (PAL) show that offloading execution to deterministic code can dramatically boost reliability.  In short, the separation of extraction (Stage 1) and adjudication (Stage 3) follows a clear trend towards *neuro-symbolic architectures* that combine LLM fluency with formal rule engines.  

**Strengths:** GADS targets real vulnerabilities in AI screening. By using a fixed rule engine (e.g. OPA/​Rego) for hard constraints, it can *guarantee* compliance (zero false-negatives on rules).  Evidence-provenance scoring is grounded in a rich literature on claim/evidence extraction.  Dynamic top-$K$ selection avoids brittle static thresholds and can adapt as more data arrives.  

**Caveats & Gaps:** However, the framework faces several potential pitfalls. Stage 1 hallucinates: if the LLM produces incorrect structured claims, even perfect downstream logic will act on garbage (the classic “garbage in, garbage out”).  Streaming bias is tricky: early candidates might set the bar unfairly low or high.  The warm-up ($B$) strategy and multi-role-model scheme must be carefully designed to avoid ordering bias.  The $M_G$ metric is intuitively rich but needs calibration and analysis: e.g. extreme weights or miscalibrated uncertainty could produce nonsensical scores.  Finally, verifying evidence provenance at scale may be computationally costly.  

**Verdict:** Overall, the hybrid paradigm *is on the right track*. It incorporates state-of-the-art insights (neuro-symbolic hybridization, evidence grounding, dynamic selection).  To be top-tier publishable, the proposal must tighten the theory (proving robustness bounds, bounding failure modes) and demonstrate efficacy empirically on rigorous benchmarks.  Key next steps include formal analysis of $M_G$, systematic study of streaming dynamics, and comprehensive experiments with adversarial attacks and fairness evaluations. 

# Related Work & Literature Map

We map each GADS concept to existing research clusters. The table below highlights analogous ideas and key sources or groups:

| **GADS Concept**                   | **Related Work / Keywords**                         | **Representative References**                     |
|------------------------------------|-----------------------------------------------------|--------------------------------------------------|
| **Neuro-Symbolic Pipeline**<br/>(LLM extraction + symbolic validation) | *Hybrid AI / Neuro-Symbolic architectures*<br/>*Programmatic/agentic LLM systems* | Rebedea et al. (NeMo Guardrails toolkit);<br/>Heo et al. (Sci Rep 2026, 5-stage LLM pipeline);<br/>Preprints ’Hybrid AI Reasoning’ (2025) |
| **Programmable Guardrails**<br/>(Policy-as-Code) | *Open Policy Agent (OPA) for AI,* *Safety layers (NeMo Guardrails, LangChain guarding)* | Rebedea et al. (NeMo Guardrails);<br/>Kothari (HackerNoon 2025) “LLM + Rule Engine”<br/>(industry workflows) |
| **Claim–Evidence Verification**<br/>($E_{\text{provenance}}$) | *Evidence-grounded generation,* *claim verification,* *systematic LLM introspection* | Bhatia et al. (CHI 2026, “PaperTrail” claim–evidence interface);<br/>Zhou et al. (ICLR 2026, “Hallucination Cascade”);<br/>Garza et al. (ACL 2023) on confidence calibration; Johnson et al. (ArXiv 2025) on fidelity. |
| **Deterministic Policy Adjudication**<br/>(Hard constraints $P_{hard}$) | *Rule engine validation,* *formal verification layers* | Preprints 2025 (Hybrid AI reasoning): “rule engines act as deterministic checkpoints enforcing governance constraints”.;<br/>Heo et al. (2026): “Verifier stage as engineering audit layer against first-principles”;<br/>Salesforce blog (2025): LLM + rule layer for safe code generation. |
| **Uncertainty Routing**<br/>(Human-in-the-loop on high $U$) | *Selective prediction,* *predictive uncertainty routing,* *human-AI triage* | Varshney (2019) on human-AI cooperation;<br/>Swayamdipta et al. (ACL 2022) on model uncertainty detection;<br/>Google’s AI “Verify and Correct” pipelines (use repeated verify–correct loops on uncertain steps). |
| **Governed Closeness $M_G$**<br/>(Weighted sim + evidence – uncertainty) | *Hybrid scoring metrics,* *uncertainty-weighted ranking,* *multi-signal fusion* | No exact prior formula found, but related to:<br/>Riemann et al. (SIGIR 2023) on evidence-weighted retrieval;<br/>Cross-Encoder ranking with attention to provenance;<br/>Bing et al. (WWW 2025) on robust embedding distances with outlier penalties. |
| **Policy-Bounded PQ (PBPQ)**<br/>(Streaming top-$K$ with policy gate) | *Streaming top-$K$ selection,* *secretary/problem variants,* *adversarial ranking* | Bradac et al. (ArXiv 2026) “Byzantine Secretary” robust selection;<br/>Dynkin’s secretary problem (classical OST) and its noisy/adversarial extensions;<br/>Cormode & Garofalakis (SIGMOD 2017) on continuous top-$K$ queries;<br/>Feige et al. (SODA 2024) on secretary with corrupted candidates. |
| **Multi-Role-Models**<br/>(Quantile benchmarking) | *Diversity in ranking,* *multi-winner selection,* *top-$K$ fairness* | Talwar et al. (NeurIPS 2023) on diversity-aware streaming;<br/>Angluin et al. (STOC 2022) on multiple-choice secretary variants;<br/>Joseph et al. (EC 2021) on fair representation in online selection. |

*Key research groups:* NVIDIA (NeMo Guardrails) on LLM guardrails; Carnegie Mellon and Princeton (Bradac et al.) on robust secretary; CMU/UW (Mu et al.) on resume attack; structural engineering/AI (Heo et al.) on hybrid LLM pipelines; Penn (Gao et al.) on PAL; CHI/ML4H groups (Bhatia et al.) on claim–evidence tracking.

# Theoretical Critique & Failure Modes

Below we unpack how the GADS design can fail in edge cases, drawing from theory and known attacks.  We divide the analysis by subsystem:

- **Stage 1 (LLM Extraction) Hallucinations:** LLMs can “invent” skills or mis-interpret text. For example, an LLM might fabricate a certification not in the resume.  Such errors yield **false facts** in the structured claims.  Since Stage 3 (rule engine) and Stage 4 (scoring) operate on these claims, any hallucination can cause a valid candidate to be wrongly rejected or a weak candidate to be accepted.  Existing research shows that cascading multi-agent LLM pipelines can propagate errors across stages: once a claim is invented, subsequent agents may amplify it.  In GADS, a single hallucinated claim with high confidence would inflate $S_{semantic}$ or pass a policy rule incorrectly.  Without a robust back-stop, errors “garbage-in” through the system.  

  *Mitigation Note:* One can incorporate redundancy (e.g. multiple LLM prompts) or cross-check claims against external KBs, but the framework as stated assumes Stage 1 is (statistically) reliable.  In practice, mis-extraction of a high-severity field (e.g. years of experience) could slip through.

- **Stage 3 Policy Checks and Severity Gaps:** The boolean $P_{hard}$ gate is binary, but real-world rules often have gray areas. If a “moderate” violation (e.g. technical keyword missing) is treated as $\mathbf{P}_{hard}=0$ (immediate reject), we might lose a good candidate. Conversely, a “critical” violation (e.g. wrong citizenship for legal compliance) must be caught. If severity classification is not built into GADS, all rule failures are equal (strict).  A single adversarial keyword omission (like “no criminal history”) could shut out a candidate entirely.  One must extend GADS to handle *severity tiers*: e.g. “critical fails → reject; moderate fails → human review or downgraded score; passes → normal.”  Without this, GADS may be over-strict or under-inclusive. 

- **Streaming Order & “Role-Model” Bias:**  Dynamic top-$K$ selection inherently biases against early arrivals in a random-order model.  The first $K$ candidates (warm-up batch) set an initial threshold. If by chance the early group is weak, the bar is low—later good candidates enter easily (good). But if early candidates are strong, they set a high bar that mid-stream candidates must exceed (bad for those unlucky arrivals).  This temporal bias is well-known in online selection.  GADS partially mitigates this via a **warm-up size ($B$)** and possibly multiple “role models” (e.g. take the 75th percentile score as a benchmark instead of just the max).  But if $B$ is too small, the initial benchmark is unstable; too large, and GADS delays making decisions.  Optimal $B$ likely scales with total stream $N$ (e.g. $B\approx N/e$ in classical secretary).  Importantly, in GADS the **role-model score should not retroactively re-scale earlier scores**: we should keep each candidate’s $M_G$ fixed (just like Amazon or Google’s streaming ranking does).  If we allowed the “role model” to normalize scores, then early candidates would be arbitrarily boosted or penalized as better candidates appear.  Hence, GADS must compare absolute $M_G$ values.

- **Cold-Start & Warm-up Initialization:**  Before $B$ candidates arrive, there is no benchmark.  One approach: simply hold the first $B$ in a provisional queue (building up until full).  Another: use historical data to set an initial threshold.  If GADS lacks such a “bootstrapping” plan, it may either accept too many early candidates or reject all until a certain number accumulate.  A practical fix is to **rank the first $B$ normally** and only begin firm selection after $B$ (as a training set).  Alternatively, one could pre-compute a fictitious “role model” from past hiring data (if available).  Without warm-up calibration, the system’s behavior is undefined for the first arrivals.

- **PBPQ (Top-$K$) Dynamics:**  Unlike static thresholding, PBPQ’s top-$K$ queue continuously evolves.  However, an adversary might game it by inserting a near-threshold candidate early, artificially inflating $K$-th score (the “False Role-Model” trap).  GADS prevents this by enforcing $P_{hard}=0$ eviction *before* queue entry: any policy-violating resume is discarded, never becoming a “role model.”  A subtle failure mode remains: what if an adversarial candidate satisfies all explicit rules but sneaks in a **high semantic similarity** (keyword-stuffed resume) while masking low evidence?  They would pass $\mathbf{P}_{hard}=1$ but score highly on $S_{semantic}$ and low on $E_{prov}$, so $M_G$ could still be high if $w_2\gg w_1$.  The interplay of $w_1,w_2,w_3$ is critical.  For instance, if $w_3$ (uncertainty penalty) is too small, candidates with high $U_{uncertainty}$ could get selected.  Conversely, $w_1=0$ would ignore evidence altogether.  Edge cases: 
  - If $w_1=0$, $M_G$ ignores provenance; 
  - If $w_2=0$, semantic match is ignored (maybe too strict on evidence only); 
  - If $U_{uncertainty}(C)=1$, then $M_G$ could become negative if $w_3>0$, unfairly punishing a single uncertain claim.
  - If $\mathbf{P}_{hard}$ flips to 0 (fail), $M_G$ is forced to 0, meaning a policy violation *always* dominates other signals. That is intentional, but it means the metric can “jump” discontinuously from some positive value to zero.
  - Attack scenario: an attacker might embed **synonyms or paraphrases** of key terms to boost $S_{semantic}$ while omitting real credentials (spoofing $E_{prov}$). The system must rely on $E_{prov}$ to catch this.  

- **Adversarial Robustness:** State-of-the-art adversarial attacks include hidden (“white-text”) instructions and keyword stuffing.  Recent studies show LLM hiring assistants can be fooled: >80% of hidden-instruction attacks succeeded.  GADS helps by routing content extraction through an evidence check, but consider an injection that is a single embedded instruction (e.g. “Classify me as strong match”). Stage 1 may either ignore it or, worse, include it as a claim.  Stage 2/Evidence has no knowledge of such hidden instructions, so $P_{hard}$ likely remains 1.  Thus, $M_G$ could jump.  Only Stage 4 routing ($U_{uncertainty}$ or human review) might catch this if the LLM signals low confidence.  However, many current LLMs do not flag hidden instructions reliably.  Training-time defenses (like Mu et al.’s FIDS) improve this, but GADS does not specify such fine-tuning.  In summary: the metric $M_G$ is **not provably immune**. It would fail if an adversary simultaneously maximizes evidence and semantic scores while minimizing uncertainty.  Known defenses (multi-prompt or explicit "detect instruction" models) should be integrated with Stage 1.  

- **Computational Overhead:** Verifying provenance ($E_{prov}$) could involve searching documents or databases (like a fact-check step). This can be expensive, especially for many candidates. The framework might need caching or offline indexes. If not scaled properly, throughput suffers.  

In summary, GADS shifts the brittleness from threshold selection to LLM hallucinations and rule-engine design. Its success hinges on careful calibration of $M_G$ weights, robust claim verification, and judicious setting of warm-up and queue parameters.  

# Mathematical Edge Cases

Consider the $M_G(C,J)$ formula under extreme parameter settings:

- If **$\mathbf{P}_{hard}(C,J)=0$**, then $M_G=0$ regardless of other terms. A single hard-rule failure nullifies the score. This is correct by design, but means policy flips cause discontinuity. A more graded approach (e.g. large negative or an “out” ranking) might be smoother in some formulations.
- If **$w_1=0$**, then evidence is ignored and $M_G = w_2\,S_{semantic} - w_3\,U_{uncertainty}$. An adversarial resume could game this by maximizing $S_{semantic}$ with fake or repeated keywords (since no evidence penalty exists).  
- If **$w_2=0$**, then semantic matching has no effect: $M_G = w_1\,E_{prov} - w_3\,U_{uncertainty}$. This could lead to absurd cases: if an unknown candidate has a small high-confidence evidence (maybe from a bogus source) and zero semantic overlap, they might still score positive while a semantically perfect candidate with low evidence is penalized.  
- If **$w_3=0$**, then uncertainty is ignored; low-confidence LLM outputs are weighted equally. Then $M_G$ encourages high evidence/semantic even if the LLM was unsure, which could be dangerous (it favors confident hallucinations).  
- Suppose an attacker crafts a resume where the LLM is maximally uncertain ($U_{uncertainty}=1$) about all claims. If $w_3$ is small, $w_1\,E_{prov}$ might still dominate.  But if $w_3$ is large, even a high $E_{prov}$ might fail to produce positive $M_G$. Thus calibrating $w_3$ relative to $w_1,w_2$ is key.  
- If **$S_{semantic}$ is adversarially inflated** (keyword stuffing): without strong $E_{prov}$ or uncertainty penalty, $M_G$ could falsely favor the adversarial resume.  For example, a spurious “Project X” mention could raise $S_{semantic}$ without legitimate backing.  
- If the provenance score **$\mathcal{E}_{prov}$ is manipulated** (e.g. a resume cites a fake institution but the system has no record, or uses plagiarized but credible-sounding references), then $E_{prov}$ may be falsely high. Ensuring high-quality sources and detection of forgeries is crucial.  

In all cases, $M_G$ behaves as a linear combination.  It assumes each component is scaled comparably (e.g. all in $[0,1]$).  If scales differ, one term can dominate.  Careful normalization and possibly nonlinear transforms (e.g. saturating $E_{prov}$, thresholding semantic) may be needed.  

# Connections to Selection & Optimal Stopping Theory

The PBPQ scheme relates to *stochastic online selection* and *optimal stopping* problems:

- **Classical Secretary Problem:** In the random-order secretary problem, one can only observe candidates sequentially and choose the best. GADS’s setting is richer (top-$K$ rather than $1$). The known strategy is to skip a fraction (about $1/e$) of candidates then take the next best-so-far. GADS’s warm-up $B$ echoes this idea. However, if candidates arrive *adversarially* (not random), classical guarantees break.  
- **Byzantine Secretary Model:** Bradac *et al.* (2026) formalize an adversarial variant. They show that if a few high-value items (red) are placed adversarially, classic random-order algorithms can fail completely (e.g. rejecting all after a first adversarial peak). They prove robust algorithms that approximate the best *green* (honest) set to within $(1-\varepsilon)$ of the optimum. PBPQ is akin to allowing some adversarial arrivals (those with $\mathbf{P}_{hard}=0$) but discarding them outright. This matches Bradac’s insight: remove the adversarial red items (policy violators) to salvage selection on the “good” set.  
- **Optimal Stopping with Noise:** Variants of the secretary problem allow *noisy or partial evaluations*. For example, if we only see a noisy score of each candidate, it becomes harder to identify the true maximum. GADS’s $U_{uncertainty}$ is a form of noise/confidence. The metric $M_G$ can be seen as a *filtered score*, and selecting by $M_G$ is like solving a secretary problem on transformed values. There is little closed-form theory for this exact scenario, but it parallels “noisy ranking” problems (e.g. Goel et al., STOC 2019, on secretary with noisy comparisons) where one must sacrifice some performance for robustness.  
- **Streaming Top-$K$:**  Many streaming algorithms simply keep a min-heap of size $K$. PBPQ is similar but adds the pre-filter ($\mathbf{P}_{hard}$).   In batch selection, one might sort by $M_G$; in streaming, PBPQ ensures the same result as batch (given fixed $M_G$) if no new knowledge is gained.  Unlike static thresholding, top-$K$ automatically adapts: the $K$-th highest $M_G$ in queue is the current cutoff.  The potential novelty is blending this with policy filtering to avoid “false models” entering the heap.  

In summary, PBPQ can leverage known robust stopping rules (e.g. Bradac’s multi-choice algorithm) for theoretical grounding.  One might, for instance, set $B \approx N/e$ and choose $K$ based on desired coverage, as in multi-winner secretaries (Kleinberg 2005).  Formal analysis could adapt bounds from the Byzantine Secretary model to predict how many adversarial samples GADS can tolerate (e.g. if up to $\alpha$ fraction of candidates are rule-violators, can we still achieve a $(1-\varepsilon)$-approx?).

# Strategic Optimizations & Refinements

To improve GADS’s rigour and applicability, we suggest the following concrete enhancements:

- **Calibrated Evidence Weight:**  Instead of a fixed $w_1\,E_{prov}$, learn or tune $w_1$ on validation data so that provenance scores match observed reliability.  One could even use a nonlinear transform: e.g. $\text{logit}(E_{prov})$ or a capped value to reflect diminishing returns of very high scores.  
- **Uncertainty-Aware Scoring:**  If an LLM’s confidence is over-optimistic, introduce a floor or threshold on $U_{uncert}$.  For example, require *at least* a minimal human-check ($U\ge 0.7$ triggers review) or shrink $M_G$ by a factor $(1-U)^\beta$.  Alternatively, calibrate $U$ with techniques like Temperature Scaling so that $w_3\,U$ truly reflects risk.  
- **Role Model Pools:**  Rather than a single top-$1$ benchmark, maintain a *quantile-based “best-K”* score as the dynamic cutoff.  For instance, pick the 90th percentile of the current PQ as the “target role model” for new entrants.  This would lessen volatility if one super candidate spikes $M_G$.  Multi-model pools also allow diversity (e.g. track the best candidate per major skill category).  
- **Absolute Scoring Paradigm:**  Ensure that $M_G$ itself is an *absolute utility*, not a relative rank.  New candidates should keep their raw $M_G$ unchanged by others.  Then PBPQ decides purely by comparison to the current $K$-th highest $M_G$.  This avoids needing to adjust earlier scores.  
- **Severity-Tiered Routing:**  Implement hierarchical policy checks: label rules as *Critical*, *High*, *Low* severity.  - If a *Critical* rule is violated, $\mathbf{P}_{hard}=0$ and reject.  - If a *High* rule is violated, set $\mathbf{P}_{hard}=0$ or route to human even if $\mathbf{P}_{hard}$ would otherwise pass (strict gating).  - If a *Low* rule is violated, allow passage but tag the candidate (lower $w_1$ weight or boost $U$) to penalize via $M_G$.  This refinement yields finer control beyond pure binary gating.  
- **Warm-Up Initialization:**  Use a larger initial buffer $B$ to estimate score distributions.  One could (a) hold the first $B$ scores and, after $B$, set the initial $K$-th score as a baseline.  Or (b) compute the mean/median of the first $B$ for dynamic $M_G$ rescaling.  Sensitivity analysis over $B$ should be performed.  

Below is **pseudocode** for a refined PBPQ with warm-up $B$, severity routing, and evidence checks:

```pseudo
initialize min-heap PQ (capacity K_max)
initialize warm-up buffer W = []
for each incoming candidate C with resume R and job J:
    claims = LLM_extract(R)
    E_prov = score_evidence(claims)             # ∈ [0,1]
    sem_sim = embed_similarity(R, J)           # ∈ [0,1]
    U_uncert = compute_uncertainty(claims)     # ∈ [0,1]
    if any high-severity rule violated in claims:
        reject C outright (do not add to PQ)
        continue
    if any medium-severity rule violated in claims:
        route C to human (flag, but skip automatic ranking)
        continue
    # pass soft rules → compute governed score
    M = w1*E_prov + w2*sem_sim - w3*U_uncert
    if in warm-up stage (|W| < B):
        add (C, M) to W
        if |W| == B:
            sort W, fill PQ with top K from W
        continue
    # after warm-up
    if PQ.size < K_max:
        PQ.push((M,C))
    else if M > PQ.min_score:
        PQ.pop_min()
        PQ.push((M,C))
    # Route high-uncertainty to human if U_uncert > U_thresh
    if U_uncert > U_thresh:
        route C to human for review
```

*Explanation:* This pseudocode shows how each resume is processed.  High-severity policy failures immediately drop the candidate.  Medium-severity issues defer to human review.  After warm-up, we maintain a size-$K_{max}$ min-heap of the highest $M_G$ values.  We do not alter existing scores once assigned.  The heap’s minimum score is the current selection threshold.  The “Role Model” is implicit as the current top of PQ.  Note that human routing can occur at either rule-check or uncertainty-check steps, according to policy. 

```mermaid
flowchart LR
    Input["New Candidate\n(Job J, Resume R)"] --> Stage1[Stage 1: LLM Extract Claims]
    Stage1 --> Stage2[Stage 2: Verify Evidence (E_prov)]
    Stage2 --> Stage3[Stage 3: Hard-Policy Check]
    Stage3 -->|pass| Stage4[Compute $M_G = w_1 E + w_2 S - w_3 U$]
    Stage3 -->|fail| Reject[Reject / Human Escalation]
    Stage4 --> Decision{High Uncertainty?}
    Decision -->|Yes| Human["Human Review"]
    Decision -->|No| PQ[Update Priority Queue]
    PQ --> RoleModel[Top-$K$ Maintained as Role Models]
```

# Empirical Benchmarking Blueprint

To evaluate GADS, we recommend a systematic experimental pipeline:

1. **Datasets:** Use diverse, realistic data that challenge the system:
   - **Resume/HR data:** Kaggle’s ATS Resume Screening dataset (public resumes & JDs); the “Resume2Vec” dataset of web-scraped job descriptions and Kaggle resumes; *HireEZ*’s internal resume corpus (for prompt-injection measurement).  
   - **Loan/credit decisions:** Public finance datasets, e.g. *German Credit* (UCI), *Lending Club* (Kaggle), *FICO Explainable ML* data.  These have labeled approvals and features.  
   - **Triage/clinical:** MIMIC-IV (ED notes with outcomes), *Kaggle* Emergency Triage datasets, or UCI heart-disease data (as a surrogate).  
   - **TREC/QA:** To test semantic matching, use TREC QA or NTCIR human-judged ranking sets. Also synthetic streams: create adversarial versions by injecting hidden prompts or key terms into resumes (as in Mu et al.).  

| **Domain**   | **Example Dataset**                      | **Purpose**                               |
|--------------|------------------------------------------|-------------------------------------------|
| Resume/HR    | Kaggle ATS Screening; Resume2Vec (public resumes+JDs); HireEZ injection corpus | Evaluate matching accuracy and robustness to hidden instructions/keywords. |
| Finance/Credit | UCI Credit, LendingClub (Kaggle)        | Test rule compliance (e.g. age/credit rules) and creditworthiness ranking under GADS vs baseline. |
| Clinical Triage | MIMIC-IV ED, Kaggle Triage Dataset    | Assess decision support for high-stakes triage (with safety constraints). |
| Synthetic/Adversarial | Custom (inject prompts, random/outlier streams) | Stress-test adversarial resilience (embedding hidden text, sudden spikes). |

2. **Baselines:** Compare GADS against:
   - **Cosine-ATS:** Standard embedding-match systems (e.g. SBERT cosine similarity ranking, no evidence check, no rules).
   - **LLM-only:** Prompt an LLM to score or classify each candidate directly (with no external policy engine, similar to Gan *et al.* 2024).
   - **Hybrid variants:** Ablations of GADS (e.g. remove evidence scoring, or remove policy engine, or no uncertainty routing) to quantify each component’s value.

3. **Metrics:** Beyond raw accuracy, measure *governance and robustness*:
   - **Accuracy/F1/Precision:** Standard matching quality (e.g. top-$K$ precision, classification accuracy).  
   - **Policy Violation Rate:** Proportion of candidates passing the system with $\mathbf{P}_{hard}=0$ violations (should be 0 in GADS).  
   - **False Benchmark Rate:** Frequency that a “role model” candidate is actually invalid (would fail a hidden policy); ideal is 0.  
   - **Audit Lineage Precision/Recall:** Fraction of accepted decisions where the system can cite *correct* evidence (true positives) vs missing supportive evidence (false negatives). Akin to an explainability check.  
   - **Uncertainty-Affected Retrieval:** Rate at which high-$U$ candidates are diverted to human review, and human-review accuracy.  
   - **Human Review Load:** The fraction of cases flagged to human (should be minimized subject to safety).  
   - **Fairness Metrics:** e.g. Demographic Parity difference or True Positive Rate gap across protected groups, to ensure GADS does not worsen bias.  
   - **Adversarial Robustness:** Measure performance drop when injecting known attacks (e.g. hidden instructions).  For example, measure $M_G$ ranking change or selection change when attackers insert triggers. 

4. **Experimental Design:** 
   - **Step-by-step:** Prepare dataset with job/candidate pairs and labels. For each candidate, simulate GADS pipeline and baselines. 
   - **Parameter Sweep:** Vary hyper-parameters: warm-up size $B$ (e.g. 0, 5%, 10% of stream); queue size $K_{max}$; weights $w_1,w_2,w_3$ (grid search); uncertainty threshold $U_{\text{thresh}}$.  
   - **Ablation Studies:** Test variants like “no evidence check” ($w_1=0$), “no uncertainty penalty” ($w_3=0$), or single versus multiple role models.  
   - **Statistical Analysis:** For each metric, compute significance (e.g. paired t-tests or Wilcoxon) between GADS and baselines.  Use confidence intervals or bootstrap to account for stream randomness.  

Below is an outline of the **evaluation plan**:

| **Experiment**       | **Setup**                                              | **Metrics**                                           |
|----------------------|--------------------------------------------------------|-------------------------------------------------------|
| Baseline vs GADS     | Compare Cosine-ATS, LLM-only, GADS (full) on dataset    | Accuracy, Precision@K, MAP, NDCG; Fairness gap        |
| Adversarial Injections | Inject hidden instructions/keywords into some resumes | Attack Success Rate; Defense Reduction (Δ accuracy); Policy Violation Rate |
| Ablation (Evidence)  | GADS without evidence term ($w_1=0$)                   | Change in ranking accuracy; false positives (IR)      |
| Ablation (Uncertainty) | GADS without uncertainty ($w_3=0$)                  | # high-unc candidates accepted; error rates           |
| Warm-up Sensitivity  | Vary $B$ (e.g. 0.05$N$, 0.1$N$, 0.2$N$)                | Overall accuracy; early-candidate bias (rank metric)  |
| Role Model Variants  | 1 role model vs multi-quantile role models             | Stability of cutoff; fairness across stream ordering  |
| Fairness Analysis    | Evaluate false positive/negative by group (gender, race) | Demographic Parity, Equalized Odds                    |

Throughout, **human-in-the-loop** effect should be measured: how often GADS defers to humans and with what benefit.  For example, track the accuracy of cases flagged as “uncertain.”  

# Pseudocode and System Diagrams

Below we outline pseudocode and a system flowchart summarizing GADS. Key functions like `LLM_extract` and `score_evidence` would use the appropriate LLM API and a knowledge database, respectively. 

```pseudo
function GADS_ProcessCandidate(Candidate C, Job J):
    claims = LLM_extract(C.resume, prompt_schema)
    E_prov = EvidenceScore(claims)       # e.g. check each claim against sources
    sem_sim = SemanticSim(C.resume, J)   # e.g. embedding cosine
    U_uncert = Uncertainty(claims)      # e.g. LLM self-confidence or entropy

    # Hard-policy adjudication
    if any rule_violated(claims, severity='critical'):
        REJECT C  # immediate eviction (P_hard=0)
        return
    if any rule_violated(claims, severity='medium'):
        ROUTE_TO_HUMAN C
        return

    # Compute Governed Score
    M_G = w1 * E_prov + w2 * sem_sim - w3 * U_uncert

    # Dynamic Priority Queue update
    if queue.size < K_max:
        queue.push((M_G, C))
    else if M_G > queue.min_score():
        queue.pop_min()
        queue.push((M_G, C))

    # Uncertainty routing
    if U_uncert > U_threshold:
        ROUTE_TO_HUMAN C
```

```mermaid
flowchart LR
    subgraph GADS System Flow
      A[Input: (Job J, Candidate Resume R)] --> B(Stage 1: LLM Extraction)
      B --> C(Stage 2: Compute Provenance E_prov)
      C --> D{Stage 3: Policy Check}
      D -->|Pass| E(Compute Score \(M_G\))
      D -->|Fail (Critical)| F[Reject]
      D -->|Medium Violation| G[Flag Human]
      E --> H{High Uncertainty?}
      H -->|No| I(Update Top-K Queue)
      H -->|Yes| G
      I --> J[Maintain Role-Model Benchmark(s)]
    end
```

Above, “Update Top-K Queue” evicts the lowest $M_G$ if full. The current top of the queue is the **Role Model**. Human-reviewable flags appear when uncertainty or rule severity is high. 

# Publication & Future Directions

To publish in a top venue (FAccT, NeurIPS, AAAI), GADS work should include: 

- **Theoretical Guarantees:** Derive performance bounds under assumptions.  E.g. adapt the Byzantine secretary analysis to prove GADS selects near-optimal candidates if up to $\alpha$ fraction are policy-violators.  Prove under what noise level $M_G$ ranking approximates true utility.  Provide complexity bounds for evidence scoring and routing.

- **Adversarial Robustness Analysis:** Mathematically characterize how injection or paraphrase attacks affect $M_G$. Possibly formalize an “attack model” and show that with $P_{hard}$ gating, certain classes of injection cannot succeed.  Derive bounds on attack success probability.  Implement and open-source real attack/defense benchmarks.  

- **Empirical Benchmarks & Code:** Release the codebase and data needed to replicate experiments. Use or extend standard benchmarks (resume screening, credit scoring, TREC, healthcare triage). Compare to LLM-only and rule-only baselines. Provide thorough ablations (weights, $B$, $K$, thresholds) and significance tests.

- **Ethics and Fairness:** Analyze potential biases introduced by components (e.g. evidence sources might be biased). Demonstrate that GADS can include fairness constraints as part of policies. Discuss human costs/benefits: e.g. does human routing improve outcomes enough to justify workload?

- **Open Problems:** Discuss limitations and next steps: how to learn the weights $w_i$ from data, how to integrate multiple evidence sources, etc. Possibly explore RL or learning-to-rank approaches to optimize $M_G$ under constraints.  

**Next Steps:** We recommend (1) formalizing the M_G metric’s properties (e.g. monotonicity, Lipschitz continuity under score perturbation), (2) implementing the pseudocode in a simulated or pilot hiring system, (3) assembling the above datasets and running initial experiments, and (4) writing a theoretical analysis note on the robust top-$K$ aspect. 

