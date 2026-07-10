---
title: "Conversational Preference Elicitation Simulation Framework"
output:
  html_document:
    toc: true
    toc_float: true
  pdf_document:
    toc: true
    toc_depth: 2
---


## Requirements Specification (Framework Draft)

**Version:** 0.2\
**Status:** Draft

------------------------------------------------------------------------

# 1. Introduction

This document defines the overall architecture and expected behavior of
a simulation framework for developing, evaluating, and optimizing
adaptive conversational interview policies for preference elicitation.

The framework is intended as a research platform rather than a
production clinical system. It enables conversational elicitation
methods to be developed under controlled experimental conditions using
synthetic participants whose latent characteristics are known.

This document describes the overall framework and system architecture.
Detailed requirements for the major components will be developed in
subsequent sections.

------------------------------------------------------------------------

# 2. Purpose

The primary objective is to provide a simulation environment in which
conversational interview policies can be objectively compared and
iteratively improved.

The framework supports research on recovering latent human
characteristics---including preferences, values, beliefs, and
decision-making styles---from natural language conversations.

Patient-centered healthcare decision making is the motivating
application, but the architecture is designed to be domain independent.

------------------------------------------------------------------------

# 3. Conceptual Model

The framework treats an interview as an adaptive Bayesian experiment.

At each conversational turn:

1.  The interviewer asks a question.
2.  The participant generates a response from an underlying latent
    state.
3.  The inference engine updates its posterior beliefs.
4.  The interview policy selects the next question.
5.  The process repeats until an appropriate stopping criterion is
    reached.

At a higher level, interview policies are developed through repeated
cycles of simulation, evaluation, and refinement.

------------------------------------------------------------------------

# 4. Overall Architecture

The framework consists of four principal components.

1.  Participant Model
2.  Inference Engine
3.  Adaptive Interview Policy
4.  Experiment Orchestrator

The Experiment Orchestrator coordinates interactions among the other
three components, manages simulation sessions, records results, and
computes evaluation metrics.

                     Participant Model
                            |
                            V
                   Participant Response
                            |
                            V
                    Inference Engine
                            |
                      Posterior State
                            |
                            V
              Adaptive Interview Policy
                            |
                      Next Question
                            |
                            -------
                                  |
                                  V
                        Conversation History

    Experiment Orchestrator
        • manages sessions
        • logs conversations
        • stores ground truth
        • computes evaluation metrics

------------------------------------------------------------------------

# 5. Design Principles

The framework should satisfy the following principles.

-   Modular architecture.
-   Explicit representation of latent variables.
-   Bayesian treatment of uncertainty.
-   Separation of participant simulation and inference.
-   Reproducibility.
-   Model-agnostic interfaces.
-   Interchangeable implementations.
-   Quantitative evaluation.
-   Extensibility.

------------------------------------------------------------------------

# 6. Progressive Capability Levels

Each major component will support multiple generations of
implementation.

Typical progression:

-   Version 1 --- Minimal Viable Prototype
-   Version 2 --- Structured Hybrid System
-   Version 3 --- Fully Bayesian / Learning-Based System

The framework should allow individual components to evolve
independently.

------------------------------------------------------------------------

# 7. Component Specifications

## 7.1 Participant Model

### Purpose

The Participant Model simulates realistic human participants for use in conversational preference elicitation experiments. Each participant is defined by an explicit latent state representing preferences, values, beliefs, communication style, and other decision-relevant characteristics. These latent variables constitute the ground truth for the simulation and are never directly observed by the interviewer or inference engine.

The Participant Model generates conversational responses that are consistent with its latent state, allowing interview policies to be evaluated by their ability to recover the hidden characteristics that produced the observed dialogue.

The Participant Model is therefore a **generative model** rather than a collection of scripted personas.

---

### Responsibilities

The Participant Model is responsible for:

- representing each participant's latent characteristics;
- representing observable demographic and contextual information;
- generating realistic conversational responses to interview questions;
- maintaining conversational consistency across multiple turns;
- exhibiting realistic uncertainty, ambiguity, and variability;
- ensuring that latent variables remain hidden from the interviewer.

The Participant Model is **not** responsible for estimating latent variables, selecting interview questions, or making downstream decisions.

---

### Inputs

The Participant Model receives:

- participant specification (latent state);
- conversation history;
- current interview question;
- optional random seed;
- optional response-generation parameters.

---

### Outputs

The Participant Model returns:

- participant response;
- optional internal diagnostic information (available only for debugging);
- updated internal conversational state.

Only the participant response is visible to the interviewer.

---

### Internal Representation

Each participant is represented by a structured specification consisting of several distinct categories of information.

#### Observable Characteristics

Observable characteristics represent information that would normally be available before the interview.

Examples include:

- age
- sex
- occupation
- education
- family structure
- geographic location
- financial circumstances
- clinical information
- available treatment options

These characteristics are not latent variables and may be available to both participant and interviewer.

---

#### Latent Psychological Variables

Latent variables represent internal characteristics that influence responses but are never explicitly revealed.

Examples include:

- preference for longevity
- quality-of-life preference
- independence
- family orientation
- financial security priority
- trust in clinicians
- risk tolerance
- ambiguity aversion
- treatment burden aversion
- travel burden aversion
- preference for aggressive treatment

The framework does not prescribe a fixed set of latent variables. Different application domains may define different latent models.

Latent variables should generally be represented on normalized continuous scales to facilitate inference and comparison across simulations.

---

#### Communication Characteristics

Communication characteristics describe *how* the participant communicates rather than *what* they value.

Examples include:

- verbosity
- health literacy
- numeracy
- emotional expressiveness
- directness
- willingness to disclose personal information
- preference for concrete versus abstract language

These characteristics influence conversational style but should not be interpreted as preferences.

---

#### Response Behavior

Response behavior controls realistic conversational variability.

Examples include:

- consistency across repeated questions
- susceptibility to framing effects
- probability of misunderstanding
- conversational fatigue
- tendency to elaborate
- willingness to admit uncertainty
- likelihood of changing one's mind after reflection

These parameters allow different participant models to exhibit realistic human behavior rather than perfectly rational consistency.

---

### Separation of Ground Truth and Behavior

The Participant Model distinguishes between the participant's latent state and the mechanism used to express that state.

The latent state represents the participant's true internal characteristics.

The response-generation mechanism determines how those characteristics are translated into natural language during the interview.

This separation allows the same latent participant to be simulated using different response-generation models.

---

### Response Generation

Given a conversational history and a new interview question, the Participant Model generates a natural-language response that is:

- consistent with the participant's latent state;
- consistent with previous responses;
- appropriate to the current conversational context;
- plausible for an ordinary human participant.

The participant should not explicitly reveal latent variables or summarize their own internal preference model unless such insight would naturally emerge during conversation.

Responses should exhibit realistic conversational behavior, including:

- incomplete answers;
- clarification requests;
- uncertainty;
- hesitation;
- emotionally driven responses;
- occasional inconsistency;
- changing emphasis as the conversation progresses.

---

### Version 1 (Minimum Viable Product)

Version 1 uses a structured YAML participant specification together with a large language model.

The LLM receives:

- complete participant specification;
- conversation history;
- interviewer question;
- response-generation instructions.

The LLM generates a participant response while treating the participant specification as private internal state.

The participant specification remains inaccessible to the interviewer and inference engine.

---

### Version 2 (Structured Behavioral Model)

Version 2 introduces an explicit response policy that mediates between the latent state and the LLM.

Rather than prompting the LLM directly from the latent variables, the response policy determines:

- which latent characteristics influence the current response;
- which concerns are likely to be expressed;
- how much information is voluntarily disclosed;
- how uncertainty and emotion affect the response.

The LLM becomes responsible primarily for generating natural language.

---

### Version 3 (Probabilistic Participant Model)

Version 3 replaces manually specified behavioral rules with an explicit probabilistic generative model.

Responses are generated from probabilistic relationships among:

- latent psychological variables;
- contextual factors;
- conversational history;
- emotional state;
- cognitive state.

The participant model may incorporate Bayesian networks, probabilistic programs, reinforcement learning, or other generative modeling techniques.

This version supports systematic investigation of alternative theories of human decision making.

---

### Interfaces

The Participant Model should expose a single abstract interface.

```python
generate_response(
    participant_state,
    conversation_history,
    interviewer_question,
    response_parameters
) -> ParticipantResponse
```

The implementation may use local LLMs, cloud LLMs, symbolic systems, probabilistic programs, or future conversational architectures.

The remainder of the framework should not depend on the implementation.

---

### Evaluation Criteria

A Participant Model should be evaluated according to:

- internal consistency;
- conversational realism;
- adherence to latent state;
- robustness across repeated simulations;
- appropriate conversational variability;
- controllability through latent variables;
- compatibility with multiple interview policies.

The purpose of the Participant Model is not to produce the most human-like conversation possible, but to produce **controlled, reproducible, and scientifically useful simulations** that enable objective evaluation of conversational elicitation methods.

---

### Open Research Questions

The Participant Model raises several research questions that future work may address:

- Which latent variables best characterize decision-relevant human preferences?
- How should dependencies among latent variables be represented?
- How much conversational variability should be introduced?
- How should preference construction during conversation be modeled?
- How should memory, learning, and changing preferences be represented across long interviews?
- How should synthetic participants be validated against real human conversations?

---

I think one philosophical point is worth emphasizing throughout this section: **the participant is not the LLM**. The participant is the *latent state*. The LLM is simply one implementation of the response-generation mechanism. Keeping those concepts separate will make the architecture much cleaner and will allow you to replace the LLM later without changing the scientific model. That separation is likely to become one of the defining design principles of the entire framework.


------------------------------------------------------------------------

I think the Inference Engine is the intellectual heart of the framework. Unlike the Participant Model, which is essentially a simulator, the Inference Engine embodies the scientific problem: **recovering latent human characteristics from conversation**. I would therefore specify it as a Bayesian inference component rather than simply an LLM prompt.

---

## 7.2 Inference Engine

### Purpose

The Inference Engine estimates a participant's latent psychological characteristics from an observed conversation. It maintains a probabilistic representation of uncertainty throughout the interview and provides the Adaptive Interview Policy with the information required to select subsequent conversational interventions.

Unlike the Participant Model, which generates responses from a known latent state, the Inference Engine attempts to recover that latent state using only information available to a real interviewer.

The Inference Engine therefore performs **Bayesian state estimation** over the course of the interview.

---

### Responsibilities

The Inference Engine is responsible for:

- estimating posterior beliefs about latent participant characteristics;
- maintaining uncertainty throughout the interview;
- integrating evidence across multiple conversational turns;
- identifying inconsistencies or conflicting evidence;
- explaining the evidence supporting posterior estimates;
- providing the Adaptive Interview Policy with the current posterior state.

The Inference Engine is **not** responsible for selecting interview questions or generating participant responses.

---

### Inputs

The Inference Engine receives:

- complete conversation transcript;
- participant metadata available to the interviewer;
- current interview turn;
- optional domain-specific latent variable definitions;
- optional prior distributions.

The Inference Engine **never** receives the participant's true latent state.

---

### Outputs

The Inference Engine produces a posterior representation consisting of:

- posterior estimates for each latent variable;
- uncertainty associated with each estimate;
- evidence supporting each estimate;
- identified inconsistencies or ambiguities;
- optional recommendations regarding unresolved uncertainty.

The posterior becomes the principal input to the Adaptive Interview Policy.

---

### Internal Representation

The posterior should represent both estimated values and remaining uncertainty.

For each latent variable, the posterior should include:

```yaml
posterior:

  family_orientation:
      mean: 0.91
      sd: 0.08

  risk_tolerance:
      mean: 0.47
      sd: 0.31

  trust_in_clinicians:
      mean: 0.72
      sd: 0.18
```

The framework does not require a particular probabilistic representation. Future implementations may use:

- Gaussian approximations;
- Bayesian networks;
- Monte Carlo samples;
- particle filters;
- probabilistic programming;
- learned posterior approximations.

---

### Evidence Integration

The Inference Engine should treat every participant response as noisy evidence rather than as a direct measurement of latent preferences.

For each conversational turn, it should:

1. interpret the participant's response;
2. identify relevant evidence;
3. estimate how strongly that evidence supports or contradicts each latent variable;
4. update posterior beliefs.

Evidence from multiple conversational turns should accumulate throughout the interview.

The engine should also recognize when new evidence contradicts previous observations and revise its posterior accordingly.

---

### Explainability

Posterior estimates should be accompanied by supporting evidence.

For example:

```yaml
family_orientation:

    estimate: 0.93

    confidence: high

    evidence:

      - "My children depend on me."

      - "I have to keep working."

risk_tolerance:

    estimate: 0.42

    confidence: low

    evidence:

      - "Mixed statements about treatment risks."
```

This allows interview policies, researchers, and clinicians to understand how conclusions were reached.

---

### Uncertainty Representation

Maintaining uncertainty is a central responsibility of the Inference Engine.

Rather than producing point estimates, the engine should explicitly represent uncertainty throughout the interview.

High uncertainty indicates opportunities for further questioning.

Low uncertainty indicates that additional questioning is unlikely to improve inference.

Posterior uncertainty therefore guides the Adaptive Interview Policy.

---

### Version 1 (Minimum Viable Product)

Version 1 uses an LLM to estimate latent variables directly from the conversation transcript.

The LLM receives:

- transcript;
- latent variable definitions;
- estimation instructions.

It returns structured estimates for each latent variable together with qualitative confidence and supporting evidence.

This version serves primarily as a proof of concept.

---

### Version 2 (Hybrid Bayesian Inference)

Version 2 separates semantic interpretation from probabilistic inference.

The LLM first extracts structured observations from the conversation.

Example:

```yaml
observations:

- topic: family

  polarity: positive

  strength: strong

- topic: travel

  burden: high

- topic: uncertainty

  level: moderate
```

These observations become inputs to an explicit Bayesian inference model that computes posterior beliefs over the latent variables.

This architecture separates language understanding from probabilistic reasoning.

---

### Version 3 (Probabilistic Programming)

Version 3 represents the latent participant model explicitly using probabilistic programming.

The Inference Engine computes posterior distributions using formal probabilistic inference rather than heuristic estimation.

Possible implementations include:

- Stan
- PyMC
- Turing
- Bean Machine
- other probabilistic programming frameworks

The LLM is responsible only for translating conversation into structured observations.

The probabilistic model performs all Bayesian updating.

---

### Latent Variable Dependencies

Future inference engines should exploit dependencies among latent variables.

For example:

```text
Family Identity
        │
        ▼
Preference for Remaining Employed
        │
        ▼
Treatment Preferences
```

Observations about treatment choices may therefore provide indirect evidence about multiple latent variables simultaneously.

Representing these dependencies should improve inference accuracy and reduce interview length.

---

### Interface

The Inference Engine should expose a single abstract interface.

```python
update_posterior(
    transcript,
    prior_posterior,
    latent_model
) -> PosteriorState
```

The implementation should be independent of the conversational model used by the Participant Model.

---

### Evaluation Criteria

Inference engines should be evaluated using:

- latent-state recovery accuracy;
- posterior calibration;
- robustness to ambiguous language;
- robustness to inconsistent responses;
- computational efficiency;
- reproducibility;
- explainability;
- compatibility with multiple participant models.

Because the participant's true latent state is known during simulation, recovery performance can be measured objectively.

---

### Open Research Questions

Important research questions include:

- Which latent variables can be reliably recovered from conversation?
- How should uncertainty be represented?
- What probabilistic models best capture dependencies among latent variables?
- How should contradictory conversational evidence be integrated?
- How much prior information should be incorporated before the interview begins?
- How should explanations of posterior estimates be generated?
- Can inference accuracy be improved by jointly modeling language and decision making?

---

### Relationship to the Adaptive Interview Policy

The Inference Engine and Adaptive Interview Policy form a closed Bayesian estimation loop.

The Inference Engine estimates the participant's latent state from the observed conversation.

The Adaptive Interview Policy uses the resulting posterior to determine which conversational intervention is expected to provide the greatest additional information.

This separation allows different inference algorithms and interview policies to be evaluated independently while using the same participant simulations.

---

I think there's one addition that could become a hallmark of the framework: **decision-focused inference**. Most inference engines estimate latent variables because they're interesting in themselves. In your setting, the ultimate purpose is to support a healthcare decision. Eventually, the Inference Engine should not only estimate \(P(\theta \mid \text{conversation})\), but also estimate how uncertainty in \(\theta\) affects the downstream decision. That would allow the interview policy to choose questions based not merely on information gain, but on **expected value of information (VOI)**—asking the questions that are expected to improve the decision the most, not simply reduce uncertainty the most. That would connect the framework directly to Bayesian decision analysis and distinguish it from generic conversational AI systems.


------------------------------------------------------------------------

I think this is the section where the framework becomes unique. The Participant Model and Inference Engine are necessary components, but the **Adaptive Interview Policy** is the object of optimization. It is the "algorithm" that your framework is trying to improve. I would therefore write this section in terms of **decision making under uncertainty**, rather than simply "question generation."

---

## 7.3 Adaptive Interview Policy

### Purpose

The Adaptive Interview Policy determines the next conversational action during an interview. Its objective is to reduce uncertainty about the participant's latent characteristics as efficiently as possible while minimizing interview burden and maintaining a natural, supportive conversation.

The policy treats the interview as a sequential Bayesian experiment. At each conversational turn, it selects the intervention expected to provide the greatest value for improving inference and, ultimately, downstream decision making.

Unlike traditional questionnaires, which present a fixed sequence of questions, an adaptive interview policy continuously adjusts its behavior based on information acquired during the conversation.

---

### Responsibilities

The Adaptive Interview Policy is responsible for:

- selecting the next conversational action;
- determining which latent variables should be explored next;
- balancing information gain against interview burden;
- avoiding unnecessary or redundant questions;
- selecting appropriate conversational strategies;
- determining when sufficient information has been obtained to end the interview.

The Adaptive Interview Policy is **not** responsible for estimating latent variables or generating participant responses.

---

### Inputs

The Adaptive Interview Policy receives:

- current posterior distribution from the Inference Engine;
- complete conversation history;
- participant metadata available to the interviewer;
- interview objectives;
- practical constraints (e.g., interview length, literacy level, conversational style).

The policy does **not** receive the participant's true latent state.

---

### Outputs

At each conversational turn, the policy produces a structured conversational action.

Examples include:

- ask a new question;
- ask a clarification question;
- summarize and confirm understanding;
- explore an inconsistency;
- provide factual information (when appropriate);
- terminate the interview.

The policy output should include both the conversational action and the rationale for selecting it.

---

### Policy State

The interview policy maintains its own internal state throughout the interview.

Typical state variables include:

- posterior uncertainty;
- topics already discussed;
- variables already explored;
- participant burden estimate;
- participant engagement estimate;
- detected misunderstandings;
- unresolved ambiguities.

This state allows the policy to avoid repetition and adapt its strategy over time.

---

### Candidate Question Selection

The policy should separate **what** it wishes to learn from **how** it asks.

At each conversational turn, the policy first identifies one or more latent variables whose uncertainty remains both high and decision-relevant.

It then selects an appropriate conversational strategy for obtaining additional information.

Possible strategies include:

- open-ended questions;
- value clarification;
- hypothetical trade-offs;
- pairwise comparisons;
- ranking exercises;
- clarification questions;
- reflective summaries;
- consistency checks;
- counterfactual scenarios.

Natural-language wording is generated only after the conversational objective has been selected.

---

### Information-Guided Question Selection

The primary objective of the policy is to maximize the usefulness of each conversational intervention.

Selection criteria may include:

- expected reduction in posterior uncertainty;
- expected improvement in downstream decision quality;
- participant burden;
- conversational redundancy;
- emotional sensitivity;
- susceptibility to framing effects.

The framework does not prescribe a single optimization criterion. Different applications may adopt different objective functions.

---

### Conversational Constraints

The policy should maintain a natural conversational flow.

It should:

- avoid repeatedly asking similar questions;
- avoid unnecessarily leading questions;
- adapt vocabulary to participant literacy;
- respond appropriately to participant uncertainty;
- recognize emotional distress;
- permit participants to elaborate naturally;
- maintain rapport throughout the interview.

The interview should resemble an authentic clinical conversation rather than an adaptive questionnaire.

---

### Stopping Criteria

The policy is responsible for determining when additional questioning is unlikely to improve inference sufficiently to justify the additional burden.

Possible stopping criteria include:

- posterior uncertainty below predefined thresholds;
- expected information gain below threshold;
- expected value of information below threshold;
- participant burden exceeding acceptable limits;
- maximum interview length reached;
- downstream decision becoming insensitive to remaining uncertainty.

Stopping rules should be configurable.

---

### Version 1 (Minimum Viable Product)

Version 1 implements the interview policy primarily through structured LLM prompting.

The policy receives:

- transcript;
- posterior summary;
- interview objectives.

It generates a single conversational action together with one natural-language question.

Selection is guided by qualitative descriptions of remaining uncertainty rather than explicit optimization.

This version serves as a proof of concept.

---

### Version 2 (Hybrid Interview Policy)

Version 2 separates decision making from language generation.

A deterministic policy first identifies:

- target latent variable(s);
- conversational objective;
- question type;
- stopping decision.

An LLM is then responsible only for producing natural-language wording appropriate to the current conversational context.

This architecture improves reproducibility while retaining conversational flexibility.

---

### Version 3 (Bayesian Experimental Design)

Version 3 formulates interview policy as an explicit Bayesian experimental design problem.

For each candidate conversational action, the policy estimates:

- possible participant responses;
- resulting posterior distributions;
- expected reduction in uncertainty;
- expected value of information;
- expected interview cost.

The selected action maximizes an application-specific utility function balancing information gain against participant burden and other practical considerations.

This version provides a principled framework for optimizing conversational strategies.

---

### Question Library

Future implementations may maintain a structured library of conversational templates.

Templates should be indexed by:

- latent variable(s) addressed;
- conversational strategy;
- reading level;
- emotional sensitivity;
- clinical context.

The policy selects an appropriate template and then adapts its wording to the current conversation.

Separating question selection from language generation allows competing interview policies to be compared independently of linguistic style.

---

### Interface

The Adaptive Interview Policy should expose a single abstract interface.

```python
select_next_action(
    posterior_state,
    conversation_history,
    interview_constraints,
    policy_state
) -> InterviewAction
```

The implementation may use LLM prompting, symbolic reasoning, reinforcement learning, Bayesian optimization, or future conversational architectures.

The remainder of the framework should remain independent of the implementation.

---

### Evaluation Criteria

Interview policies should be evaluated using:

- latent-state recovery accuracy;
- interview efficiency;
- expected value of information;
- participant burden;
- robustness to framing effects;
- robustness across participant populations;
- conversational naturalness;
- computational efficiency;
- downstream decision quality.

Because the true participant state is known in simulation, competing interview policies can be compared objectively.

---

### Open Research Questions

Important research questions include:

- Which conversational strategies are most informative for different latent variables?
- How should interview burden be quantified?
- How should conversational naturalness be balanced against information gain?
- How should uncertainty determine stopping decisions?
- Can interview policies be learned automatically through reinforcement learning or Bayesian optimization?
- How should expected value of information be estimated for conversational interventions?
- How should interview policies generalize across domains?

---

### Relationship to the Inference Engine

The Adaptive Interview Policy and the Inference Engine operate as complementary components within a sequential Bayesian decision process.

The Inference Engine estimates the participant's latent state from the observed conversation.

The Adaptive Interview Policy uses the resulting posterior distribution to determine which conversational action is expected to produce the greatest improvement in inference or downstream decision quality.

This separation allows inference algorithms and interview policies to be developed, evaluated, and optimized independently while using a common participant simulation framework.

---

#### Commentary

I think one philosophical point should guide this entire section:

> **The interview policy should not ask the most informative question—it should ask the most valuable question.**

Those are not always the same.

Suppose we are uncertain about ten latent variables, but only two could change the treatment recommendation. A purely information-theoretic policy would ask about whichever variable has the highest entropy. A decision-theoretic policy would ask about whichever variable has the greatest **Expected Value of Information (EVOI)** for the clinical decision. I think that distinction is what connects your framework most directly to Bayesian decision analysis and makes it more than just an adaptive interviewing system. It's also a natural progression: Version 1 asks sensible questions, Version 2 asks strategically targeted questions, and Version 3 asks the questions that are expected to make the biggest difference to the decision.

------------------------------------------------------------------------

## 7.4 Experiment Orchestrator

### Purpose

The **Experiment Orchestrator** is what transforms the three core components into a scientific research platform. It coordinates interactions among the Participant Model, Inference Engine, and Adaptive Interview Policy during simulated interviews. It manages experimental execution, maintains conversation history, records ground truth, computes evaluation metrics, and ensures reproducibility.

Unlike the other components, the Experiment Orchestrator does not participate directly in the conversation. Its role is to execute controlled experiments in which competing participant models, inference engines, and interview policies can be evaluated under identical conditions.

The Experiment Orchestrator therefore serves as the experimental framework that enables systematic comparison and iterative improvement of conversational elicitation methods.

---

### Responsibilities

The Experiment Orchestrator is responsible for:

- initializing simulation sessions;
- loading participant specifications;
- coordinating interactions among framework components;
- maintaining complete conversation histories;
- recording hidden ground truth;
- enforcing information separation between components;
- managing random seeds and reproducibility;
- executing experimental batches;
- computing evaluation metrics;
- storing experimental results.

The Experiment Orchestrator is **not** responsible for participant simulation, Bayesian inference, or interview policy decisions.

---

### Information Isolation

One of the primary responsibilities of the Experiment Orchestrator is maintaining strict separation between information available to different components.

Specifically:

- the Participant Model receives the participant's complete latent state;
- the Inference Engine receives only observable conversation and participant metadata;
- the Adaptive Interview Policy receives only the current posterior estimates and observable conversation;
- evaluation modules receive both the inferred latent state and the ground truth after the interview has concluded.

This separation prevents accidental leakage of hidden information and ensures that simulated interviews faithfully represent real-world conditions.

---

### Experiment Lifecycle

Each experiment proceeds through the following stages.

#### 1. Participant Initialization

The Experiment Orchestrator loads a participant specification and initializes the Participant Model.

Ground-truth latent variables are retained internally and remain inaccessible to the interviewer.

---

#### 2. Interview Initialization

The orchestrator initializes:

- conversation history;
- inference engine;
- interview policy;
- random seeds;
- stopping criteria.

---

#### 3. Conversational Loop

The orchestrator repeatedly executes:

1. Interview Policy selects the next conversational action.
2. Participant Model generates a response.
3. Conversation history is updated.
4. Inference Engine updates posterior beliefs.
5. Stopping criteria are evaluated.

This process continues until the interview terminates.

---

#### 4. Evaluation

Following interview completion, the orchestrator compares inferred latent variables with the participant's true latent state.

Performance metrics are computed and stored.

---

#### 5. Reporting

The orchestrator records:

- conversation transcript;
- posterior trajectory;
- interview actions;
- stopping reason;
- evaluation metrics;
- experimental metadata.

---

### Session Management

Each interview session should maintain complete experimental provenance.

Typical session metadata includes:

```yaml
session:

  session_id: ...

  participant_id: ...

  participant_version: ...

  interview_policy: ...

  inference_engine: ...

  participant_model: ...

  random_seed: ...

  timestamp: ...

  software_versions: ...
```

This information enables complete reproducibility of simulation experiments.

---

### Experiment Configuration

Experiments should be defined using declarative configuration files rather than custom software.

Typical configuration options include:

- participant population;
- interview policy;
- inference engine;
- participant model;
- stopping criteria;
- evaluation metrics;
- number of replications;
- random seed;
- output location.

This allows experiments to be repeated without modifying program code.

---

### Batch Experiments

The Experiment Orchestrator should support automated execution of large experimental batches.

Examples include:

- evaluating one interview policy across thousands of participants;
- comparing multiple inference engines on the same conversations;
- evaluating robustness across different participant populations;
- sensitivity analyses over latent variable distributions;
- parameter sweeps for interview policies.

Batch execution is essential for statistically meaningful comparison of competing methods.

---

### Evaluation Pipeline

Following each interview, the orchestrator computes standardized performance metrics.

Typical metrics include:

- latent-state recovery error;
- posterior calibration;
- interview length;
- participant burden;
- computational cost;
- conversational naturalness;
- downstream decision quality;
- expected value of information;
- stopping efficiency.

Metric definitions should be standardized across experiments to facilitate comparison among competing implementations.

---

### Logging

The Experiment Orchestrator should record complete experimental logs.

Logs should include:

- participant metadata;
- conversation transcript;
- posterior estimates after each conversational turn;
- interview actions;
- timing information;
- warnings or errors;
- evaluation results.

Ground-truth latent variables should be stored separately from observable conversation logs to preserve information isolation.

---

### Version 1 (Minimum Viable Product)

Version 1 manages individual interviews.

Capabilities include:

- loading participant YAML files;
- executing one interview;
- recording transcripts;
- storing participant ground truth;
- computing basic recovery accuracy;
- exporting results.

This version provides the minimum functionality required for interactive experimentation.

---

### Version 2 (Research Platform)

Version 2 introduces systematic experimentation.

Additional capabilities include:

- batch execution;
- multiple participant populations;
- multiple interview policies;
- multiple inference engines;
- standardized evaluation metrics;
- automated report generation;
- experiment configuration files.

This version supports comparative research studies.

---

### Version 3 (Optimization Framework)

Version 3 transforms the orchestrator into a closed optimization environment.

Additional capabilities include:

- automated policy optimization;
- reinforcement learning workflows;
- Bayesian optimization;
- adaptive participant generation;
- hyperparameter optimization;
- distributed experiment execution;
- continuous benchmarking.

The orchestrator becomes responsible for managing iterative cycles of simulation, evaluation, policy improvement, and validation.

---

### Interfaces

The Experiment Orchestrator should expose a single high-level interface.

```python
run_experiment(
    participant_model,
    inference_engine,
    interview_policy,
    experiment_configuration
) -> ExperimentResult
```

Internally, the orchestrator manages all interactions among framework components.

Component implementations should remain interchangeable.

---

### Evaluation Criteria

The Experiment Orchestrator should be evaluated according to:

- reproducibility;
- scalability;
- correctness of component isolation;
- completeness of experimental logging;
- computational efficiency;
- support for interchangeable components;
- support for large-scale benchmarking.

Its primary purpose is not conversational intelligence but scientific rigor.

---

### Open Research Questions

Important questions for future work include:

- How should benchmark participant populations be constructed?
- Which evaluation metrics best predict real-world interview performance?
- How should stochastic participant behavior be managed across repeated experiments?
- What statistical methods should be used to compare interview policies?
- How should experiments be distributed across heterogeneous computing resources?
- How should simulation studies be integrated with human-subject validation?

---

### Relationship to the Framework

The Experiment Orchestrator provides the infrastructure that transforms the Participant Model, Inference Engine, and Adaptive Interview Policy into a coherent experimental platform.

Its primary contribution is enabling controlled, reproducible, and extensible experimentation. By enforcing modular interfaces, preserving information isolation, and standardizing evaluation, it allows individual components to evolve independently while ensuring that competing approaches can be compared fairly.

---

#### Commentary

One thing I'd add that doesn't usually appear in software specifications is the concept of an **Experiment** as a first-class object. Rather than thinking of the orchestrator as "running interviews," I'd think of it as managing experiments.

For example:

```yaml
experiment:
  participant_population: lung_cancer_v1
  participant_model: llm_participant_v2
  inference_engine: hybrid_bayes_v1
  interview_policy: voi_policy_v3
  repetitions: 1000
  random_seed: 12345
  metrics:
    - recovery_accuracy
    - posterior_calibration
    - decision_regret
```

This mirrors how machine learning frameworks treat training runs or how Bayesian software treats model specifications. It will make the platform feel like a true research environment, where every result is traceable to an explicit experimental configuration. I think that will become one of the defining strengths of the framework.

------------------------------------------------------------------------

# 8. Evaluation Framework

The framework should support objective comparison of interview policies
using metrics such as:

-   recovery accuracy
-   posterior calibration
-   interview length
-   participant burden
-   robustness to framing
-   robustness across participant populations
-   computational cost
-   downstream decision quality

The precise definitions of these metrics will be specified separately.

------------------------------------------------------------------------

# 9. Future Extensions

Potential future capabilities include:

-   probabilistic programming inference engines
-   reinforcement learning interview policies
-   value-of-information question selection
-   multi-agent participant models
-   multimodal conversations
-   longitudinal interviews
-   human-in-the-loop evaluation
-   real participant validation

---

## Future Extension: Explicit Latent Variable Models

The current framework assumes that each participant is characterized by a collection of latent psychological variables (e.g., preferences, values, beliefs, and communication characteristics). Future versions of the framework should make this latent variable model an explicit, interchangeable component rather than an implicit part of the Participant Model.

A **Latent Variable Model** defines the conceptual structure of the participant, including:

- the latent variables to be represented;
- prior distributions over those variables;
- dependencies among latent variables;
- relationships between latent variables and observable behavior;
- relationships between latent variables and downstream decision models.

For example, a healthcare application might define a model in which family identity influences financial priorities, which in turn affect treatment preferences. A different application domain might employ an entirely different latent structure.

Separating the latent variable model from the Participant Model provides several advantages:

- **Scientific flexibility.** Researchers can compare competing psychological theories by evaluating different latent variable models while holding the participant simulation, inference engine, and interview policy constant.
- **Modularity.** Participant models become generic simulators capable of operating with different latent variable definitions.
- **Generality.** The framework can be applied to diverse domains by substituting an appropriate latent variable model without changing the overall system architecture.
- **Improved inference.** Explicit dependencies among latent variables allow inference engines to exploit indirect evidence, improving recovery accuracy and reducing interview length.
- **Decision integration.** Latent variables can be linked explicitly to downstream utility models or decision analyses, allowing interview policies to prioritize questions that are expected to improve decision quality.

Conceptually, the architecture becomes:

```text
Latent Variable Model
        │
        ▼
Participant Model
        │
        ▼
Conversation
        │
        ▼
Inference Engine
        │
        ▼
Posterior Distribution
        │
        ▼
Adaptive Interview Policy
        │
        ▼
Next Conversational Action
```

This extension broadens the scope of the framework from evaluating conversational interview strategies to evaluating complete computational models of human decision making. Under this architecture, researchers can independently compare:

1. **Latent Variable Models** that represent alternative theories of human preferences and behavior.
2. **Inference Engines** that recover latent variables from conversational evidence.
3. **Adaptive Interview Policies** that determine how information should be gathered.

Together, these components establish a general experimental platform for developing, comparing, and validating computational models of conversational inference across multiple application domains.

------------------------------------------------------------------------

# 10. Roadmap

This framework document serves as the architectural specification.

Subsequent documents will provide detailed requirements for:

1.  Participant Model
2.  Inference Engine
3.  Adaptive Interview Policy
4.  Experiment Orchestrator
5.  Evaluation Metrics
6.  Reference Data Formats
7.  Milestones and implementation prompts
