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

Some quick tricks that were tested on the go, to see if the model could be patched by using prompt engineering:
- Provide a simulated chat interface by appending "User:" to the user prompt, and "Assistant:" or "Aristotle:" at the end of it, in order to attempt for he model generation to provide us some relevant content.
- Test with other similar prompts trying to engage in a conversation with the model.
- Load the model with a higher precission (they were initially tested with 4bit quantization).
  
It is remarkable to mention that those models were trained using the qlora approach; and the generated LoRAs are added at the end of the model; as it is the case for adapters in the LLM ecosystem.

As we saw that these approaches did not solve the problem, the next attempt was to try to fine tune it and fix the lack of conversational capabilities:
- Another LoRA was trained on casual conversations dataset. Then both LoRAs were loaded and prompted with the attempt to have both small talks with the model and more deep conversations regarding the model's knowledge.
- More than two LoRAs were added: another was trained on GPT3/4 conversations (not a big dataset), and the three LoRAs were put together... still a long way to go.

It was not until a fine-tune of a LoRA with a merge of the datasets was performed: the contents of the philosopher writings and casual conversations were put together in the same dataset. The models were trained for 20, 40 and 100 epochs; to have an estimate on how much more training would ruin more the model or make it more performant for the task.

These results were the best of all: the model was capable of keeping casual conversations, as well as answering to the user's prompts about mathematics and philosophy; the hallucinations rate was not double checked; and we merely found the generations engaging for more conversation or not.

Somehow that approach left us with an issue that has appeared in other occassions in the state of the art, and trying to solve it brought more problems of the trade: the model when asked "hello", started generating more than what it should: adding the next common steps for the conversation, that would need to be provided by the user; in other words, we were uncapable of making the model to stop generating tokens when it should, and simply continued.

That is quite common in the early stages of training an LLM, and is usually due to the fact that the BOS (begin of sequence) and EOS (end of sequence) are not added properly in the training dataset. The next logical experiment was to add them properly to the dataset, and check if there was an improvement in the generation or not.

We clearly were too optimistic on the fact that adding such tokens would leave us with a great model, somehow shit happens... And we ended up with a model that barely repeated what we thought was the EOS token. The model had learned that generating such tokens would lead to a high decrease in the loss of the model... the model was overfitting.

There were a couple of facts that need to take further consideration to study:
- The fine-tuned model is https://huggingface.co/NousResearch/Nous-Hermes-llama-2-7b, which seems to have a BOS of <s> and EOS of </s> from the special_tokens file; somehow we find that the prompt engineering guide uses another format to indicate instructions and how to resolve them. If such difference in the prompt ends up being the thing that finally solves the "Hello prompt problem"; that would go hand in hand with our ideas of how "LLMs are CPUs" (https://github.com/DaertML/llms_are_trained_cpus), and how different prompt formats are kind of a Virtual Machine in today's computers.
- Using generation hyperparameters in HuggingFace like length_penaly=1.3 and repetition_penalty=3.0 (these have been the only values we tested with... as we are documenting this on the go); provided interesting results: adding some rubbish tokens in the middle of the generation, but usually do not repeating he end token continuously.

One could use classic deep learning mechanisms to avoid the overfitting issue:
- Reduce the Learning Rate: in order to reduce the importance of the newly provided sequences of tokens.
- Increase the dropout ratio in the model, to avoid the convergence to such sequences.

Even though we have some new insights on the dynamics of the issue and how to fix them; we feel like we just scratched the surface of possibilities and more work in the topic will bring us closer to a well performing model that can chat with the user casually and provide deeper conversations in its knowledge field.
