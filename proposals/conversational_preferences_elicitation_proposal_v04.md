---
output:
  pdf_document: default
  html_document: default
---
# A Simulation-Based Framework for Developing Adaptive Conversational Interview Policies

## Overview

Many important human characteristics—including preferences, values, and decision-making styles—cannot be measured directly but must be inferred through conversation. Designing effective conversational elicitation strategies is therefore a fundamental challenge in healthcare and many other fields. Yet there is currently no rigorous methodology for evaluating competing interview strategies because, with real participants, the true latent state is fundamentally unknowable.

This project proposes a simulation-based framework that treats conversational elicitation as an adaptive Bayesian experiment. By constructing synthetic participants with known latent psychological states, we create a controlled environment in which conversational interview policies can be objectively evaluated, compared, and optimized. Patient-centered healthcare decision making serves as the motivating application, but the framework is intended as a general methodology for developing adaptive conversational inference systems.

## Research Framework

Each synthetic participant is defined by a latent psychological model representing characteristics relevant to the application domain. These latent variables remain hidden from the interviewer but are used by a conversational AI to generate realistic responses that are consistent with the participant's underlying characteristics.

The interviewer model is guided by an adaptive experimental policy. After each response, it updates its posterior beliefs about the participant's latent state and selects the next question expected to maximize information gain. The interview policy may be implemented using LLM prompts, symbolic decision procedures, reinforcement learning, or other conversational architectures. Our primary interest is the interview policy itself rather than its implementation.

The framework consists of three interacting components:

1. **Participant model:** generates realistic conversational responses from a known latent state.
2. **Inference engine:** estimates the participant's latent state from the conversation.
3. **Adaptive interview policy:** selects each subsequent question to maximize expected information gain while balancing interview length, participant burden, and robustness to framing effects.

During an interview, each participant response is treated as an observation generated from an underlying latent state. After each response, the interviewer updates its posterior beliefs about that state and uses an adaptive interview policy to select the next question expected to maximize information gain. The interview proceeds as a sequential Bayesian experiment until the remaining uncertainty is sufficiently small.

The objective is to recover the participant's latent state as accurately and efficiently as possible.

## Iterative Optimization

The principal contribution of the project is an iterative optimization framework for developing conversational interview policies. Large synthetic populations provide objective benchmarks on which competing policies can be evaluated using measures such as recovery accuracy, posterior calibration, interview length, participant burden, and robustness to ambiguous or inconsistently framed responses.

This creates a closed development cycle:

**Generate synthetic participants → Conduct adaptive interviews → Estimate latent state → Measure performance → Improve interview policy → Repeat**

Because every stage is simulated, large numbers of candidate interview strategies can be evaluated rapidly before validation with human participants.

## Expected Impact

This project establishes a new methodology for designing and optimizing conversational interview policies through simulation. By enabling objective comparison of competing approaches under controlled experimental conditions, it creates a benchmark ecosystem in which conversational elicitation methods can be systematically developed, evaluated, and refined. The framework is broadly applicable wherever latent human characteristics must be inferred through conversation, including psychology, education, consumer research, and human-computer interaction.

