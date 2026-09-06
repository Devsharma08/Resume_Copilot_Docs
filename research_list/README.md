# Research Index

This folder contains the research that explains why ordinary resume screening is insufficient and how governed AI decision systems could address those weaknesses.

## Research Structure

### 1. Problem Definition

[Root Cause Analysis and Target Problem Blueprint](root_cause_analysis_target_problem_blueprint.md)

Traces the problem from practical symptoms such as keyword gaming, prompt injection, missing hard requirements, and arbitrary scores to the research target: governed AI decision systems with evidence, policy, uncertainty, and audit controls.

### 2. Selection and Metric Design

[Dynamic Selection Paradigms and Governed Closeness Metrics](dynamic_selection_paradigms_governed_closeness_metrics.md)

Examines static thresholding versus dynamic top-K priority queues, identifies the false role-model risk, and defines the governed closeness metric and policy-bounded priority queue.

### 3. Interactive Exploration

[Candidate Selection Closeness Governance Simulator](candidate_selection_closeness_governance_simulator.html)

An interactive browser-based research simulator for exploring selection paradigms, queue behavior, governed closeness, and research-roadmap integration.

### 4. Source Conversation Log

[first_all.txt](first_all.txt)

Preserves the original research prompts and responses that led to the current problem framing. It is retained as source context rather than treated as the formal specification.

## Conceptual Flow

```text
Practical screening failures
        |
        v
Root-cause and target-problem definition
        |
        v
Governed AI architecture
        |
        v
Evidence-aware closeness metric
        |
        v
Policy-bounded streaming selection
        |
        v
Simulator and future experiments
```

## Research Boundary

These notes are exploratory. They support future product and governance decisions, but they do not replace the current MVP product, backend, or database specifications.
