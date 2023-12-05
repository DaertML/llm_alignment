# llm_alignment
Repository for code and discussions on how to align LLMs and other findings

# Introduction
LLMs are one of the latest breakthroughs in the AI ecosystem; while everyone is trying to jump on the train (no pun intended) of LLMs, yet there is a huge lack of understanding te dynamics of how thi models are properly trained, aligned to the user (here we discuss that alignment is not necessary related with censorship of the model, or trying to avoid certain prompts, but more in the line of making a comfortable user experience while keeping certain guardrails; and mainly ensuring that there will be a limited amount of hallucinations).

This repository will mainly focus on discussing some adventures to improve models and make them aligned to a certain use case. Most of the vulnerabilities of prior trained models were discovered by MichaelisTrofficus in an attempt to discover how the trained models at DaertML could be used for the simulation of a conversation between philosophers.

# How models are aligned
There are different ways in which a model can be aligned to follow a user prompt, here are the most common, and most derivative methods can be classified as one of those:
- By using prompt engineering.
- By fine-tuning a model.
- By training a model.
- By using any RLHF or RLAIF mechanism.

