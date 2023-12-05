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
- By doing context distillation.
- By performing RAG and appending any relevant data to te context of the prompt.

Every single mechanism previously listed attempts to achieve the same goal: to activate certain paths and regions in the LLM latent space; in a similar manner Diffuser models behave.

We find it remarkable relevant to align a model prior to deploying it in an end to end environment or production ecosystem. The task of alignment is continuous: we have found OpenAI struggling on certain occasions to bring a useful and aligned model in their GPT4 versions, as well as the original LLaMA2 model, which is highly limited by guardrails, to avoid generation dangerous contents.

As a final note, classical NLP algorithms to do intent classification or sentiment analysis, over the user's prompt and the given answer, could help in avoiding dangerours or wrong generations of the model.

# How to (try to) solve the Hello Prompt problem
One of the prompts that MichaelisTrofficus (unofficially proclaimed the Chief Red Team Officer at DaertML :)) found to be making the LLM to provide wrong answers, was a blank "hello" prompt. Doing so left the model to disclose training data; something that should not happen in a production environment. That seemed like a great parallelism with a "Hello World" problem in any programming language; a safe and unintentionally breaking prompt for the models (https://huggingface.co/DaertML/Aristotle-7B and https://huggingface.co/DaertML/Plato-7B).
