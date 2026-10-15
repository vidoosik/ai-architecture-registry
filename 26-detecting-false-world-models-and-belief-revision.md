# 26 — Detecting false world models and revising explanations

**Status:** literature-backed principles plus proposed experiments; not an architecture selection.

## Core question

How can a system detect that its current explanation of the world is wrong, identify which assumptions caused the error, and revise only what the evidence warrants—without oscillating or blindly trusting its own critique?

## Findings

### 1. Belief revision is a separate capability from deduction

AGM belief-revision theory formalizes how a belief set changes when new information arrives. If evidence conflicts with prior beliefs, logic alone does not determine what to retract; the system needs priorities or a minimal-change policy. The 2026 review by Almeida and Casals traces computational truth-maintenance systems and AGM-style approaches and discusses implementation challenges. [1][2]

**Design implication:** store not only conclusions, but also support, source, conditions, and a revision priority. “New fact received” must not mean “overwrite every conflicting fact.”

### 2. Track dependencies so errors can be retracted downstream

Truth-maintenance systems attach justifications to beliefs. Assumption-based TMS can represent alternative sets of assumptions under which a conclusion holds. When an assumption is withdrawn, conclusions that depended on it can be invalidated or recomputed instead of silently remaining active. [3][4]

**Design implication:** each derived claim needs a dependency trail. If “cat sleeps on bed” depended on “room is cold” and “cat prefers warm places,” retracting the first premise should invalidate that particular derivation, not erase unrelated observations about the cat.

### 3. Diagnosis needs competing explanations, not one fluent story

Model-based diagnosis asks which parts or assumptions of a model could explain an observed failure. Bayesian variants combine structural explanations with uncertainty. Research on abductive inference also warns that whether hypotheses compete or can coexist affects which explanation is selected. [5][6]

**Design implication:** preserve several candidate explanations when evidence is insufficient. Do not force mutually exclusive causes unless the domain justifies that assumption; multiple causes may operate together.

### 4. Distinguish self-critique from evidence-based correction

A 2026 study of intrinsic LLM self-correction reports that a second pass can fix mistakes but can also turn initially correct answers into wrong ones; aggregate accuracy can hide these transitions. The study therefore evaluates both recovered errors and introduced errors. [7] A 2026 ACL paper on self-auditing argues that internally coherent reasoning can still violate evidence or logical constraints and proposes checking belief states before action commitment. [8]

**Design implication:** “think again” is not a truth oracle. Prefer checks against observations, executable tests, trusted sources, formal constraints, or predictions that can be falsified. If no independent test exists, retain uncertainty rather than treating a rewrite as verification.

### 5. A belief state has more than a confidence score

A 2026 survey preprint frames state maintenance in decision agents around three pieces: current belief state, the transition/revision process, and the likelihood or evidence model used to score new observations. It notes that the evidence-likelihood component is often supplied externally rather than learned by the agent. This is a useful taxonomy, not proof that one implementation solves belief revision. [9]

**Design implication:** keep separate fields for:
- claim and scope/context;
- status: observed, inferred, assumed, disputed, retracted;
- source and time;
- supporting assumptions/dependencies;
- confidence or priority, with its meaning defined;
- expected observations under each candidate explanation;
- revision history and reason for change.

## Proposed revision loop

1. **Predict:** derive what the current model expects in this context.
2. **Compare:** check a new observation, test result, or trusted source against that prediction.
3. **Localize:** find the smallest set of assumptions and derivations that can account for the mismatch.
4. **Branch:** create multiple candidate explanations; distinguish alternatives from causes that can coexist.
5. **Discriminate:** derive different predictions from the candidates and seek the cheapest informative test.
6. **Revise minimally:** change the least-supported assumptions consistent with the evidence; invalidate downstream conclusions through recorded dependencies.
7. **Retain history:** keep prior claims and why they were superseded; do not silently rewrite history.
8. **Check stability:** test new and held-out cases to detect overfitting, oscillation, and regressions.

This is a proposed synthesis of established ideas, not a single published algorithm.

## Compact experiment proposal

Use small synthetic worlds first, where the correct hidden cause and all observations are known. Compare:
- **A:** overwrite the conflicting fact;
- **B:** rerun the full reasoning chain;
- **C:** dependency-aware truth maintenance;
- **D:** dependency tracking plus competing explanations and discriminating tests.

Include cases with:
- a wrong initial rule;
- one false observation and one reliable observation;
- two causes that coexist;
- mutually exclusive causes where exclusivity is explicitly given;
- an exception that later disappears;
- a changed environment/distribution;
- ambiguous evidence where the correct answer is UNKNOWN;
- a correction that should invalidate several downstream conclusions but preserve independent facts.

Measure:
- correction accuracy and false revisions;
- downstream stale conclusions after retraction;
- correct UNKNOWN rate and confidence calibration;
- number/cost of tests and reasoning steps;
- latency, peak RAM, persistent storage;
- oscillation and regressions on previously solved cases.

A key score is **revision precision**: among beliefs changed or retracted, how many actually needed changing? Also track **revision recall**: among beliefs that should change, how many did change. Report them separately; a system can look stable simply by refusing to revise anything.

## What this does—and does not—suggest

- **Established:** belief revision, truth-maintenance systems, model-based diagnosis, abductive reasoning, and evidence-based testing are established research areas.
- **Reported result:** the cited self-correction study documents both recoveries and regressions in tested LLM settings; do not generalize its exact numbers to all models or tasks.
- **Hypothesis:** dependency-aware belief revision with counterfactual/discriminating tests could be a useful layer in a compact reasoning system.
- **Not equivalent:** a knowledge graph stores relations; a truth-maintenance system records why beliefs currently hold; a causal model predicts effects under interventions; a confidence score alone does not perform any of these jobs.
- **Open issue:** natural-language input must still be mapped reliably to claims, contexts, sources, and causal assumptions. A perfect revision layer cannot repair a claim that was parsed incorrectly without noticing.

## Sources

1. Almeida & Casals, *From Doyle to AGM: A Survey and an Implementation Roadmap for Belief Change* (2026). https://journals.sagepub.com/doi/10.1177/30504554261426170
2. Stanford Encyclopedia of Philosophy, *Logic of Belief Revision*. https://plato.stanford.edu/archives/sum2025/entries/logic-belief-revision/
3. Reiter & de Kleer, *Foundations of assumption-based truth maintenance systems* (1987). https://explore.metascienceobservatory.org/papers/W145128706
4. *Reason Maintenance Systems: Tools for Foundations-Based Belief Revision*. https://onlinelibrary.wiley.com/doi/abs/10.1002/9780470050118.ecse440
5. Lucas, *Bayesian model-based diagnosis* (2001). https://doi.org/10.1016/S0888-613X(01)00036-6
6. Glass, *Competing hypotheses and abductive inference* (2021). https://link.springer.com/article/10.1007/s10472-019-09630-0
7. Zhang, *When Should LLMs Trust Their Own Revisions? A Risk-Aware Study of Intrinsic Self-Correction* (2026 preprint). https://arxiv.org/abs/2609.35832
8. Yuan et al., *Verify Before You Commit: Towards Faithful Reasoning in LLM Agents via Self-Auditing* (ACL 2026). https://aclanthology.org/2026.acl-long.1440/
9. Huang et al., *From Memory to Belief: A Survey of State Maintenance and Belief Revision in LLM Decision Agents* (2026 preprint). https://papers.ssrn.com/sol3/Delivery.cfm/7493158.pdf?abstractid=7493158&mirid=1&type=2
