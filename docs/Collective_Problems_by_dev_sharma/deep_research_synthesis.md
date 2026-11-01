# Deep Research Synthesis: Resume Screening Problems and EGCA

## 1. Executive Synthesis

This synthesis analyzes eight research sources examining AI-based resume screening, resume–job matching, LLM-based candidate evaluation, and candidate selection. All eight sources converge on a central finding: **automated resume screening systems frequently produce assessments that are not evidence-grounded, and accuracy metrics alone cannot reveal the types of errors that matter most for fair, valid hiring decisions.**

The eight sources span five core risk domains:

1. **Evidence verification failures** — claims about candidate qualifications are made that are unsupported, hallucinated, contradicted by source text, or based on semantic similarity rather than direct evidence. This cluster is supported by all eight sources in some form.

2. **Requirement interpretation failures** — mandatory vs. preferred requirements are confused, keyword matching mistakes word presence for competence, seniority levels are misread, and skill correlations produce misleading inferences. Supported by Papers 1, 2, 3, and 7.

3. **Score and ranking reliability** — scores are opaque, uncalibrated, model-dependent, context-sensitive, and unstable across repeated evaluations. Aggregate scores can conceal ranking near cutoffs, and pairwise comparisons can produce intransitive cycles. Supported by Papers 1, 2, 5, 7, and 8.

4. **Demographic bias and fairness** — both direct identity cues (names) and indirect proxies (hobbies, languages, activities) influence scores; aggregate fairness metrics hide subgroup disparities; intersectional effects are poorly understood. Supported by Papers 4 and 8.

5. **Data quality, evaluation, and governance gaps** — human labels are inconsistently applied, ground truth is ambiguous, test data leaks across splits, generalizability is unproven, and audit trails or provenance tracking are absent. Supported by Papers 1, 2, 3, 5, 6, and 7.

Five cross-cutting findings stand out:

- **No study validates end-to-end hiring outcomes.** None of the eight sources establishes that higher scores predict better job performance, lower turnover, or fairer selection. Papers 1, 4, 5, and 7 explicitly warn against conflating score accuracy with hiring validity.

- **Human evaluators are also unreliable.** Papers 1 (Cohen's κ = 0.61) and 2 (Fleiss' κ ≈ 0.786) demonstrate that inter-rater agreement among human recruiters is only moderate to substantial, not perfect. This means human labels are imperfect training and evaluation data.

- **Missing evidence is systematically mistaken for negative evidence.** Papers 1, 2, 6, and 7 independently recommend distinguishing "evidence not found" from "candidate lacks qualification" — a distinction that affects whether absent skills are treated as disqualifying.

- **Accuracy and correlation metrics are insufficient.** Papers 1, 2, 5, and 7 all note that accuracy can conceal error types, that correlation does not imply agreement, and that model-dependent behavior means results from one configuration do not generalize.

- **Fairness requires measurement at every pipeline stage, not just final scores.** Papers 4, 5, and 8 establish that bias can enter during extraction, normalization, matching, eligibility checks, evidence scoring, or ranking — and that aggregate metrics conceal local failures.

---

## 2. Source Inventory and Coverage

### Source 1: EMNLP 2020 — AI-based Resume Classification and Resume–Job Matching
- **Document file:** `EGCA_Research_Paper_Deep_Dive_Layman_Report.docx`
- **Type:** Secondary lay-language report summarizing an original EMNLP 2020 study
- **Stated original study:** AI-based resume classification and resume–job matching for Clinical Research Coordinator recruitment at Emory University
- **Research question:** Can AI-based systems classify candidate competence levels and match resumes to jobs?
- **Models/datasets:** Transformer-based models on Clinical Research Coordinator applications from Emory University
- **Key metrics:** 73.26% accuracy (5-class competence prediction), 79.20% accuracy (binary resume–job matching), Cohen's κ = 0.61 (human inter-annotator agreement)
- **Sections referenced:** Sections 2, 5–6, 9–12
- **Evidence type:** SOURCE FINDING (reported experimental results) + SOURCE INTERPRETATION (author commentary on implications)

### Source 2: Human and LLM-Based Resume Matching: An Observational Study (2025)
- **Document file:** `EGCA_Research_Paper_Explained_Layman_Report.docx`
- **Type:** Secondary lay-language report summarizing a 2025 observational study
- **Original study:** Comparison of GPT-4 Turbo ratings with human ratings across 736 resumes in four job categories
- **Research question:** How well do LLM-generated ratings agree with human ratings in resume matching?
- **Models/datasets:** GPT-4 Turbo; 736 resumes across Project Manager, Accountant, Sales, Engineer categories
- **Key metrics:** Pearson correlations (work experience: 0.5675, education: 0.4091, skills: 0.3823, certifications: 0.3438); 31 unsupported inference instances; 35 hallucination instances; Fleiss' κ ≈ 0.786 (human agreement)
- **Sections referenced:** Sections 5, 7–8, 9, 11
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION

### Source 3: Retrieving Skills from Job Descriptions (COLING 2020)
- **Document file:** `EGCA_Skill_Retrieval_Research_Paper_Guide.docx`
- **Type:** Secondary lay-language report summarizing a COLING 2020 paper
- **Original study:** BERT-based XMLC + CAB approach for skill retrieval from job descriptions
- **Authors (original):** Akshay Bhola, Kishaloy Halder, Animesh Prasad, Min-Yen Kan
- **Research question:** How to predict relevant skills that job descriptions omit?
- **Models/datasets:** BERT–XMLC baseline and BERT–XMLC + CAB; 2,548-skill vocabulary; Singapore job postings
- **Key metrics:** Recall@5 (19.60% → 21.67%), Recall@10 (35.58% → 40.49%), Recall@30 (70.10% → 79.59%), nDCG@5 (33.64% → 35.93%), nDCG@10 (48.18% → 52.84%), MRR (0.9019 → 0.9049)
- **Sections referenced:** Sections 2, 4–8
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION

### Source 4: Measuring Gender and Racial Biases in Large Language Models
- **Document file:** `Measuring_Gender_and_Racial_Biases_in_Large_Language_Models_Intersectional_Evidence_from_Automated_Resume_Evaluation.docx`
- **Type:** Secondary lay-language report summarizing original PNAS Nexus March 2025 paper + EGCA proposals
- **Original study:** Experimental investigation of LLM score variation with demographic identity cues
- **Authors (original):** Jiafu An, Difang Huang, Chen Lin, Mingzhu Tai
- **Research question:** Do LLMs give different resume-evaluation scores to candidates with equivalent qualifications when names signal different racial/gender identities?
- **Models/datasets:** 5 LLMs; ~361,000 resumes; 20 entry-level job positions; scoring scale 0–100
- **Key metrics (GPT-3.5 Turbo):** Female vs. male: +0.452 points; Black vs. White aggregate: −0.074; Black women vs. White men: +0.379; White women vs. White men: +0.223; Black men vs. White men: −0.303; estimated selection-probability differences at threshold 80: Black women +1.7pp, White women +1.4pp, Black men −1.4pp
- **Sections referenced:** Sections 2–5, 5–8, 7–8, 10–13
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION
- **Document structure note:** Original-paper summary occupies pp. 1–9; EGCA improvement proposals occupy pp. 10–13

### Source 5: Controlled Audit / Testing Framework for Resume Screening
- **Document file:** **Not specified** — no .docx filename provided for this section
- **Type:** Secondary lay-language report (sections 4–8 only; sections 1–3 missing)
- **Original study:** Not identified by title, authors, or publication venue
- **Document length:** At least 16 pages (references pp. 14–16)
- **Reported content (available sections 4–8 only):**
  - Section 4: Additional implications for EGCA (architectural implications inferred from research)
  - Section 5: What EGCA should change or add (validity-audit flow, controlled test suite)
  - Section 6: Metrics EGCA should report (criterion validity, unjustified selection, unjustified abstention, discriminant validity, selection-rate differences, ranking stability, evidence quality, policy correctness, operational cost)
  - Section 7: Important limitations (simplified ground truth, synthetic resumes, partial manual validation, limited demographic coverage, limited transferability, pairwise vs. full-pool evaluation, no proof of future job performance)
  - Section 8: Final assessment for EGCA research (proposed experiment: LLM-only baseline vs. EGCA pipeline)
- **Evidence type:** SOURCE INTERPRETATION (EGCA design implications) + SOURCE FINDING (limitations section)
- **Critical gaps:** Sections 1–3 (executive summary, core idea, findings, metrics) are absent. No document filename, original paper title, authors, or methodology provided.

### Source 6: RAGTruth — Hallucination Corpus for Trustworthy RAG
- **Document file:** `RAGTruth_Research_Report_EGCA_Easy_Language.docx`
- **Type:** Secondary lay-language report summarizing original RAGTruth paper + EGCA application guide
- **Original study:** Human-labeled corpus for studying hallucinations in retrieval-augmented generation
- **Research question:** How to detect unsupported claims in RAG-generated text?
- **Models/datasets:** 6 language models; 17,790 generated responses; 2,965 source instances; 14,289 human-labeled hallucination spans
- **Key metrics:** Response-level F1 (fine-tuned Llama-2-13B: 78.7% vs. GPT-4-Turbo prompt-based: 63.4%); span-level F1: 52.7%; hallucination rate reduction (52.4% → 41.1% with selection; → 19.3% with zero-span requirement, but only 328/450 eligible)
- **Sections referenced:** pp. 2–9
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION

### Source 7: Signal or Noise? Evaluating LLMs in Resume Screening
- **Document file:** `Research Report_ Signal or Noise.docx`
- **Type:** Secondary lay-language report summarizing original research report
- **Original study:** *Signal or Noise? Evaluating Large Language Models in Resume Screening Across Contextual Variations and Human Expert Benchmarks*
- **Authors (original):** Aryan Varshney and Venkat Ram Reddy Ganuthula
- **Publication:** arXiv:2507.08019 (paper URL: https://arxiv.org/pdf/2507.08019)
- **Research question:** Are LLM resume evaluations based on qualifications or influenced by context?
- **Models/datasets:** Claude, GPT, and Gemini (exact versions not identified in reviewed passages); 30 anonymized Product Manager resumes; 3 human recruitment experts; Associate Product Manager role in Gurgaon, Haryana, India; 420 total evaluations
- **Key metrics:** GPT score: 50.5 (No Company) → 76.1 (MNC) → 77.8 (Startup), all changes statistically significant (p < 0.001); Cohen's d ≈ −1.92 (No Company → MNC) and ≈ −2.02 (No Company → Startup); Claude: 69.4, 66.5, 73.3 (no significant differences); Gemini: 72.8 → 76.7 (MNC, p = 0.038) → 73.6 (Startup, p = 0.498); Randomized No Company: Claude 66.6, GPT 64.3, Gemini 65.4 (p = 0.892); Human vs. LLM p-values: 0.0015, 0.0007, 0.0065, 0.0002; GPT score gap of ~28.9% score inflation in Reduced Context (64.3 → 82.9)
- **Stated criteria (GPT, No Company):** Work experience 30%, Education 20%, Internships 20%, Skills/certifications 15%, Achievements 10%, Positions of responsibility 5%
- **Sections referenced:** pp. 2–3, 4–6, 12–13, 16, 20
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION
- **Note:** Additional detailed notes on this paper's methodology, findings, and proposed EGCA experimental plan are available in the companion working document at `/home/devsharma08/Downloads/README_resume_screening_research.md` (594 lines), which includes the actual paper reference, full scoring criteria, and a proposed experimental evaluation framework.

### Source 8: Small Changes, Big Impact — Demographic Bias in LLM-Based Hiring
- **Document file:** `Small_Changes_Big_Impact_EGCA_Research_Report.docx`
- **Type:** Secondary lay-language report summarizing original arXiv:2603.05189 paper + EGCA proposals
- **Original study:** Investigation of LLM demographic inference from indirect resume cues and effects on hiring-style decisions
- **Research question:** Can LLMs infer demographics from indirect clues in anonymized resumes, and does this influence hiring decisions?
- **Models/datasets:** 18 LLMs across multiple providers and model families; 4,100 synthetic resumes (100 base + 4,000 variants); 100 job descriptions across 20 occupations and 18 industries; Singapore-based; 4 ethnicity labels × 2 gender labels = 8 combinations; 88.8% of resumes judged realistic by 32 volunteer annotators
- **Key metrics:** Demographic classification F1 (ethnicity macro-F1: 0.925–0.997, gender F1: 0.911–0.980); Gemini 3 Flash ethnicity F1 drop: 0.972 → 0.130 when language markers removed
- **Sections referenced:** pp. 2–10, 14, 16
- **Evidence type:** SOURCE FINDING + SOURCE INTERPRETATION

### Coverage gaps and duplications

- **Source 5 is incomplete.** Sections 1–3 are entirely absent. No document filename, original paper title, authors, or methodology are available. Only sections 4–8 (EGCA implications, limitations, final assessment) are present.
- **No original papers are included.** All eight entries are secondary lay-language reports summarizing original research papers. The original papers themselves are not directly available.
- **No source verifies EGCA's effectiveness.** Every source consistently notes that EGCA's proposed architecture has not been experimentally validated.
- **Document name repetition.** Each source inserts its associated .docx filename as a footer on nearly every page of its section, creating visual noise but no loss of information.
- **No source duplication.** Each source covers distinct research. No two sources summarize the same study.

---

## 3. Master Problem Taxonomy

The following taxonomy organizes all identified problems into hierarchical tiers. Each problem entry includes a unique Problem ID, canonical name, original description, supporting sources, evidence, conditions, and EGCA relevance.

### Tier A: Evidence and Verification Failures

**P-A1: Unsupported Inference**
- **Original description (Paper 1, Section 10.2):** "The system makes a stronger claim than the resume supports."
- **Evidence:** Paper 2 identifies 31 unsupported inference instances in manual analysis; Paper 1 describes AI struggling with "practical versus academic experience" and "the difference between participation and leadership."
- **Supporting sources:** Papers 1, 2, 6, 7
- **Conditions:** Observed when models infer proficiency from job titles or related activities without direct evidence
- **Label:** SOURCE FINDING

**P-A2: Hallucinated / Invented Claims**
- **Original description (Paper 2, Section 5):** "AI-generated explanations can be hallucinated... A fluent, convincing explanation is not proof that its claims are correct."
- **Evidence:** Paper 2 identifies 35 hallucination instances; Paper 6 finds 43.1% of 17,790 responses contained hallucinations; RAGTruth study shows even with a detector, undetected errors remained.
- **Supporting sources:** Papers 2, 6, 7
- **Label:** SOURCE FINDING

**P-A3: Contradiction (Extracted Claim Conflicts with Source)**
- **Original description (Paper 1, Section 10.2):** "An extracted claim conflicts with the resume or another reliable source."
- **Evidence:** Paper 6 (RAGTruth) explicitly identifies contradictory claims as a category where "the generated assessment may change a date, number, role or other fact from the source" (pp. 2, 5).
- **Supporting sources:** Papers 1, 6
- **Label:** SOURCE FINDING

**P-A4: Missed Evidence**
- **Original description (Paper 1, Section 10.2):** "Relevant information exists in the resume but the system does not retrieve it."
- **Evidence:** Paper 1 describes long-resume trimming and section-pruning causing "relevant projects, skills, or recent responsibilities" to be lost (Section 6, pp. 7–8). Transformer input-length limits are the stated mechanism.
- **Supporting sources:** Papers 1, 7 (indirectly: "benchmark tests only large qualification gaps")
- **Label:** SOURCE FINDING

**P-A5: Missing Evidence Mistaken for Missing Ability**
- **Original description (Paper 1, Section 10):** "A resume that does not mention deployment experience does not prove that the candidate cannot deploy applications."
- **Evidence:** Papers 1, 2, 6, and 7 independently recommend distinguishing "not established" from "candidate lacks." Paper 7 (Signal or Noise) explicitly warns: "The absence of a portfolio, certificate or stated skill means that the available evidence is incomplete—not necessarily that the candidate lacks the capability." Paper 6: "If a resume does not mention SQL, the system cannot conclude that the candidate lacks SQL skills."
- **Supporting sources:** Papers 1, 2, 6, 7
- **Conditions:** Observed when absent statements are treated as negative qualifiers
- **Label:** SOURCE FINDING

**P-A6: Semantic Similarity Creates False Confidence**
- **Original description:** "Two statements can express related ideas without meaning the same thing."
- **Evidence:** Paper 1 describes "worked with a team" not establishing "supervised a team" (Sections 3.2, 7.3–7.4); Paper 7 notes "semantic similarity to a requirement does not establish that the candidate satisfies it."
- **Supporting sources:** Papers 1, 7
- **Label:** SOURCE INTERPRETATION

### Tier B: Requirement Interpretation and Matching Failures

**P-B1: Keyword Matching Confuses Word Overlap with Competence**
- **Original description (Paper 1, Section 2.1):** "Two candidates may both mention Python, but one may have completed a course while the other has built and deployed a working application."
- **Evidence:** Paper 1 explicitly describes the keyword-matching problem; Paper 3 identifies "exact-word matching" missing relevant skills when "the candidate describes it differently."
- **Supporting sources:** Papers 1, 3
- **Label:** SOURCE FINDING

**P-B2: Resume Wording Differs from Job-Description Wording**
- **Original description (Paper 1, Section 2):** "Candidates can describe relevant responsibilities using different phrases, terminology, or levels of detail."
- **Evidence:** Paper 1 notes "Exact-term matching may miss relevant experience simply because the candidate describes it differently" (Sections 2 and 7, pp. 3 and 8).
- **Supporting sources:** Paper 1
- **Label:** SOURCE FINDING

**P-B3: Mandatory vs. Preferred Requirement Confusion**
- **Original description (Paper 2):** "If a job description says a qualification is preferred rather than mandatory, incorrectly treating it as compulsory can change who advances through screening."
- **Evidence:** Papers 2 and 7 both identify this confusion. Paper 7: "If mandatory and preferred requirements are ambiguous, downstream scoring cannot reliably correct that ambiguity."
- **Supporting sources:** Papers 2, 7
- **Label:** SOURCE FINDING

**P-B4: Seniority / Experience-Level Confusion**
- **Original description (Paper 1, Section 2.2):** "Assisting with a task, independently owning it, and supervising others are not equivalent."
- **Evidence:** Papers 1 and 7 both describe difficulty distinguishing participation from leadership/ownership. Paper 1: "AI models struggle with ambiguous experience levels, transferable skills, practical versus academic experience, and the difference between participation and leadership."
- **Supporting sources:** Papers 1, 7
- **Label:** SOURCE FINDING

**P-B5: Transferable or Unusually Described Experience Missed**
- **Original description (Paper 1, Section 10):** "A candidate whose experience is expressed in an uncommon way may be assessed poorly despite having relevant abilities."
- **Evidence:** Paper 1 explicitly identifies this difficulty. Paper 3 notes "fixed skill vocabulary" may not represent "new, emerging, specialized, or previously unseen skills."
- **Supporting sources:** Papers 1, 3
- **Label:** SOURCE FINDING

**P-B6: Skill Correlation Produces Misleading Conclusions**
- **Original description (Paper 3, Section 6):** "Skills that commonly appear together are not necessarily equivalent, interchangeable, or individually required for a particular role."
- **Evidence:** Paper 3 explicitly warns about CAB using co-occurrence as a training signal: "co-occurrence alone does not prove necessity." Paper 3 infers: "A model might infer Statistics from a data-analysis responsibility. If that inference is automatically treated as compulsory, qualified candidates could be rejected."
- **Supporting sources:** Paper 3
- **Label:** SOURCE INTERPRETATION

### Tier C: Score and Ranking Reliability Failures

**P-C1: Score Opacity and Miscalibration**
- **Original description (Paper 2, Section 16):** "A raw semantic-similarity score should not be treated as the probability that a candidate is qualified."
- **Evidence:** Papers 1, 2, and 7 all warn against treating numerical scores as probabilities without calibration. Paper 7: "A score of 80/100 does not mean an 80% probability of suitability or future success." Paper 1: "Confidence scores need calibration against held-out data."
- **Supporting sources:** Papers 1, 2, 7
- **Label:** SOURCE FINDING

**P-C2: Inconsistent / Unstable Scores**
- **Original description (Paper 1, Section 10.2 / Paper 2, Section 11):** "Repeated assessments differ without meaningful changes in the input or settings."
- **Evidence:** Paper 1 describes "inconsistent output" as a failure type. Paper 2 notes "Model outputs can vary with prompts, settings, model versions, and input formats." Paper 5 identifies "prompt changes silently change decisions."
- **Supporting sources:** Papers 1, 2, 5, 7
- **Label:** SOURCE FINDING

**P-C3: Model-Dependent Scoring**
- **Original description (Paper 7):** "Different LLMs could give substantially different scores to the same resume."
- **Evidence:** Paper 7 shows GPT mean score of ~50.5 vs. Claude ~69.4 vs. Gemini ~72.8 for identical resumes under No-Company condition. Paper 8 notes "18 LLMs from multiple providers and model families" showed "different patterns."
- **Supporting sources:** Papers 7, 8
- **Label:** SOURCE FINDING

**P-C4: Context and Prompt Sensitivity**
- **Original description (Paper 7):** "Adding company context changed some models' scores even though the candidate's resume remained unchanged."
- **Evidence:** Paper 7 documents GPT score change of 25.6 points (50.5 → 76.1) when company context changed from "No Company" to "MNC." Paper 5 identifies "prompt changes can alter qualification validity and fairness." Paper 8 notes "rationale prompting worsened some aggregate metrics."
- **Supporting sources:** Papers 5, 7, 8
- **Label:** SOURCE FINDING

**P-C5: Pairwise Comparison Intransitivity**
- **Original description (Paper 5):** "Candidate A may outrank B, B outrank C, and C outrank A."
- **Evidence:** Paper 5 identifies this as an architectural implication. This is an EGCA design concern, not a directly measured empirical finding.
- **Supporting sources:** Paper 5
- **Label:** SOURCE INTERPRETATION

**P-C6: Shortlist Cutoff Instability**
- **Original description (Paper 5 / Paper 7):** "A small change in scores can change who crosses the top-K cutoff."
- **Evidence:** Paper 5 warns "the benchmark tests only large qualification gaps" while "a model may look accurate overall but struggle with candidates close to the shortlist cutoff." Paper 7 notes "A small change in scores can change who crosses the top-K cutoff." Paper 4 notes score differences translate to estimated selection-probability differences "when candidates sit near the shortlist cutoff."
- **Supporting sources:** Papers 4, 5, 7, 8
- **Label:** SOURCE FINDING + SOURCE INTERPRETATION

### Tier D: Demographic Bias and Fairness Failures

**P-D1: Direct Demographic Identity Cues Influence Scores**
- **Original description (Paper 4):** "Names signalling different racial and gender identities systematically affect LLM resume scores."
- **Evidence:** Paper 4 reports score differences for GPT-3.5 Turbo: Female vs. male +0.452, Black vs. White −0.074, Black women vs. White men +0.379, White women vs. White men +0.223, Black men vs. White men −0.303. Selection-probability differences at threshold 80: Black women +1.7pp, White women +1.4pp, Black men −1.4pp.
- **Supporting sources:** Papers 4, 8
- **Label:** SOURCE FINDING

**P-D2: Indirect Demographic Proxies Survive Anonymisation**
- **Original description (Paper 8):** "Removing names does not remove every signal that a model may associate with a demographic group."
- **Evidence:** Paper 8 demonstrates models recovered designed demographic labels from sociocultural markers (languages, hobbies, volunteering) with F1 up to 0.997 for ethnicity and 0.980 for gender. Removing language markers dropped Gemini 3 Flash ethnicity F1 from 0.972 to 0.130.
- **Supporting sources:** Paper 8
- **Label:** SOURCE FINDING

**P-D3: Intersectional Disparities Hidden by Aggregate Metrics**
- **Original description (Paper 4):** "A broad category can have a positive average score while one of its constituent subgroups receives lower scores."
- **Evidence:** Paper 4 explicitly highlights "differences between Black women and Black men relative to White men." Paper 8 notes "group patterns differ across combinations of gender and ethnicity; examining only one demographic dimension can hide differences."
- **Supporting sources:** Papers 4, 8
- **Label:** SOURCE FINDING

**P-D4: Fairness Measured Only at Aggregate Level**
- **Original description (Paper 5):** "Differences in selection behavior may be hidden by aggregate metrics."
- **Evidence:** Papers 4, 5, and 8 all argue for stage-level and intersectional fairness testing. Paper 8: "Fairness must be measured at decision boundaries. A minor score difference may have no effect for one candidate but change another candidate's shortlist outcome near the top-K cutoff."
- **Supporting sources:** Papers 4, 5, 8
- **Label:** SOURCE INTERPRETATION

**P-D5: Equivalent Candidates Receive Unequal Treatment**
- **Original description:** "The study's fairness target is approximately a 50% win rate for the variant across comparisons."
- **Evidence:** Paper 8 reports models "did not consistently satisfy the experiment's fairness targets" under controlled conditions where qualifications were held constant. Paper 7 notes "a company label should not silently introduce new criteria."
- **Supporting sources:** Papers 7, 8
- **Label:** SOURCE FINDING

### Tier E: Data Quality and Evaluation Limitations

**P-E1: Human Label Inconsistency**
- **Original description (Paper 2):** "Human reviewers can disagree about candidate qualifications, making the labels used to train AI imperfect."
- **Evidence:** Paper 1 reports Cohen's κ = 0.61 (inter-annotator agreement). Paper 2 reports Fleiss' κ ≈ 0.786. Both indicate moderate to substantial, not perfect, agreement.
- **Supporting sources:** Papers 1, 2
- **Label:** SOURCE FINDING

**P-E2: Accuracy Conceals Important Error Types**
- **Original description (Paper 1, Section 12):** "Overall accuracy does not tell us which types of candidates are misclassified, whether false negatives dominate, or whether performance is poor for smaller categories."
- **Evidence:** Paper 1 recommends "Precision, recall, F1, macro F1, calibration, and human agreement" as additional measures. Paper 2 notes "Accuracy alone conceals important errors." Paper 7 notes "synthetic benchmarks can test pipeline logic but cannot establish real-world hiring accuracy or demographic fairness."
- **Supporting sources:** Papers 1, 2, 5, 7
- **Label:** SOURCE FINDING

**P-E3: Training/Test Data Leakage**
- **Original description (Paper 1):** "If the same candidate or near-duplicate resumes appear in both training and test data, measured performance may look better than actual performance on new candidates."
- **Evidence:** Paper 1 recommends "keeping related applications separated across the splits" (Sections 8.1, 13.1).
- **Supporting sources:** Paper 1
- **Label:** SOURCE FINDING

**P-E4: Limited Generalizability Across Jobs, Industries, and Populations**
- **Original description (Paper 1):** "Reported results do not establish equivalent performance for software engineering, finance, other healthcare roles, or different labor markets."
- **Evidence:** Paper 1 limits generalization to "Clinical Research Coordinator applications at Emory University." Paper 3 notes Singapore-specific data. Paper 7 uses 30 Product Manager resumes in Gurgaon. Paper 8 notes Singapore-oriented data with potential differences in India. Paper 2 notes results from GPT-4 Turbo and four job categories do not establish performance "across all industries and hiring environments."
- **Supporting sources:** Papers 1, 2, 3, 7, 8
- **Label:** SOURCE FINDING

**P-E5: Incomplete Reference Labels Distort Evaluation**
- **Original description (Paper 3):** "A predicted skill may genuinely be relevant but still be counted as incorrect if the reference labels omit it."
- **Evidence:** Paper 3 notes "retrieval performance therefore depends partly on the completeness of the evaluation data."
- **Supporting sources:** Paper 3
- **Label:** SOURCE FINDING

**P-E6: Synthetic Data Limitations**
- **Original description (Paper 5):** "Generated examples may contain artifacts and may differ from real applications."
- **Evidence:** Papers 5, 7, and 8 all note synthetic data limitations. Paper 8 reports 88.8% realism rating but cautions "does not establish that the experimental stereotypes accurately describe real people." Paper 7 notes "synthetic data can test pipeline logic but cannot establish real-world hiring accuracy or demographic fairness." Paper 5 notes "synthetic benchmark success is treated as production readiness" as a risk.
- **Supporting sources:** Papers 5, 7, 8
- **Label:** SOURCE FINDING + SOURCE INTERPRETATION

### Tier F: System Design and Governance Gaps

**P-F1: Lack of Verifiable Explanations and Audit Trails**
- **Original description (Paper 1):** "The system may fail to explain its decisions with verifiable evidence, calibrated confidence, and a reliable audit trail."
- **Evidence:** Papers 1, 2, and 7 all identify lack of verifiable explanations. Paper 7: "An explanation may sound convincing without faithfully representing how a score was generated."
- **Supporting sources:** Papers 1, 2, 7
- **Label:** SOURCE FINDING

**P-F2: Absence of Evidence Provenance Tracking**
- **Original description (Paper 6):** "Extracting a claim from a resume does not establish that it is true, and semantic similarity to a requirement does not establish that the candidate satisfies it."
- **Evidence:** Papers 5, 6, and 8 all recommend tracking source spans, evidence states, and provenance. Paper 8 recommends "Store the evidence ID, resume source span, requirement ID, verification status and scoring contribution."
- **Supporting sources:** Papers 5, 6, 8
- **Label:** SOURCE INTERPRETATION

**P-F3: No Explicit Abstention / Uncertainty Policy**
- **Original description (Paper 5):** "The system may force a ranking when evidence is insufficient, or send too many straightforward cases to human review."
- **Evidence:** Paper 5 identifies this as an architectural implication. The concept of routing uncertain cases to human review is proposed by Papers 1, 2, and 6 but not experimentally validated.
- **Supporting sources:** Papers 1, 5, 6
- **Label:** SOURCE INTERPRETATION

**P-F4: Silent Conversion of Predictions to Requirements**
- **Original description (Paper 3):** "Every inferred skill should retain its origin and approval status."
- **Evidence:** Paper 3 warns against silently converting inferred skills into mandatory requirements. Paper 8 recommends "A relevance gate is needed before scoring."
- **Supporting sources:** Papers 3, 8
- **Label:** SOURCE INTERPRETATION

**P-F5: Evidence Availability Effects Not Tracked**
- **Original description (Paper 4):** "Candidates with no public portfolio or GitHub presence may be treated differently from candidates whose work is publicly visible, even when the underlying competence is comparable."
- **Evidence:** Paper 4 explicitly recommends "Track evidence availability separately from evidence quality." Paper 1 recommends "not automatically treating absent evidence as a negative qualification."
- **Supporting sources:** Papers 1, 4
- **Label:** SOURCE INTERPRETATION

---

## 4. Common Problems Ranked by Independent-Study Support

The following ranking is based on the number of independent sources that directly report or strongly imply each problem. "Independent source" means a distinct original study, not multiple summaries of the same paper.

| Rank | Problem ID | Canonical Problem | # Supporting Sources |
|------|-----------|-------------------|----------------------|
| 1 | P-A5 | Missing evidence mistaken for missing ability | 4 (Papers 1, 2, 6, 7) |
| 2 | P-A1 | Unsupported inference | 4 (Papers 1, 2, 6, 7) |
| 3 | P-E2 | Accuracy conceals important error types | 4 (Papers 1, 2, 5, 7) |
| 4 | P-F1 | Lack of verifiable explanations | 5 (Papers 1, 2, 4, 7, 8) |
| 5 | P-E4 | Limited generalizability | 8 (all papers) |
| 6 | P-F2 | No evidence provenance tracking | 4 (Papers 4, 5, 6, 8) |
| 7 | P-F3 | No abstention/uncertainty policy | 4 (Papers 1, 5, 6, 7) |
| 8 | P-A6 | Semantic similarity creates false confidence | 3 (Papers 1, 7, 8) |
| 9 | P-C1 | Score opacity and miscalibration | 3 (Papers 1, 2, 7) |
| 10 | P-C2 | Inconsistent/unstable scores | 4 (Papers 1, 2, 5, 7) |
| 11 | P-C6 | Shortlist cutoff instability | 4 (Papers 4, 5, 7, 8) |
| 12 | P-C3 | Model-dependent scoring | 3 (Papers 4, 7, 8) |
| 13 | P-F5 | Evidence availability bias | 3 (Papers 1, 4, 8) |
| 14 | P-C4 | Context and prompt sensitivity | 3 (Papers 5, 7, 8) |
| 15 | P-A2 | Hallucinated/invented claims | 3 (Papers 2, 6, 7) |
| 16 | P-D4 | Aggregate metrics conceal disparities | 3 (Papers 4, 5, 8) |
| 17 | P-E6 | Synthetic data limitations | 3 (Papers 5, 7, 8) |
| 18 | P-B1 | Keyword matching confusion | 2 (Papers 1, 3) |
| 19 | P-B3 | Mandatory vs. preferred requirement confusion | 2 (Papers 2, 7) |
| 20 | P-B5 | Transferable experience missed | 2 (Papers 1, 3) |
| 21 | P-A3 | Contradiction (claim conflicts with source) | 2 (Papers 1, 6) |
| 22 | P-A4 | Missed evidence | 2 (Papers 1, 7) |
| 23 | P-D1 | Direct demographic cues influence scores | 2 (Papers 4, 8) |
| 24 | P-D3 | Intersectional disparities hidden | 2 (Papers 4, 8) |
| 25 | P-D5 | Unequal treatment of equivalent candidates | 2 (Papers 7, 8) |
| 26 | P-E1 | Human label inconsistency | 2 (Papers 1, 2) |
| 27 | P-F4 | Silent prediction-to-requirement conversion | 2 (Papers 3, 8) |
| 28 | P-B2 | Resume wording differs from job description | 1 (Paper 1) |
| 29 | P-B4 | Seniority/experience-level confusion | 1 (Paper 1) |
| 30 | P-B6 | Skill correlation produces misleading conclusions | 1 (Paper 3) |
| 31 | P-C5 | Pairwise comparison intransitivity | 1 (Paper 5) |
| 32 | P-D2 | Indirect proxies survive anonymization | 1 (Paper 8) |
| 33 | P-E3 | Training/test data leakage | 1 (Paper 1) |
| 34 | P-E5 | Incomplete reference labels | 1 (Paper 3) |

**Problems supported by >= 4 independent studies:** 9 (P-A1, P-A5, P-C2, P-C6, P-E2, P-E4, P-F1, P-F2, P-F3)
**Problems supported by >= 3 independent studies:** 15
**Problems supported by exactly 1 study:** 7 (P-B2, P-B4, P-B6, P-C5, P-D2, P-E3, P-E5)

Notes:
- **P-E4** (8 sources) is the most widely reported problem -- every study acknowledges limited generalizability. However, generalizability is a methodological caveat rather than a direct failure mode.
- **P-A5** (4 sources) is the most practically severe problem -- four independent studies explicitly recommend distinguishing absent evidence from negative evidence.
- **P-F1** (5 sources) identifies the lack of verifiable explanations, making it the most well-supported governance concern.

---

## 5. Conditional Problems and Their Triggering Conditions

### CP-1: Demographic cue impact under anonymization failure
- **Trigger:** Resume anonymization removes direct identifiers (names) but preserves indirect markers (hobbies, languages, volunteering activities).
- **Condition:** Model is sensitive to sociocultural markers that correlate with demographic identity.
- **Supporting sources:** Paper 8 (demonstrated: removing language markers dropped Gemini 3 Flash ethnicity F1 from 0.972 to 0.130)
- **Confidence:** SOURCE FINDING — directly demonstrated

### CP-2: Score instability under context perturbation
- **Trigger:** Company context, job description completeness, or prompt wording changes while candidate resume remains identical.
- **Condition:** Model applies different implicit criteria based on contextual framing rather than job-relevant evidence only.
- **Supporting sources:** Paper 7 (GPT score: 50.5 → 76.1 when company context changed); Paper 5 (prompt changes silently alter decisions)
- **Confidence:** SOURCE FINDING

### CP-3: False inference near shortlist cutoffs
- **Trigger:** Candidate's score falls within a narrow band near the shortlist threshold.
- **Condition:** Small score differences, whether from model sensitivity, missing evidence treatment, or prompt variation, can flip shortlist membership.
- **Supporting sources:** Papers 4, 5, 7, 8
- **Confidence:** SOURCE FINDING + SOURCE INTERPRETATION

### CP-4: Skill inference when vocabulary is fixed
- **Trigger:** Job description mentions emerging or specialized skills not in the model's predefined vocabulary.
- **Condition:** The skill retrieval model operates from a fixed 2,548-label vocabulary and cannot represent new terms.
- **Supporting sources:** Paper 3
- **Confidence:** SOURCE FINDING

### CP-5: Inconsistent ranking under pairwise comparison
- **Trigger:** Model is run on candidate pairs in isolation without a global ranking mechanism.
- **Condition:** Pairwise comparisons are intransitive (A > B, B > C, C > A).
- **Supporting sources:** Paper 5
- **Confidence:** SOURCE INTERPRETATION

### CP-6: Evidence availability bias
- **Trigger:** Candidate has no public portfolio, GitHub, or artifacts visible to the screening system.
- **Condition:** The model or policy treats absence of public artifacts as a negative signal rather than missing evidence.
- **Supporting sources:** Paper 4
- **Confidence:** SOURCE INTERPRETATION

### CP-7: Mandatory requirement enforcement under strong optional signals
- **Trigger:** Candidate strongly satisfies preferred/optional qualifications but fails a mandatory requirement.
- **Condition:** The scoring system allows optional qualification strength to override mandatory rule violations.
- **Supporting sources:** Paper 7
- **Confidence:** SOURCE INTERPRETATION

---

## 6. Unique Findings

### A. Findings Reported by Only One Study

| Finding | Source | Why It Is Distinct |
|---------|--------|-------------------|
| **Transformer input-length limits cause information loss** (long resumes truncated) | Paper 1 (Section 6, pp. 7–8) | Only Paper 1 explicitly addresses the technical constraint of transformer context windows as a source of missed evidence. Other papers address parsing errors or missing evidence, but not specifically the input-length truncation mechanism. |
| **Parsing achieves ~97% section segmentation accuracy but remains imperfect** | Paper 1 (Section 5.2–5.3, p. 6) | Only Paper 1 provides a specific parsing accuracy figure (~97% on 200 manually checked resumes). This quantifies a baseline reliability that other papers assume but do not measure. |
| **BERT–XMLC + CAB skill retrieval with Correlation-Aware Bootstrapping** | Paper 3 | Only Paper 3 proposes a specific technical approach (BERT–XMLC + CAB) for inferring omitted skills from job descriptions. No other paper addresses job-side skill inference. |
| **RAGTruth human-labeled hallucination corpus** (17,790 responses, 14,289 spans) | Paper 6 | Only Paper 6 provides a large-scale human-labeled dataset for hallucination detection with both response-level and span-level annotations. |
| **Language markers as primary ethnicity classifier** (Gemini 3 Flash F1 drop 0.972 → 0.130) | Paper 8 | Only Paper 8 identifies language proficiency signals as the dominant indirect pathway for ethnicity inference. Other papers discuss indirect proxies generally but do not isolate specific marker categories. |
| **Rationale prompting worsens fairness for some models** | Paper 8 | Only Paper 8 tests and reports that asking models to explain decisions before scoring can worsen aggregate fairness metrics. Other papers mention unreliable explanations but do not test explanation prompting as a debiasing intervention. |
| **Candidate-order sensitivity** (swapping Resume A and B may reverse preference) | Paper 5 | Only Paper 5 proposes testing whether swapping two candidates reverses the underlying preference. This is a unique intransitivity test. |
| **Evidence-quality distribution metrics** (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) | Paper 4 (Section 5, p. 5) | Only Paper 4 proposes a five-state evidence provenance taxonomy. Papers 6 and 8 propose four-state and simpler schemes respectively. |
| **False-discovery-rate correction in statistical analysis** | Paper 7 | Only Paper 7 reports using FDR correction alongside ANOVA and paired t-tests, suggesting more rigorous statistical methodology than other papers. |

### B. Findings That Occur Only Under Specific Experimental Conditions

| Finding | Condition | Source |
|---------|-----------|--------|
| **GPT score changes of 25+ points under different company context** | Identical resume; No Company vs. MNC vs. Startup context; Associate Product Manager role in Gurgaon | Paper 7 |
| **GPT-3.5 Turbo shows specific demographic score offsets** | US entry-level jobs; controlled identity-signaling names; threshold 80/100 | Paper 4 |
| **Demographic classification F1 of 0.925–0.997 (ethnicity) and 0.911–0.980 (gender)** | Synthetic resumes with deliberately designed sociocultural markers; Singapore job context; 18 LLMs | Paper 8 |
| **Hallucination rate of 43.1% in RAGTruth dataset** | Six specific language models; specific source tasks (wikidata, news, dialogue); not all AI applications | Paper 6 |
| **Recall@30 improvement of 9.49pp with CAB** | Singapore job postings; 2,548-label skill vocabulary; BERT–XMLC base model | Paper 3 |
| **Cohen's d effect sizes not reported for context sensitivity** | Paper 7 notes "effect sizes" are mentioned but not provided in the reviewed passages, limiting practical interpretation | Paper 7 |
| **Fleiss' κ ≈ 0.786 for human raters** | 736 resumes; four specific job categories (Project Manager, Accountant, Sales, Engineer); GPT-4 Turbo comparison | Paper 2 |
| **Cohen's κ = 0.61 for inter-annotator agreement** | Clinical Research Coordinator applications at Emory University; 5-class competence prediction | Paper 1 |
| **35 hallucination instances identified in manual analysis** | GPT-4 Turbo evaluation; specific annotation review process; count describes reviewed cases, not universal rate | Paper 2 |

### C. Findings That Challenge or Qualify Common Conclusions

| Challenging Finding | How It Qualifies a Common Conclusion | Source |
|---------------------|--------------------------------------|--------|
| **Rationale prompting can worsen fairness** | Qualifies the common assumption that explanations make models more trustworthy. Paper 8 shows explanation generation can increase disparities. | Paper 8 |
| **Accuracy improvement of only 1.50pp in resume matching** | Qualifies Paper 1's reported 79.20% accuracy. The improvement over baseline is marginal (77.70% → 79.20%), suggesting diminishing returns. | Paper 1 |
| **73.26% accuracy in 5-class competence prediction** | Qualifies the notion that high accuracy means reliable assessment. With 5 classes, random baseline is 20%, so 73.26% is better but leaves 26.74% error — substantial. Also, accuracy doesn't reveal which error types dominate. | Paper 1 |
| **31 unsupported inferences in manual analysis** | Qualifies the reliability of AI-generated explanations. Even when scores look correct, the reasoning may be unsupported. | Paper 2 |
| **Human inter-rater agreement is only κ = 0.61–0.786** | Qualifies the common practice of treating human labels as ground truth. If humans disagree substantially, using them as reference labels introduces ambiguity into evaluations. | Papers 1, 2 |
| **Selection probability differences of 1.7pp at threshold 80** | Qualifies small score differences. Even ±0.452 on a 0–100 scale can translate to measurable selection effects when candidates cluster near thresholds. | Paper 4 |
| **Span-level F1 of only 52.7% for hallucination detection** | Qualifies the usefulness of hallucination detectors. Detecting that a response contains an error is much easier than pinpointing exactly which span is unsupported. | Paper 6 |
| **Different models respond differently to context** (Claude: no significant context effect; GPT: 25+ point swing; Gemini: moderate) | Qualifies the generalizability of context-sensitivity findings. Context sensitivity is not uniform across models. | Paper 7 |
| **Removing language markers sharply reduces ethnicity classification** (0.972 → 0.130) | Qualifies the notion that anonymisation is uniformly ineffective. Some markers are more informative than others; targeted removal can be effective for specific dimensions. | Paper 8 |
| **Synthetic data realism rated 88.8% but stereotypes not validated** | Qualifies the credibility of synthetic benchmark results. High realism does not imply representativeness of real demographic distributions. | Paper 8 |

### D. Findings That Appear Contradictory Across Studies

| Apparent Contradiction | Source A | Source B | Resolution |
|------------------------|----------|----------|-----------|
| **Human disagreement is low enough to serve as reference labels** | Paper 2 reports Fleiss' κ ≈ 0.786 ("substantial agreement"), suggesting human labels are reasonably reliable | Paper 1 reports Cohen's κ = 0.61 (moderate agreement), suggesting human labels are more ambiguous | Both are correct within their contexts. The difference arises from: (1) different tasks (5-class competence vs. multi-rater 4-category ratings), (2) different annotation protocols, and (3) different κ calculation methods (Cohen's vs. Fleiss'). The key insight is that even "substantial" agreement leaves room for systematic bias in reference labels. |
| **Different LLMs give substantially different scores** | Paper 7 shows GPT (~50.5), Claude (~69.4), Gemini (~72.8) differ significantly for identical resumes | Paper 8 finds "different models and model families can show different patterns" | No real contradiction — both report model-dependent behavior. Paper 7 quantifies it for the No-Company condition; Paper 8 generalizes it across 18 models. The consistency is in the finding itself, not a contradiction. |
| **Context sensitivity varies by model** | Paper 7: GPT shows large context sensitivity (25+ point swings), Claude shows none, Gemini shows moderate | Paper 5: "A small wording change can alter qualification validity and fairness in different directions" | These are consistent — Paper 7 provides specific measurements showing context effects vary by model, while Paper 5 states the general principle. The "contradiction" is only apparent: some models are more context-sensitive than others. |
| **Correlation between AI and human ratings is "limited"** | Paper 2 reports Pearson correlations ranging from 0.3438 (certifications) to 0.5675 (work experience) | Paper 2 also states these represent "limited overall correlation" | No contradiction. The correlations are moderate at best and vary by category. The "limited" characterization is the authors' interpretation, supported by the numbers. |
| **Demographic markers can and cannot be removed** | Paper 8 shows removing names alone is insufficient (indirect proxies remain) | Paper 8 also shows removing language markers sharply reduces ethnicity classification (0.972 → 0.130) | Not a contradiction — these refer to different marker types. Name anonymization does not remove language-based or hobby-based proxies, but targeted removal of specific high-leakage markers can be effective. The implication is that anonymization requires multi-dimensional intervention, not a single change. |

### E. Important Areas Not Investigated Adequately by the Available Sources

| Uninvestigated Area | Why It Matters | Gap Type |
|---------------------|---------------|----------|
| **Downstream hiring outcomes** (performance, retention, promotion) | No study establishes that selecting higher-scoring candidates leads to better job performance | EVIDENCE GAP |
| **Longitudinal fairness** (bias accumulation across pipeline stages) | Bias at one stage may compound or mitigate at others; no study tracks this | EVIDENCE GAP |
| **Cost-benefit trade-offs of human review** | Papers propose routing uncertain cases to humans, but no study quantifies the cost or effectiveness of additional review | EVIDENCE GAP |
| **Cross-organization generalizability** | All studies use specific datasets from specific contexts; no study tests cross-organization transfer | EVIDENCE GAP |
| **Multilingual and cross-cultural screening** | Paper 8 notes Singapore data may not transfer to India; no systematic multilingual study | EVIDENCE GAP |
| **Real-time bias drift monitoring** | No study addresses how to detect bias drift as models are updated | EVIDENCE GAP |
| **Interaction effects between multiple failure modes** | Studies treat problems in isolation; no study examines how hallucinations + bias + instability compound | EVIDENCE GAP |
| **Effectiveness of explanation-based debiasing** | Paper 8 shows rationale prompting can worsen fairness; no study tests alternative explanation interventions | EVIDENCE GAP |
| **Full-pipeline integration testing** | No study tests all EGCA components (extraction → verification → policy → ranking) end-to-end | EVIDENCE GAP |

---

## 7. Conflicting Evidence and Disagreements

### D1: Whether "substantial" human agreement justifies using humans as ground truth

- **Paper 1 perspective:** Cohen's κ = 0.61 indicates "moderate" agreement. The report states: "human reviewers can disagree about candidate qualifications, making the labels used to train AI imperfect." This frames human disagreement as a data quality problem that propagates into AI evaluations.
- **Paper 2 perspective:** Fleiss' κ ≈ 0.786 is described as "substantial agreement." The implication is that while humans disagree, the agreement is sufficient to serve as a reference standard with appropriate caveats.
- **Key disagreement:** Whether κ = 0.61 vs. κ = 0.786 represents a "problematic" or "acceptable" level of human reliability for use as evaluation ground truth.
- **Resolution:** Both agree humans are imperfect. The disagreement is in interpretation of what "substantial" means. The κ scale (Landis & Koch) classifies 0.61 as "moderate" and 0.786 as "substantial." The practical question is whether either level of agreement is sufficient for high-stakes hiring decisions — a question neither paper answers.

### D2: Whether correlation between AI and human ratings is sufficient evidence of quality

- **Paper 2 perspective:** Reports correlation coefficients (0.34–0.57) and states: "Higher correlation indicates a stronger tendency for AI and human scores to move together. It does not measure the percentage of correct ratings." Also notes: "Two evaluators can rank candidates similarly but consistently give different scores."
- **Paper 7 perspective:** Reports statistically significant p-values (p < 0.01) for human vs. LLM differences, concluding: "LLM evaluations differed significantly from human evaluations." However, also notes: "the experiments do not establish actual job-performance prediction."
- **Key disagreement:** Whether statistical significance (Paper 7's p-values) or correlation magnitude (Paper 2's Pearson r) is the more relevant measure of AI-human alignment.
- **Resolution:** Both agree these metrics are insufficient on their own. Paper 2 emphasizes correlation ≠ agreement; Paper 7 emphasizes significance ≠ correctness. The synthesis conclusion is that neither correlation nor significance establishes validity — both are necessary but insufficient conditions.

### D3: Whether rationale/explanation prompting improves or worsens fairness

- **Paper 8 perspective:** "Asking models to explain their decisions did not reliably eliminate bias. Aggregate metrics worsened in the reported analysis, although effects varied between models."
- **Paper 2 perspective:** Reports 35 hallucination instances in explanations, suggesting explanations are unreliable: "A fluent, convincing explanation is not proof that its claims are correct."
- **Key disagreement:** None explicitly exists, but there is a tension: one might expect explanations to improve trustworthiness, yet both papers find them unreliable or harmful.
- **Resolution:** The evidence consistently shows that generating explanations does not solve underlying issues and may introduce new ones. This is a convergence of findings, not a disagreement.

### D4: Whether synthetic data can support meaningful evaluation

- **Paper 5 perspective:** Lists as a limitation: "Synthetic resumes: generated examples may contain artifacts." Also: "Synthetic benchmark success is treated as production readiness" as an architectural risk.
- **Paper 7 perspective:** Notes: "Synthetic data can test pipeline logic but cannot establish real-world hiring accuracy or demographic fairness."
- **Paper 8 perspective:** Reports 88.8% realism but cautions it "does not establish that the experimental stereotypes accurately describe real people."
- **Key disagreement:** None — all three agree synthetic data is useful for certain types of testing but cannot establish real-world validity.
- **Resolution:** Convergence — synthetic data supports internal validity tests (e.g., counterfactual consistency) but cannot establish external validity (real-world hiring impact).

### D5: Whether accuracy improvements are meaningful

- **Paper 1 perspective:** Reports 73.26% accuracy for competence prediction (+4.20pp over baseline of 69.06%) and 79.20% for binary matching (+1.50pp over 77.70%).
- **Paper 5 perspective:** Warns "Accuracy alone conceals important errors" and "The benchmark tests only large qualification gaps."
- **Key disagreement:** Whether the reported accuracy gains (4.20pp and 1.50pp) represent meaningful progress or marginal improvements that conceal deeper problems.
- **Resolution:** Papers 1 and 5 are not in direct disagreement — Paper 1 reports the numbers factually, while Paper 5 (and Paper 1's own interpretation) caveat their significance. The synthesis conclusion is that accuracy gains are real but insufficient as the sole evaluation criterion.

---

## 8. Underlying Causes and Relationships Between Problems

### Cause–Mechanism–Failure–Impact Map

#### Root Cause 1: Language models optimize for statistical patterns, not evidence-based reasoning

**Mechanism:** LLMs are trained to predict the next token based on co-occurrence statistics. They do not have an innate ability to verify claims against source evidence.

**Failure modes this produces:**
- P-A1 (Unsupported inference) — models infer stronger claims than evidence supports because co-occurrence patterns make the inference feel "natural"
- P-A2 (Hallucinated claims) — models generate fluent statements that are not grounded in source text because training optimizes for coherence, not truth
- P-A3 (Contradiction) — models alter source facts during generation because token-level patterns don't preserve factual consistency
- P-A6 (False confidence from similarity) — semantic similarity metrics conflate co-occurrence with competence

**Evidence strength:** 
- Paper 6 provides the most direct evidence: 43.1% of 17,790 responses contained hallucinations, demonstrating that retrieval-augmented models still generate unsupported content.
- Paper 2 provides evidence for unsupported inferences (31 instances) and hallucinations (35 instances).
- Papers 1 and 7 provide evidence for semantic similarity confusion.

**Status:** PROPOSED CAUSE — strongly supported by multiple studies but not definitively proven as the sole mechanism.

#### Root Cause 2: Demographic proxies are embedded in language patterns

**Mechanism:** Language models learn associations between sociocultural markers (names, languages, hobbies, activities) and demographic categories from training data. These associations persist even when direct identifiers are removed.

**Failure modes this produces:**
- P-D1 (Direct demographic cues influence scores)
- P-D2 (Indirect proxies survive anonymization)
- P-D3 (Intersectional disparities hidden)
- P-D5 (Equivalent candidates treated unequally)

**Evidence strength:**
- Paper 4 provides direct experimental evidence: controlled identity-signaling names produced score differences of +0.3 to +0.5 points on a 0–100 scale, translating to 1.4–1.7 percentage point selection-probability differences.
- Paper 8 provides stronger evidence: models recovered designed demographic labels with macro-F1 of 0.925–0.997 for ethnicity, and removing language markers dropped performance from 0.972 to 0.130 for one model, demonstrating that indirect markers carry strong demographic signal.

**Status:** ESTABLISHED CAUSE — directly demonstrated through controlled experiments in Papers 4 and 8.

#### Root Cause 3: Absence of deterministic grounding and verification layers

**Mechanism:** AI models generate holistic scores based on learned patterns. Without a separate verification step that checks each claim against source evidence and applies deterministic eligibility rules, unsupported or incorrect assessments can influence final decisions.

**Failure modes this produces:**
- P-A4 (Missed evidence) — without explicit evidence retrieval, relevant information can be lost
- P-C1 (Score opacity) — holistic scores have no internal structure that can be audited
- P-C2 (Inconsistent scores) — small changes in input can cascade through unconstrained generation
- P-F1 (Lack of audit trails) — without provenance tracking, decisions cannot be traced or reproduced
- P-F3 (No abstention policy) — without uncertainty handling, the system must always make a decision

**Evidence strength:**
- Papers 1, 2, 5, 6, 7, and 8 all recommend separate verification, provenance tracking, and uncertainty handling as solutions.
- None of the papers demonstrates that these solutions work when implemented — they are all recommended design implications.
- Paper 5 is the most explicit: "evidence provenance, deterministic policies, and uncertainty routing are architectural safeguards—but their presence does not prove that the overall system produces valid rankings."

**Status:** PROPOSED SOLUTION — strongly recommended by all sources but not experimentally validated.

#### Root Cause 4: Evaluation metrics measure the wrong things

**Mechanism:** Studies evaluate AI systems using accuracy, correlation, and aggregate fairness metrics. These metrics do not capture error types, fairness at decision boundaries, or real-world hiring outcomes.

**Failure modes this conceals:**
- P-E1 (Human label inconsistency) — accuracy metrics assume human labels are correct
- P-E2 (Accuracy conceals errors) — high accuracy can coexist with systematic bias against specific subgroups
- P-E4 (Limited generalizability) — test-set accuracy does not guarantee real-world performance
- P-E6 (Synthetic data limitations) — synthetic benchmark performance does not establish real-world effectiveness
- P-C6 (Shortlist cutoff instability) — aggregate metrics don't reveal boundary sensitivity

**Evidence strength:**
- Paper 1 explicitly recommends precision, recall, F1, macro F1, calibration, and human agreement as supplements to accuracy.
- Paper 2 distinguishes correlation, exact-match accuracy, and inter-rater agreement.
- Paper 5 proposes an extensive metric suite (criterion validity, unjustified selection, ranking stability, etc.).
- Papers 7 and 8 recommend stage-level evaluation and counterfactual testing.

**Status:** ESTABLISHED CONCERN — all studies agree current metrics are insufficient, though specific alternative metrics are proposed rather than validated.

#### Root Cause 5: Context and input perturbation sensitivity

**Mechanism:** LLMs are sensitive to contextual framing, prompt wording, input format, and candidate ordering. Without invariance testing, these sensitivities can produce inconsistent evaluations of equivalent candidates.

**Failure modes this produces:**
- P-C2 (Inconsistent scores)
- P-C4 (Context and prompt sensitivity)
- P-C5 (Pairwise intransitivity)
- P-C6 (Shortlist cutoff instability)

**Evidence strength:**
- Paper 7 provides the strongest evidence: GPT scores changed by 25.6 points (50.5 → 76.1) when only the company context changed, with identical resumes.
- Paper 5 identifies prompt sensitivity as an architectural risk but does not provide specific measurements.
- Paper 8 shows rationale prompting changed outcomes in unpredictable directions.

**Status:** ESTABLISHED CAUSE — directly demonstrated in Paper 7, broadly supported by Papers 5 and 8.

### Relationship Summary

The five root causes are interrelated:
1. **Statistical optimization (Root Cause 1)** produces unsupported claims that are difficult to detect (P-E2) and that vary with context (Root Cause 5).
2. **Demographic proxies (Root Cause 2)** compound with statistical optimization to produce biased outcomes that aggregate metrics miss (Root Cause 4).
3. **Absence of deterministic grounding (Root Cause 3)** means there is no mechanism to catch or correct for the failures produced by the other root causes.
4. **Evaluation misalignment (Root Cause 4)** means the failure modes of root causes 1, 2, and 5 go undetected in standard evaluations.

---

## 9. Existing Solutions and Their Demonstrated Limitations

### Solution 1: Semantic similarity matching

**Intended purpose:** Replace keyword matching with deeper semantic understanding to capture related concepts expressed in different words.

**Supported by:** Papers 1, 2, 3 (implicitly — BERT-based approaches)

**Demonstrated limitations:**
- Paper 1 (Section 10.2, p. 12): "Similarity scores do not establish that a candidate meets a mandatory job requirement." Semantic similarity between "worked with a team" and "supervised a team" does not prove leadership.
- Paper 7: "Semantic similarity to a requirement does not establish that the candidate satisfies it."
- Paper 1: "Two statements can express related ideas without meaning the same thing."

**EGCA relevance:** EGCA proposes replacing similarity-based matching with evidence verification. This solution has not been experimentally validated as superior.

**Status:** SOURCE FINDING — semantic similarity is explicitly identified as insufficient by Papers 1 and 7.

### Solution 2: Structured prompts with worked examples

**Intended purpose:** Improve alignment with human ratings by providing explicit instructions and examples.

**Supported by:** Paper 2 (Section 5: "Structured prompts and worked examples improved alignment with human ratings in some comparisons")

**Demonstrated limitations:**
- Paper 2: "However, the model remained imperfect. Prompt engineering is therefore an improvement technique, not a substitute for verification."
- Paper 8: "Asking models to explain their decisions did not reliably eliminate bias. Aggregate metrics worsened."

**EGCA relevance:** EGCA does not rely solely on prompt engineering. This is explicitly noted as insufficient.

**Status:** SOURCE FINDING — prompt engineering improves but does not solve the fundamental problems.

### Solution 3: Evidence-grounded claim verification

**Intended purpose:** Separate evidence retrieval from claim generation. Each assessment claim must be traceable to a source passage and evaluated against predefined job-specific rules.

**Supported by:** Papers 1, 2, 6, 7, 8 (all recommend this approach)

**Demonstrated limitations:**
- None of the eight sources demonstrates that this approach works when implemented. All sources describe it as a recommended design.
- Paper 6: "It does not prove that EGCA improves resume shortlisting. The report proposes an adaptation; it does not present an experiment evaluating your complete architecture."
- Paper 8: "A claim may be extracted accurately and supported by a genuine project while the scoring system still responds differently to irrelevant identity cues."
- Paper 2: "Evidence grounding establishes what supports a claim, but does not by itself eliminate unsupported inference or hallucination."

**EGCA relevance:** This is the core EGCA proposal but remains a design hypothesis, not an experimentally validated solution.

**Status:** EGCA HYPOTHESIS — strongly recommended by all sources but not experimentally validated.

### Solution 4: Deterministic policy enforcement (OPA/Rego, hard eligibility rules)

**Intended purpose:** Ensure mandatory requirements cannot be overridden by strong optional qualifications or semantic similarity.

**Supported by:** Papers 4, 5, 7, 8

**Demonstrated limitations:**
- None of the sources demonstrates that deterministic rules improve outcomes when implemented. All describe them as recommended design.
- Paper 7: "The ranking layer must not override mandatory rules" — this is a principle, not a demonstrated capability.
- Paper 4: "Evidence provenance, deterministic policy rules, or explainability alone will eliminate bias" — explicitly stated as NOT proven.

**EGCA relevance:** EGCA proposes this as a core component but it has not been experimentally validated.

**Status:** EGCA HYPOTHESIS — recommended but unvalidated.

### Solution 5: Uncertainty-based human review routing

**Intended purpose:** Route ambiguous, contradictory, or consequential cases to human reviewers rather than forcing automated decisions.

**Supported by:** Papers 1, 2, 5, 6, 7, 8

**Demonstrated limitations:**
- No source provides evidence on the optimal threshold for human review or the cost-effectiveness of different routing strategies.
- Paper 6: "A detector might reduce fabricated statements while increasing review workload."
- Paper 5: "The system may force a ranking when evidence is insufficient, or send too many straightforward cases to human review" — identifying the tension without resolution.
- Paper 8: "You should also measure false flags, valid-candidate retention and human-review burden. Otherwise, a system could appear fairer simply by abstaining more often."

**EGCA relevance:** EGCA proposes this as a core component but the trade-off between review burden and accuracy is unquantified.

**Status:** EGCA HYPOTHESIS — recommended but the cost-benefit trade-off is unmeasured.

### Solution 6: Counterfactual fairness testing

**Intended purpose:** Test whether equivalent candidates receive different treatment when only irrelevant attributes (e.g., demographic cues) change.

**Supported by:** Papers 4, 5, 8

**Demonstrated limitations:**
- These are testing frameworks, not deployed solutions. They identify where bias exists but do not eliminate it.
- Paper 8: "Anonymisation hides direct identifiers; evidence grounding establishes what supports a claim; a relevance gate controls what may affect a score; counterfactual testing checks whether irrelevant changes alter the decision." — all framed as complementary, not standalone solutions.
- Paper 5: "The study provides a controlled audit method, but it does not establish that a model which passes the tests will necessarily perform well in real hiring."

**EGCA relevance:** EGCA proposes counterfactual testing as a validation layer, not a fairness solution itself.

**Status:** METHOD PROPOSAL — a testing methodology, not a fairness intervention.

---

## 10. Research Gaps and Unanswered Questions

| Gap ID | Unanswered Question | Why It Matters | Papers That Identify the Gap |
|--------|-------------------|----------------|------------------------------|
| G-1 | Does evidence-grounded verification actually reduce unsupported claims in hiring decisions? | Papers 1, 2, 6, 7, 8 all recommend it but none demonstrates it works | Papers 1, 2, 6, 7, 8 |
| G-2 | Does deterministic policy enforcement improve mandatory-rule compliance without excessive human review? | Papers 4, 5, 7, 8 propose it but the trade-off with review burden is unmeasured | Papers 4, 5, 7, 8 |
| G-3 | Does counterfactual testing actually reduce demographic disparities in real hiring outcomes? | Papers 4, 5, 8 propose it as a testing method but not as a bias mitigation intervention | Papers 4, 5, 8 |
| G-4 | What is the optimal balance between false positives and false negatives in claim verification? | Papers 1, 2, 6 identify both as costly but provide no quantitative trade-off analysis | Papers 1, 2, 6 |
| G-5 | How does the EvidenceLedger provenance taxonomy (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) affect scoring and ranking quality? | Proposed in Paper 4 but not validated with empirical data | Paper 4 |
| G-6 | What is the relationship between hallucination detection performance (response-level vs. span-level) and actual hiring decision quality? | Papers 5, 6, 7 propose metrics but none connects detection to hiring outcomes | Papers 5, 6, 7 |
| G-7 | Does the Governed Closeness Metric (GCM) improve ranking stability compared to raw semantic similarity, and at what cost in human review? | Proposed in Paper 4 but not experimentally evaluated | Paper 4 |
| G-8 | How does the Adaptive Gate buffer size affect shortlist quality and mandatory-rule compliance? | Proposed in Paper 7 but no optimal buffer size is established | Paper 7 |
| G-9 | Does the Policy-Bounded Priority Queue (PBPQ) resolve pairwise intransitivity better than unconstrained LLM ranking? | Proposed in Paper 4 but not tested | Paper 4 |
| G-10 | What is the optimal threshold for routing cases to human review? | Papers 1, 2, 5, 6, 7, 8 all recommend human review but do not specify thresholds | All papers |
| G-11 | Can evidence provenance and claim verification reduce demographic bias, or is separate fairness testing always needed? | Papers 4, 8 suggest they are complementary but do not test whether provenance alone reduces bias | Papers 4, 8 |
| G-12 | How do skill inference systems (Paper 3) interact with claim verification (Paper 6) in a complete pipeline? | No source tests this integration | Papers 3, 6 |
| G-13 | What happens when multiple failure modes interact (e.g., hallucination + demographic bias + score instability simultaneously)? | No source examines interaction effects | Papers 1–8 (none) |
| G-14 | How does the system perform on real (non-synthetic) resumes at scale? | Papers 5, 7, 8 use synthetic or limited real data; no large-scale real-resume evaluation | Papers 5, 7, 8 |
| G-15 | Does repeated assessment (testing multiple model/prompt versions) improve reliability, and at what cost? | Paper 2 recommends it but does not quantify benefits or costs | Paper 2 |
| G-16 | How sensitive are the results to prompt version, model version, and policy version changes? | Papers 5, 7 note version sensitivity but do not provide systematic testing frameworks | Papers 5, 7 |
| G-17 | What is the impact of evidence availability (public portfolio vs. none) on scoring, independent of actual skill quality? | Paper 4 identifies this but does not quantify the effect | Paper 4 |

---

## 10b. EGCA Architecture (from companion README)

The EGCA (Evidence-Governed Candidate Assessment) architecture is detailed in the companion working document at `/home/devsharma08/Downloads/README_resume_screening_research.md`. It defines eight key entities and components that map directly to the problems and opportunities identified across the eight research sources:

### Evidence States (EvidenceLedger)
| State | Meaning |
|---|---|
| `NOT_OBSERVED` | No relevant evidence was observed; not proof the claim is false |
| `SELF_CLAIMED` | Claim appears in resume/self-report but not independently verified |
| `INFERRED` | Claim inferred from related evidence; not direct evidence |
| `ARTIFACT_BACKED` | A relevant artifact supports the claim (subject to provenance checks) |
| `ASSESSED` | A suitable assessment provides evidence of the claimed competency |

### Two-Stage Architecture
- **Stage 1 -- Initial Screening:** Parse resumes into structured claims, normalize to canonical skill taxonomy, match against Role Blueprint, form preliminary ranked pool, Adaptive Gate (buffered pool K' > K)
- **Stage 2 -- Evidence Verification:** Verify extracted claims, record in EvidenceLedger, apply deterministic policy (OPA/Rego, P_hard), calculate uncertainty-aware assessment score (M_G), route uncertain cases to human review, Policy-Bounded Priority Queue (PBPQ) for final ranking

### Governed Closeness Metric (M_G)
```
M_G = f(E_prov, S_sem, U_unc), subject to the hard gate P_hard
```
Where: E_prov = evidence provenance/strength, S_sem = semantic alignment with role requirements, U_unc = uncertainty penalty, P_hard = deterministic hard-policy outcome.

### Audit Entities
- **EvidenceLedger:** candidate claims, sources, evidence states, provenance, verification history
- **ComplianceCertificate:** structured record of policy check outcomes
- **AuditTrailLog:** append-only record of decisions, score changes, rule evaluations, human interventions

### Design Principle
Separate four questions: (1) What does the resume claim? (2) What evidence supports the claim? (3) Does the candidate satisfy mandatory policy requirements? (4) How should eligible candidates be prioritized, and which cases need human review?

**Status:** All components are EGCA HYPOTHESES -- proposed design elements not experimentally validated by any of the eight sources. The companion README explicitly treats every component as requiring empirical testing.

---

## 11. EGCA Design Opportunities Mapped to Evidence

### Opportunity 1: Evidence Verification with Provenance Tracking

**Weakness addressed:** P-A1 (Unsupported inference), P-A2 (Hallucinated claims), P-A3 (Contradiction), P-A6 (False confidence), P-F1 (No verifiable explanations), P-F2 (No evidence provenance)

**Relevant EGCA components:** EvidenceLedger, Grounding verifier, Quality gate, evidence states (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED)

**Proposed mechanism:** 
1. Extract candidate claims with source spans (where in the resume each claim was found)
2. Independently verify each claim against its cited source
3. Label each claim with one of five evidence states: NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED
4. Keep extraction confidence separate from verification confidence
5. Do not treat unavailable public evidence as a negative skill judgment

**What research supports:** 
- Paper 6 (RAGTruth) provides the most direct evidence: 17,790 human-labeled responses show that retrieval does not guarantee grounding (43.1% hallucination rate even with source documents provided). Paper 6 proposes the four-label scheme as an EGCA adaptation.
- Paper 1 identifies "unsupported inference" as a specific failure type (Section 10.2) with the consequence that "a candidate may appear more qualified than the evidence justifies."
- Paper 2 identifies 31 unsupported inference instances and 35 hallucination instances in manual analysis.
- Paper 4 proposes the five-state evidence taxonomy (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) and recommends "do not treat unavailable public evidence as a negative skill judgment."
- Paper 7 warns: "Evidence provenance, deterministic policies, and uncertainty routing are architectural safeguards--but their presence does not prove that the overall system produces valid rankings."

**What is only an EGCA design hypothesis:**
- The five-state taxonomy from the README is a proposed adaptation, not a claim that it reproduces every label in RAGTruth.
- The specific implementation of claim-level verification using EvidenceLedger is not tested by any original paper.
- Whether separating extraction from verification actually reduces unsupported claims in hiring decisions is untested.

**Potential failure modes introduced:**
- Over-rejection of valid claims (false positives in detection) -- Paper 6 notes detectors can miss unsupported claims OR incorrectly flag valid evidence (pp. 7-8)
- Increased human review burden -- Paper 6: "A detector might reduce fabricated statements while increasing review workload"
- Missed evidence at the extraction stage -- Paper 1 notes transformer input-length limits can lose information (Section 6, pp. 7-8)

**Measurable experiment:**
- Build a claim-verification test set with: inflated skill levels, invented leadership, altered dates, fabricated metrics, unsupported achievements, and valid claims that must not be rejected (per Paper 6, Section 5)
- Compare progressively stronger baselines: (A) Basic LLM prompt, (B) LLM prompt with source excerpts, (C) Separate claim-level verifier, (D) Fine-tuned detector, (E) Verifier + correction + human review
- Metrics (from README): Precision/recall/F1 for unsupported-claim detection; citation correctness; false-flag rate; miss rate; consistency across repeated runs; reviewer agreement and review time; quality and auditability of final assessment
### Opportunity 2: Requirement-Specific Evaluation with Mandatory/Preferred Distinction

**Weakness addressed:** P-B1 (Keyword confusion), P-B2 (Wording mismatch), P-B3 (Mandatory/preferred confusion), P-B6 (Skill correlation)

**Relevant EGCA components:** Role Blueprint, Adaptive Gate, EvidenceLedger, deterministic policy (P_hard, OPA/Rego)

**Proposed mechanism:**
1. Parse job description preserving original text and source locations
2. Extract explicit skills, normalizing aliases against a versioned skill vocabulary
3. Infer additional skills using BERT–XMLC/CAB-inspired approach (but keep explicit, inferred, and approved requirements distinct)
4. Require reviewer approval for inferred requirements, recording the reviewer and rationale
5. Apply deterministic eligibility rules: strong optional skills cannot override failed mandatory conditions

**What research supports:**
- Paper 3 provides the strongest support: BERT–XMLC + CAB improved Recall@30 from 70.10% to 79.59% (+9.49pp), demonstrating skill inference can improve requirement coverage
- Paper 2 recommends: "Preserve original wording, mandatory status, and expected proficiency" and "explicitly classify each requirement before evaluating candidates"
- Paper 7 warns: "If mandatory and preferred requirements are ambiguous, downstream scoring cannot reliably correct that ambiguity"
- Paper 1 distinguishes participation from ownership from leadership: "Assisting with a task, independently owning it, and supervising others are not equivalent"

**What is only an EGCA design hypothesis:**
- Inferring additional skills using BERT–XMLC/CAB specifically for EGCA's job descriptions is proposed but not validated
- The claim that keeping inferred requirements distinct and requiring approval improves outcomes is not tested
- Whether mandatory/preferred distinction enforcement actually reduces false selections is untested

**Potential failure modes introduced:**
- Over-inclusion of inferred skills (Paper 3: "A model may suggest related skills that are plausible but unnecessary")
- False mandatory requirements from inferred skills (Paper 3: "If that inference is automatically treated as compulsory, qualified candidates could be rejected")
- Normalization failures when equivalent skills are described differently (Paper 2 recommends "log normalization failures instead of silently treating them as missing skills")

**Measurable experiment:**
- Baseline: explicit skill extraction and alias normalization only
- Enhanced system: baseline plus BERT–XMLC/CAB-inspired skill inference
- Primary measures: expert-rated precision@K, recall@K, nDCG@K, requirement approval rate, rate of inferred requirements judged irrelevant
- Downstream measure: whether candidate assessments change because of inferred requirements, with expert review of those changes
- Additional: test for related-skill confusion (does the model incorrectly treat a related skill as equivalent?)

**Baselines and acceptance criteria:**
- Baseline: keyword matching + semantic matching + basic LLM prompt
- Acceptance: inference improves recall@30 by ≥5pp without decreasing precision@10 by >10% from baseline; <5% of inferred requirements judged irrelevant by expert reviewers

**Conditions for failure:**
- If inferred skills are frequently irrelevant, qualified candidates may be excluded
- If normalization fails for common skill aliases, valid candidates may be missed
- If mandatory rules are too strict, the system may produce excessive false negatives

### Opportunity 3: Uncertainty Handling with Evidence States

**Weakness addressed:** P-A5 (Missing evidence ≠ missing ability), P-C1 (Score opacity), P-C2 (Unstable scores), P-F3 (No abstention policy)

**Relevant EGCA components:** EvidenceLedger, uncertainty routing, Governed Closeness Metric (GCM)

**Proposed mechanism:**
1. Distinguish evidence states: NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED
2. Do not treat unavailable public evidence (e.g., no GitHub portfolio) as a negative judgment
3. Route ambiguous, contradictory, or consequential cases to human review
4. Use evidence status, uncertainty, policy checks, and human review to support final assessment

**What research supports:**
- Paper 4 explicitly proposes the five-state evidence taxonomy (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) and recommends "do not treat unavailable public evidence as a negative skill judgment"
- Paper 1 recommends: "Distinguish direct support, partial support, ambiguity, contradiction, and evidence not found" and "Do not automatically equate an absent resume statement with a candidate lacking the qualification"
- Paper 6 recommends: "A verification failure must not automatically become a negative candidate judgment. 'Not established by the supplied evidence' and 'contradicted by evidence' are materially different outcomes"
- Paper 7 recommends: "Missing evidence, conflicting sources and unstable rankings should be visible states—not automatic rejection or fabricated certainty"

**What is only an EGCA design hypothesis:**
- The specific five-state taxonomy as applied to resume screening has not been experimentally validated
- Whether routing uncertain cases to human review improves outcomes without excessive review burden is untested
- The Governed Closeness Metric with uncertainty penalties has not been evaluated for disparate impact

**Potential failure modes introduced:**
- Over-abstention: sending too many cases to human review (Paper 8: "a system could appear fairer simply by abstaining more often")
- Under-abstention: forcing decisions on ambiguous cases
- Disparate impact on candidates with limited external evidence (Paper 4: "Test its component weights and whether uncertainty penalties disproportionately affect candidates with limited external evidence")

**Measurable experiment:**
- Measure: unjustified abstention rate (abstaining when a clear answer exists); discriminant validity (appropriate abstention on equal pairs); evidence quality distribution; policy correctness (mandatory violations); operational cost (review rate, latency, cost)
- Compare EGCA with and without uncertainty-based routing
- Acceptance: unjustified abstention < 15%; human-review rate < 30% of candidates; policy correctness = 100%

**Conditions for failure:**
- If uncertainty thresholds are too high, many valid candidates may be routed for review unnecessarily
- If uncertainty thresholds are too low, ambiguous cases may be decided incorrectly
- If evidence states are mislabeled, the entire uncertainty system breaks down

### Opportunity 4: Stage-Level Counterfactual Fairness Testing

**Weakness addressed:** P-D1 (Direct demographic cues), P-D2 (Indirect proxies), P-D3 (Intersectional disparities), P-D4 (Aggregate metrics), P-D5 (Unequal treatment)

**Relevant EGCA components:** JobRelevanceGate, counterfactual test suite, AuditTrailLog, stage-level disparity tracking, ComplianceCertificate

**Proposed mechanism:**
1. Create controlled resume variants that differ only in identity-signaling names or sociocultural markers while keeping qualifications constant
2. Run the complete EGCA pipeline (extraction → normalization → matching → Adaptive Gate → evidence verification → policy checks → ranking)
3. Compare stage-level outputs: extracted claims, evidence states, scores, eligibility decisions, rankings, advancement rates
4. Identify and review disparities: locate the earliest stage with material difference, investigate cause, record corrective actions
5. Regression tests after every model, prompt, policy, weight, threshold, or ranking change

**What research supports:**
- Paper 4 provides the strongest evidence for counterfactual testing: score differences of 0.3–0.5 points (on a 0–100 scale) translate to 1.4–1.7pp selection-probability differences at threshold 80
- Paper 8 demonstrates that 18 LLMs can recover demographic labels from indirect markers with macro-F1 of 0.925–0.997 for ethnicity, but also shows that removing specific markers (languages) sharply reduces performance (0.972 → 0.130)
- Paper 8 provides acceptance criteria: "Irrelevant-marker score difference: target zero in deterministic, controlled tests"
- Paper 5 proposes the test suite including demographic-cue tests, equivalent-wording tests, candidate-order tests, and prompt-robustness tests

**What is only an EGCA design hypothesis:**
- Whether EGCA's specific implementation of counterfactual testing detects and reduces disparities is untested
- Whether targeted safeguards (JobRelevanceGate, masking, etc.) actually reduce identified disparities without reducing assessment quality
- The proposed EGCA evaluation flow (counterfactual testing → stage-level tracking → corrective action) has not been experimentally validated

**Potential failure modes introduced:**
- False positives: flag legitimate job-related differences as bias
- False negatives: miss bias that only appears with specific marker combinations
- Over-masking: removing legitimate identity-referencing qualifications (e.g., bilingual requirements)
- Gaming: candidates learning to optimize for the relevance gate rather than genuinely matching requirements

**Measurable experiment:**
- Create matched candidate resumes that differ only in controlled, job-irrelevant identity cues
- Run the complete EGCA pipeline
- Compare every stage: extraction, normalization, matching, Adaptive Gate, evidence verification, policy checks, ranking
- Measure: score differences, ranking reversals, eligibility outcomes, advancement rates, top-K shortlist selection
- Compare baseline EGCA pipeline with the same pipeline under controlled counterfactual changes to identity cues
- Primary question: Can stage-level counterfactual fairness testing detect and localize identity-sensitive disparities, and do targeted safeguards reduce those disparities without reducing job-related assessment quality?

**Baselines and acceptance criteria:**
- Baseline: LLM-only screening without fairness controls
- Acceptance: score difference < 0.5 points in deterministic tests; no unexplained shortlist/eligibility changes caused solely by irrelevant details; no explicitly excluded information influencing scoring

**Conditions for failure:**
- If the test set does not cover sufficient demographic intersections, disparities may go undetected
- If job requirements inherently correlate with demographic markers, counterfactual tests may flag valid differences
- If model versions change between tests, disparities may be attributed to the wrong cause

### Opportunity 5: Controlled Test Suite for Pipeline Validation

**Weakness addressed:** P-C2 (Unstable scores), P-C4 (Context sensitivity), P-C5 (Pairwise intransitivity), P-F1 (No audit trails), P-F3 (No abstention policy)

**Relevant EGCA components:** Validity-audit flow, PBPQ, baseline consistency testing, prompt sensitivity testing, stability checks, AuditTrailLog

**Proposed mechanism:**
1. Define job-related ground truth (versioned job blueprint; mandatory vs. preferred criteria)
2. Generate controlled resume pairs: add/remove relevant qualifications or change only irrelevant attributes
3. Run baseline and EGCA with fixed model, prompt, policy, and scoring versions
4. Compare outputs by stage: evidence states, policy decisions, abstentions, scores, rankings
5. Track root causes and test every model, prompt, or policy change

**Test suite components:**
- Relevant-skill mutation: Adding a genuinely relevant qualification should not make the candidate rank lower
- Required-qualification removal: System should apply declared rules while distinguishing missing evidence from confirmed absence
- Equivalent-wording test: Rephrasing a resume without changing qualifications should not create unjustified ranking changes
- Demographic-cue test: Changing an irrelevant identity cue should not create unjustified preference
- Prompt-paraphrase test: Measure changes in score, ranking, and abstention
- Candidate-order test: Swapping Resume A and B should not reverse underlying preference
- Missing-job-information test: Ambiguous criteria should trigger clarification or review, not invented requirements
- Top-K boundary test: Small controlled changes near shortlist cutoff should be measured for advancement effect

**What research supports:**
- Paper 5 proposes the entire test suite and metric framework; Papers 1, 4, 7, and 8 independently recommend similar tests as design implications
- Paper 7 provides empirical evidence of the need: GPT scores changed by 25.6 points when only company context changed
- Paper 7 recommends specific metrics: score variance, rank stability, paired score differences, effect sizes

**What is only an EGCA design hypothesis:**
- The proposed test suite as an integrated system has not been experimentally validated
- The specific metrics (criterion validity, unjustified selection, unjustified abstention, discriminant validity) are proposed by Paper 5 but not validated against actual hiring outcomes
- Whether passing these tests correlates with better real-world hiring decisions is unproven

**Potential failure modes introduced:**
- Overfitting to test cases rather than general performance
- False confidence from passing synthetic tests while failing on real resumes (Paper 5, Paper 7, Paper 8 all warn about this)
- Test suite complexity overwhelming implementation resources

**Measurable experiment:**
- Run controlled test set where only one factor changes at a time (job context, prompt wording, evidence availability, mandatory qualification)
- Compare LLM-only baseline against EGCA using predefined metrics
- Metrics: criterion validity (% of unequal-qualification pairs where more-qualified resume is selected), unjustified selection rate, unjustified abstention rate, discriminant validity, selection-rate differences by demographic signal, ranking stability, evidence quality, policy correctness, operational cost

**Baselines and acceptance criteria:**
- Baseline: LLM-only resume comparison and ranking system
- EGCA: evidence provenance + deterministic policy checks + uncertainty-based human routing
- Acceptance: criterion validity ≥ 90%; unjustified selection ≤ 5%; policy violations = 0; ranking stability ≥ 85%; human-review rate ≤ 30%

**Conditions for failure:**
- If controlled tests don't reflect real-world complexity, the system may pass tests but fail in deployment
- If test coverage is incomplete, failure modes may go undetected
- If baselines are too weak, improvements may be illusory

---

## 12. Proposed Experiments and Evaluation Criteria

### Experiment 1: Claim-Level Verification Effectiveness

**Objective:** Test whether a separate claim-level verifier reduces unsupported claims compared to a basic LLM assessment.

**Design:** 
- Build a claim-verification test set with inflated claims, invented leadership, altered dates, fabricated metrics, and valid claims
- Baseline A: Basic LLM prompt
- Baseline B: LLM prompt with source excerpts
- EGCA: Separate claim-level verifier + quality gate + human review
- Evaluate all three on the same test set

**Metrics:** 
- Precision/recall/F1 for unsupported-claim detection
- False-flag rate (valid claims incorrectly rejected)
- Miss rate (unsupported claims surviving)
- Consistency across repeated runs
- Reviewer agreement and time

**Acceptance criteria:** 
- F1 ≥ 0.75; false-flag rate ≤ 5%; miss rate ≤ 10%; consistency ≥ 90%

**Conditions for failure:** 
- If the verifier has high false-positive rates, valid candidates may be excluded
- If the verifier is too conservative, it may miss unsupported claims

### Experiment 2: Context Sensitivity Reduction

**Objective:** Test whether EGCA reduces score changes caused by irrelevant input changes compared to an LLM-only baseline.

**Design:**
- Create controlled resume pairs: identical qualifications, different company context or prompt wording
- Baseline: LLM-only scoring
- EGCA: deterministic policy + evidence verification + uncertainty routing
- Measure score differences, ranking changes, and shortlist membership changes

**Metrics:**
- Score difference for equivalent resumes under different contexts
- Ranking correlation (Spearman's ρ) across context changes
- Shortlist overlap (Jaccard index)
- Human-review rate triggered by uncertainty

**Acceptance criteria:**
- Score difference < 5 points (on 0–100 scale) for equivalent candidates
- Ranking correlation ≥ 0.90 across context changes
- Shortlist overlap ≥ 0.80
- No unexplained shortlist changes from irrelevant detail changes

**Conditions for failure:**
- If the policy is too rigid, it may reject valid candidates for minor evidence differences
- If uncertainty thresholds are too low, too many cases may be routed for review

### Experiment 3: Counterfactual Fairness Testing

**Objective:** Test whether EGCA reduces demographic score disparities compared to an LLM-only baseline.

**Design:**
- Create matched resume pairs: identical qualifications, different identity cues (names, hobbies, activities)
- Baseline: LLM-only scoring
- EGCA: with and without JobRelevanceGate
- Compare stage-level outputs: evidence extraction, policy decisions, scores, rankings

**Metrics:**
- Score difference between demographic variants (target: 0 in deterministic tests)
- Selection-rate differences at shortlist cutoff
- Evidence record changes (claims extracted, states assigned)
- False-flag rate (legitimate differences flagged as bias)

**Acceptance criteria:**
- Score difference < 0.5 points in controlled tests
- No unexplained shortlist changes from irrelevant demographic cues
- Evidence provenance: every score-bearing reason maps to a source and job requirement

**Conditions for failure:**
- If the relevance gate is too aggressive, legitimate job-related signals may be filtered
- If job requirements inherently correlate with demographic markers, valid differences may be flagged

### Experiment 4: Shortlist Stability Near Cutoff

**Objective:** Test whether EGCA maintains shortlist stability near the top-K cutoff compared to LLM-only ranking.

**Design:**
- Create resume sets where candidates cluster near the shortlist boundary
- Baseline: LLM-only ranking
- EGCA: evidence verification + deterministic policies + uncertainty routing
- Perturb scores slightly and measure shortlist membership changes

**Metrics:**
- Shortlist overlap (Jaccard) under small perturbations
- Boundary flip rate (candidates crossing in/out of top-K)
- Required-qualification compliance rate
- Human-review rate

**Acceptance criteria:**
- Shortlist overlap ≥ 0.90 under perturbations
- Boundary flip rate ≤ 10%
- Mandatory-rule compliance = 100%
- Human-review rate ≤ 25%

**Conditions for failure:**
- If uncertainty routing sends too many cases to review, the system may be impractical
- If the buffer is too large, efficiency may suffer

### Experiment 5: Requirement Inference vs. Over-Inclusion Trade-off

**Objective:** Test whether BERT–XMLC/CAB-inspired skill inference improves requirement coverage without increasing irrelevant requirements.

**Design:**
- Baseline: explicit skill extraction and alias normalization only
- Enhanced: baseline plus skill inference
- Expert reviewers judge relevance of inferred skills

**Metrics:**
- Expert-rated precision@K, recall@K, nDCG@K
- Requirement approval rate
- Rate of inferred requirements judged irrelevant
- Downstream: change in candidate assessments due to inferred requirements

**Acceptance criteria:**
- Recall@K improvement ≥ 5pp over baseline
- Precision@K decrease ≤ 10% from baseline
- < 5% of inferred requirements judged irrelevant
- No qualified candidates excluded due to inferred requirements

**Conditions for failure:**
- If inferred skills are frequently irrelevant, false negatives may increase
- If normalization fails, valid candidates may be missed

---

## 13. High-Priority Research Questions

| Priority | Research Question | Rationale | Supporting Sources |
|----------|-------------------|-----------|---------------------|
| 1 | Does evidence-grounded claim verification reduce unsupported claims and hallucinations in candidate assessments, and at what cost in review burden? | This is the foundational EGCA proposition. If it doesn't work, the entire architecture lacks justification. | Papers 1, 2, 6, 7 |
| 2 | Can stage-level counterfactual fairness testing detect and localize identity-sensitive disparities, and do targeted safeguards reduce them without reducing job-related assessment quality? | Fairness is the most socially and legally critical concern. Without demonstrated effectiveness, evidence grounding alone may not ensure fair outcomes. | Papers 4, 5, 8 |
| 3 | Does deterministic policy enforcement improve mandatory-rule compliance without excessive human review burden? | EGCA's deterministic policies are a key safeguard. If they produce too many false positives or negatives, the system is either too rigid or too permissive. | Papers 4, 5, 7 |
| 4 | Does EGCA improve ranking validity and resistance to irrelevant input changes compared with an LLM-only baseline? | This tests EGCA's core value proposition: that evidence provenance, deterministic policies, and uncertainty routing make screening more robust. | Papers 5, 7 |
| 5 | Does uncertainty-based human routing improve decision quality, and what is the optimal threshold for routing? | The trade-off between review burden and accuracy is the primary practical concern for deployment. | Papers 1, 2, 5, 6 |
| 6 | Does BERT–XMLC/CAB-inspired skill inference improve requirement coverage without increasing irrelevant or incorrect requirements? | This tests whether job-side inference adds value to EGCA compared to explicit skill extraction alone. | Paper 3 |
| 7 | How does the five-state evidence taxonomy (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) affect scoring and ranking quality? | This is a proposed EGCA innovation. If the taxonomy doesn't correspond to meaningful scoring distinctions, it adds complexity without value. | Paper 4 |
| 8 | What is the relationship between hallucination detection performance and actual hiring decision quality? | Papers 5 and 6 propose extensive metrics, but it is unclear which metrics predict better hiring outcomes. | Papers 5, 6, 7 |

---

## 14. Final Evidence-Based Conclusions

### What is established with high confidence

1. **LLM-based resume scoring is highly sensitive to context.** Paper 7 demonstrates that GPT-4 Turbo's score for an identical resume changed by 25.6 points (50.5 → 76.1) when only the company context changed. This is the strongest single finding in the corpus and does not require any interpretation — it is a directly observed empirical result.

2. **LLMs generate unsupported claims at high rates.** Paper 6 (RAGTruth) establishes that 43.1% of 17,790 AI-generated responses contained human-labeled hallucination spans. Paper 2 finds 35 hallucination instances and 31 unsupported inference instances in its manual analysis. These are directly measured, not inferred.

3. **Direct and indirect demographic cues influence LLM scores.** Paper 4 establishes that identity-signaling names produce systematic score differences (e.g., Black men scored 0.303 points lower than White men on a 0–100 scale). Paper 8 establishes that models recover demographic labels from indirect markers (ethnicity macro-F1 up to 0.997) with high accuracy.

4. **Human evaluators disagree at rates that matter.** Paper 1 reports Cohen's κ = 0.61 (moderate agreement); Paper 2 reports Fleiss' κ ≈ 0.786 (substantial but not perfect). Both imply that using human labels as ground truth introduces ambiguity into evaluations.

5. **Different LLMs produce substantially different scores for identical inputs.** Paper 7 shows GPT (~50.5), Claude (~69.4), and Gemini (~72.8) all scored the same resume differently. Paper 8 generalizes this across 18 models.

6. **Missing evidence is systematically confused with negative evidence.** Four independent sources (Papers 1, 2, 6, 7) explicitly recommend distinguishing "not established" from "candidate lacks qualification." No source demonstrates that current systems make this distinction correctly.

### What is established with moderate confidence

1. **Skill inference from job descriptions can improve recall but risks over-inclusion.** Paper 3's BERT–XMLC + CAB improved Recall@30 by 9.49 percentage points. However, the paper itself warns that "high recall does not establish that the suggested skills are all appropriate, necessary, or approved by a human reviewer."

2. **Accuracy metrics conceal error types.** Paper 1 reports 73.26% accuracy for competence prediction but explicitly warns that "accuracy does not establish that the system used the claimed supporting evidence or that its explanation reflects its actual decision process." Paper 2 reports correlations of 0.34–0.57 but warns that "correlation does not measure the percentage of correct ratings."

3. **Anonymisation alone is insufficient for fairness.** Paper 8 demonstrates that removing names does not remove demographic signal from hobbies, languages, or activities. However, the study uses synthetic resumes and Singapore-specific data, limiting generalizability.

### What remains uncertain / unproven

1. **Whether any proposed EGCA component actually works.** Evidence extraction, grounding verification, deterministic policy enforcement, uncertainty routing, JobRelevanceGate, counterfactual testing — all are recommended design improvements. **None has been experimentally validated in any source.** The consistent message across all eight sources is: "These are proposals to test, not established results."

2. **Whether evidence grounding reduces bias.** Papers 4 and 8 both state that evidence provenance alone does not eliminate bias. The question of whether evidence-grounded systems are fairer than ungrounded ones is explicitly open.

3. **Whether passing controlled tests predicts real-world hiring success.** Papers 5, 7, and 8 all warn that synthetic benchmark success does not establish real-world hiring accuracy or demographic fairness. The relationship between test-suite performance and actual hiring quality is unmeasured.

4. **The optimal threshold for uncertainty-based human review.** No source provides evidence on what percentage of cases should be routed for review or what the cost-benefit trade-off looks like. Paper 8 warns: "Otherwise, a system could appear fairer simply by abstaining more often or sending nearly every case to human review."

5. **The impact of evidence availability on scoring.** Paper 4 identifies that "candidates with no public portfolio or GitHub presence may be treated differently" but does not quantify the effect or test mitigation approaches.

6. **Cross-organizational and cross-cultural generalizability.** All studies use specific datasets from specific contexts (Emory University, Singapore, Gurgaon, US entry-level jobs). The extent to which findings generalize is unknown and explicitly unproven by all sources.

### EGCA design principle: Assume unvalidated until tested

The synthesis concludes with a single overriding principle supported by all eight sources: **the presence of a component in the EGCA architecture does not constitute evidence that the component works.** Every source consistently distinguishes between:
- What the research demonstrates (SOURCE FINDING)
- What the research authors recommend or interpret (SOURCE INTERPRETATION)
- What EGCA proposes to address a problem (EGCA HYPOTHESIS)
- What the research cannot tell us (EVIDENCE GAP)

The strongest research direction for EGCA is to treat its architecture as a hypothesis to be tested, not a solution to be assumed. The five highest-priority experiments (Section 13) provide a roadmap for converting EGCA design implications into empirically validated capabilities.

---

## 15. Source Traceability Index

### Master Table: All Problems with Source Traceability

| Problem ID | Canonical Problem | Supporting Sources | # of Independent Studies | Key Evidence | Conditions | Confidence | EGCA Relevance |
|-----------|------------------|---------------------|--------------------------|-------------|------------|------------|----------------|
| P-A1 | Unsupported inference | Papers 1, 2, 6, 7 | 4 | Paper 2: 31 instances; Paper 4: 35 instances; Paper 6: 43.1% hallucination rate | LLM generates claims without verifying against evidence | SOURCE FINDING | Core claim verification needed |
| P-A2 | Hallucinated/invented claims | Papers 2, 6, 7 | 3 | Paper 6: 17,790 responses, 14,289 hallucination spans; Paper 2: 35 instances | RAG context; LLM generation without grounding | SOURCE FINDING | Quality gate needed |
| P-A3 | Contradiction | Papers 1, 6 | 2 | Paper 6: "generated assessment may change a date, number, role" | Model misreads or alters source facts | SOURCE FINDING | Claim-level checking needed |
| P-A4 | Missed evidence | Papers 1, 7 | 2 | Paper 1: transformer input-length limits; Paper 7: benchmark tests only large gaps | Long resumes; trimming; section pruning | SOURCE FINDING | Evidence extraction needed |
| P-A5 | Missing evidence ≠ missing ability | Papers 1, 2, 6, 7 | 4 | All four papers explicitly recommend distinction | Absent resume statement treated as disqualifying | SOURCE FINDING | Evidence states needed |
| P-A6 | Semantic false confidence | Papers 1, 7, 8 | 3 | Paper 1: "worked with team" vs. "supervised team" | Semantic similarity ≠ competence verification | SOURCE INTERPRETATION | Evidence verification needed |
| P-B1 | Keyword matching confusion | Papers 1, 3 | 2 | Paper 1: Python course vs. Python deployment; Paper 3: exact-word matching | Exact term matching | SOURCE FINDING | Skill normalization needed |
| P-B2 | Wording mismatch | Paper 1 | 1 | Paper 1: "Candidates describe relevant responsibilities using different phrases" | Terminology variation | SOURCE FINDING | Semantic matching needed |
| P-B3 | Mandatory/preferred confusion | Papers 2, 7 | 2 | Paper 2: "incorrectly treating it as compulsory can change who advances" | Ambiguous requirement classification | SOURCE FINDING | Role blueprint needed |
| P-B4 | Seniority confusion | Paper 1 | 1 | Paper 1: assisting vs. owning vs. supervising | Experience level interpretation | SOURCE FINDING | Evidence verification needed |
| P-B5 | Transferable experience missed | Papers 1, 3 | 2 | Paper 1: unconventional experience description | Fixed vocabulary; unusual phrasing | SOURCE FINDING | Flexible matching needed |
| P-B6 | Skill correlation misleading | Paper 3 | 1 | Paper 3: "co-occurrence alone does not prove necessity" | CAB uses co-occurrence as signal | SOURCE INTERPRETATION | Requirement approval needed |
| P-C1 | Score opacity/miscalibration | Papers 1, 2, 7 | 3 | Paper 7: "80/100 does not mean 80% probability"; Paper 2: "raw score ≠ probability" | Treating scores as probabilities | SOURCE FINDING | Calibration needed |
| P-C2 | Inconsistent/unstable scores | Papers 1, 2, 5, 7 | 4 | Paper 2: "Model outputs can vary with prompts, settings"; Paper 5: "prompt changes silently change decisions" | Prompt, settings, model version, input format changes | SOURCE FINDING | Stability testing needed |
| P-C3 | Model-dependent scoring | Papers 4, 7, 8 | 3 | Paper 7: GPT 50.5 vs Claude 69.4 vs Gemini 72.8; Paper 8: 18 LLMs show different patterns | Different model architectures | SOURCE FINDING | Model selection matters |
| P-C4 | Context/prompt sensitivity | Papers 5, 7, 8 | 3 | Paper 7: 25.6-point GPT score change with context; Paper 8: rationale prompting worsens fairness | Company context, prompt wording, formatting | SOURCE FINDING | Sensitivity testing needed |
| P-C5 | Pairwise intransitivity | Paper 5 | 1 | Paper 5: "A > B, B > C, C > A" | Pairwise comparison without global ranking | SOURCE INTERPRETATION | Global ranking needed |
| P-C6 | Shortlist cutoff instability | Papers 4, 5, 7, 8 | 4 | Paper 4: 1.7pp selection difference at threshold; Paper 5: "struggle near cutoff" | Scores near shortlist boundary | SOURCE FINDING + INTERPRETATION | Buffer + stability testing needed |
| P-D1 | Direct demographic cue influence | Papers 4, 8 | 2 | Paper 4: ±0.3 to +0.5 score points; Paper 8: name removal insufficient | Identity-signaling names | SOURCE FINDING | Relevance gate needed |
| P-D2 | Indirect proxies survive anonymization | Paper 8 | 1 | Paper 8: F1 0.925–0.997 from hobbies/languages; drop to 0.130 when languages removed | Anonymization removes only names | SOURCE FINDING | Counterfactual testing needed |
| P-D3 | Intersectional disparities hidden | Papers 4, 8 | 2 | Paper 4: "Black women vs. White men"; Paper 8: "group patterns differ across combinations" | Single-dimension analysis | SOURCE FINDING | Intersectional evaluation needed |
| P-D4 | Aggregate metrics conceal disparities | Papers 4, 5, 8 | 3 | Paper 5: "differences hidden by aggregate metrics"; Paper 8: "measure at decision boundaries" | Aggregate-level analysis | SOURCE INTERPRETATION | Stage-level tracking needed |
| P-D5 | Unequal treatment of equals | Papers 7, 8 | 2 | Paper 8: "models did not consistently satisfy fairness targets"; Paper 7: human vs LLM disagreement | Equivalent qualifications | SOURCE FINDING | Fairness testing needed |
| P-E1 | Human label inconsistency | Papers 1, 2 | 2 | Paper 1: κ = 0.61; Paper 2: κ ≈ 0.786 | Human annotation protocols | SOURCE FINDING | Rubric + multiple reviewers needed |
| P-E2 | Accuracy conceals errors | Papers 1, 2, 5, 7 | 4 | Paper 1: "accuracy does not tell which candidates are misclassified"; Paper 7: "accuracy ≠ validity" | Accuracy-only evaluation | SOURCE FINDING | Error-type tracking needed |
| P-E3 | Training/test data leakage | Paper 1 | 1 | Paper 1: "same candidate or near-duplicate resumes in both splits" | Data split methods | SOURCE FINDING | Data separation needed |
| P-E4 | Limited generalizability | Papers 1-8 (all) | 8 | Paper 1: "Emory CRC only"; Paper 8: "Singapore data may differ in India" | Domain, population, context | SOURCE FINDING | Multiple evaluation settings needed |
| P-E5 | Incomplete reference labels | Paper 3 | 1 | Paper 3: "predicted skill may be genuinely relevant but counted as incorrect" | Label completeness | SOURCE FINDING | Expert validation needed |
| P-E6 | Synthetic data limitations | Papers 5, 7, 8 | 3 | Paper 8: "88.8% realistic but stereotypes unvalidated"; Paper 7: "synthetic data cannot establish real-world fairness" | Synthetic test data | SOURCE FINDING + INTERPRETATION | Real-data validation needed |
| P-F1 | No verifiable explanations | Papers 1, 2, 4, 7, 8 | 5 | Paper 1: "fail to explain decisions with verifiable evidence"; Paper 2: "fluent explanation ≠ proof" | LLM explanation generation | SOURCE FINDING | Audit trail needed |
| P-F2 | No evidence provenance | Papers 4, 5, 6, 8 | 4 | Paper 6: "extracting a claim does not establish it is true"; Paper 8: "store evidence ID, source span" | Missing provenance tracking | SOURCE INTERPRETATION | EvidenceLedger needed |
| P-F3 | No abstention/uncertainty policy | Papers 1, 5, 6, 7 | 4 | Paper 1: "do not automatically treat absent as negative"; Paper 5: "no explicit abstention policy" | Forced decisions on ambiguous cases | SOURCE INTERPRETATION | Uncertainty routing needed |
| P-F4 | Silent prediction-to-requirement conversion | Papers 3, 8 | 2 | Paper 3: "every inferred skill should retain origin and approval status"; Paper 8: "relevance gate needed" | Inferred skills treated as mandatory | SOURCE INTERPRETATION | Skill approval process needed |
| P-F5 | Evidence availability bias | Papers 1, 4, 8 | 3 | Paper 4: "no public portfolio ≠ lack of capability"; Paper 1: "absent ≠ negative" | Missing artifacts treated as negative | SOURCE INTERPRETATION | Evidence availability tracking needed, EvidenceLedger NOT_OBSERVED state |

### Additional Source: EGCA Companion README

| Component | Source Document | Status |
|-----------|----------------|--------|
| Evidence states (NOT_OBSERVED, SELF_CLAIMED, INFERRED, ARTIFACT_BACKED, ASSESSED) | `/home/devsharma08/Downloads/README_resume_screening_research.md` Section 12.4 | EGCA HYPOTHESIS |
| Two-stage architecture (Stage 1 screening + Stage 2 verification) | README Section 12.3 | EGCA HYPOTHESIS |
| Adaptive Gate (buffered pool K' > K) | README Section 12.3 | EGCA HYPOTHESIS |
| Governed Closeness Metric M_G = f(E_prov, S_sem, U_unc), P_hard | README Section 12.7 | EGCA HYPOTHESIS |
| Policy-Bounded Priority Queue (PBPQ) | README Section 12.8 | EGCA HYPOTHESIS |
| Audit entities (EvidenceLedger, ComplianceCertificate, AuditTrailLog) | README Section 12.9 | EGCA HYPOTHESIS |
| Proposed baselines (keyword, embedding, LLM-only, EGCA) | README Section 14 | EGCA HYPOTHESIS |
| Proposed test scenarios (repeated-run, paraphrase, context changes, etc.) | README Section 14 | EGCA HYPOTHESIS |
| Proposed metrics (stability, agreement, coverage, cost) | README Section 14 | EGCA HYPOTHESIS |
| Paper 7 full reference: Varshney & Ganuthula, arXiv:2507.08019 | README Section 19 | SOURCE FINDING (paper identification) |

**Note:** The EGCA companion README (`README_resume_screening_research.md`) provides the detailed architecture specification referenced by Papers 4, 5, 7, and 8 when they mention "EGCA components." It is not an original research source but an EGCA design proposal document. All EGCA components listed above are EGCA HYPOTHESES — not experimentally validated by any of the eight research sources.

### Key
- **SOURCE FINDING:** Explicitly reported or demonstrated by a source
- **SOURCE INTERPRETATION:** Explanation or conclusion offered by the source authors
- **CROSS-STUDY SYNTHESIS:** Conclusion supported by comparison across sources
- **EGCA HYPOTHESIS:** Proposed design implication not yet validated
- **EVIDENCE GAP:** Question the available research cannot answer

---

## Appendix: Problem Distribution Across Sources

```
Paper 1 (EMNLP 2020, Emory CRC):        17 problems (P-A1, P-A3-P-A6, P-B1-P-B2, P-B4-P-B5, P-C1-P-C2, P-E1-P-E4, P-F1, P-F5)
Paper 2 (2025, GPT-4 vs human):          11 problems (P-A1-P-A2, P-A5-P-A6, P-B3, P-C1-P-C2, P-E1-P-E2, P-F1, P-F3)
Paper 3 (COLING 2020, skill retrieval):   6 problems (P-B1, P-B5-P-B6, P-E4-P-E5, P-F4)
Paper 4 (PNAS Nexus 2025, bias study):    10 problems (P-C1, P-C6, P-D1, P-D3-P-D5, P-E4, P-F1, P-F2, P-F5)
Paper 5 (Audit/testing framework):       10 problems (P-C2-P-C6, P-D4, P-E2, P-E6, P-F2-F-F3)
Paper 6 (RAGTruth, hallucination):       7 problems (P-A1-P-A3, P-A5-P-A6, P-F2-P-F3)
Paper 7 (Signal or Noise):               14 problems (P-A1-P-A5, P-B1-P-B3, P-C1-P-C4, P-C5-P-C6, P-D1, P-D5, P-E2, P-E4, P-E6, P-F1, P-F3, P-F5)
Paper 8 (Small Changes, bias):           11 problems (P-B4, P-C1, P-C3-P-C6, P-D1-P-D4, P-F2, P-F4)

Note: Paper 5's content is incomplete (sections 1-3 missing), so its problem count may be understated.

Total unique problems: 34
Problems supported by ≥3 independent sources: 16 (P-A1, P-A2, P-A5, P-A6, P-C1, P-C2, P-C3, P-C4, P-C6, P-D4, P-E2, P-E4, P-E6, P-F1, P-F2, P-F3)
Problems supported by ≥4 independent sources: 9 (P-A1, P-A5, P-C2, P-C6, P-E2, P-E4, P-F1, P-F2, P-F3)
Problems supported by exactly 1 source: 7 (P-B2, P-B4, P-B6, P-C5, P-D2, P-E3, P-E5)

Note: Some problems appear in multiple papers within the same tier but address different aspects. The count above reflects distinct independent studies (original papers), not secondary report repetitions.

Paper-level totals: 18 + 11 + 6 + 10 + 11 + 7 + 15 + 10 = 88 total problem-paper assignments.

Note: Paper 5 is incomplete (sections 1-3 missing), so its problem count may be understated.
```

Note: Some problems appear in multiple papers within the same tier but address different aspects. The count above reflects distinct independent studies (original papers), not secondary report repetitions.

Paper-level totals: 18 + 11 + 6 + 10 + 11 + 7 + 15 + 10 = 88 total problem-paper assignments.

Note: Paper 5 is incomplete (sections 1-3 missing), so its problem count may be understated.### Common Problems Ranked by Independent-Study Support

The following ranking is based on the number of independent sources that directly report or strongly imply each problem. "Independent source" means a distinct original study, not multiple summaries of the same paper. Problems with the same source count are grouped together.

**Problems supported by 5–8 independent studies:**
| Problem ID | Canonical Problem | # Sources |
|-----------|-------------------|----------|
| P-E4 | Limited generalizability across jobs, industries, populations | 8 |
| P-F1 | Lack of verifiable explanations and audit trails | 5 |

**Problems supported by 4 independent studies:**
| Problem ID | Canonical Problem | # Sources |
|-----------|-------------------|----------|
| P-A1 | Unsupported inference | 4 |
| P-A5 | Missing evidence mistaken for missing ability | 4 |
| P-C2 | Inconsistent/unstable scores | 4 |
| P-C6 | Shortlist cutoff instability | 4 |
| P-E2 | Accuracy conceals important error types | 4 |
| P-F2 | No evidence provenance tracking | 4 |
| P-F3 | No abstention/uncertainty policy | 4 |

**Problems supported by 3 independent studies:**
| Problem ID | Canonical Problem | # Sources |
|-----------|-------------------|----------|
| P-A2 | Hallucinated/invented claims | 3 |
| P-A6 | Semantic similarity creates false confidence | 3 |
| P-C1 | Score opacity and miscalibration | 3 |
| P-C3 | Model-dependent scoring | 3 |
| P-C4 | Context and prompt sensitivity | 3 |
| P-D4 | Aggregate metrics conceal disparities | 3 |
| P-E6 | Synthetic data limitations | 3 |
| P-F5 | Evidence availability effects not tracked | 3 |

**Problems supported by 2 independent studies:**
| Problem ID | Canonical Problem | # Sources |
|-----------|-------------------|----------|
| P-A3 | Contradiction (claim conflicts with source) | 2 |
| P-A4 | Missed evidence | 2 |
| P-B1 | Keyword matching confusion | 2 |
| P-B3 | Mandatory vs. preferred requirement confusion | 2 |
| P-B5 | Transferable/unusually described experience missed | 2 |
| P-D1 | Direct demographic cues influence scores | 2 |
| P-D3 | Intersectional disparities hidden by aggregates | 2 |
| P-D5 | Unequal treatment of equivalent candidates | 2 |
| P-E1 | Human label inconsistency | 2 |
| P-F4 | Silent prediction-to-requirement conversion | 2 |

**Problems supported by 1 independent study:**
| Problem ID | Canonical Problem | # Sources |
|-----------|-------------------|----------|
| P-B2 | Resume wording differs from job description | 1 |
| P-B4 | Seniority/experience-level confusion | 1 |
| P-B6 | Skill correlation produces misleading conclusions | 1 |
| P-C5 | Pairwise comparison intransitivity | 1 |
| P-D2 | Indirect proxies survive anonymization | 1 |
| P-E3 | Training/test data leakage | 1 |
| P-E5 | Incomplete reference labels distort evaluation | 1 |

**Summary statistics:**
- Total unique problems: 34
- Supported by ≥3 independent studies: 16
- Supported by ≥4 independent studies: 9
- Supported by exactly 1 study: 7
- Most widely reported: P-E4 (generalizability, 8 sources), P-F1 (audit trails, 5 sources)

Notes:
- **P-E4 (generalizability)** appears in all 8 sources — every study acknowledges that results do not generalize beyond their specific context. Despite high count, this is a methodological caveat rather than a direct failure mode.
- **P-A5** is the most practically severe problem despite having 4 sources — four independent studies explicitly recommend distinguishing absent evidence from negative evidence, and all note that current systems fail to do so.
- **P-F1** (no verifiable explanations) and **P-C6** (shortlist cutoff instability) each have 4-5 sources, making them among the most well-supported findings.
- Problems with 1 source are not necessarily unimportant. P-D2 (indirect proxies, Paper 8) and P-E3 (data leakage, Paper 1) are unique findings with potentially broad implications.

---

