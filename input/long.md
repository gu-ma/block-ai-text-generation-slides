# Table of Contents (TOC)

1. **Introduction & Goals**
2. **Stage 1: Pre-Training (Building a Base Model)**
   - 2.1 Data Collection (Internet Download & Filtering)
   - 2.2 Tokenization
   - 2.3 Neural Network Training (Next-Token Prediction)
   - 2.4 Transformer Architecture
   - 2.5 Inference (How Text is Generated)

3. **Base Models in Practice**
   - 3.1 GPT-2 Example
   - 3.2 Compute & GPUs
   - 3.3 What a Base Model Actually Is

4. **Stage 2: Post-Training (Creating an Assistant)**
   - 4.1 Supervised Fine-Tuning (SFT)
   - 4.2 Conversation Formatting & Special Tokens
   - 4.3 Human Labelers & Instruction Design
   - 4.4 Synthetic Data & Modern Pipelines

5. **Hallucinations & Factuality**
   - 5.1 Why Hallucinations Happen
   - 5.2 Mitigation via “I Don’t Know” Training
   - 5.3 Tool Use (Web Search)

6. **Tools & External Computation**
   - 6.1 Web Search
   - 6.2 Code Interpreter
   - 6.3 Context Window as Working Memory

7. **LLM Psychology & Limitations**
   - 7.1 Models “Need Tokens to Think”
   - 7.2 Reasoning Spread Across Tokens
   - 7.3 Counting & Character-Level Failures
   - 7.4 Identity & Self-Knowledge
   - 7.5 Surprising Cognitive Quirks

8. **Stage 3: Reinforcement Learning**
   - 8.1 Motivation (School Analogy)
   - 8.2 Trial-and-Error Solution Discovery
   - 8.3 Learning from Successful Outputs

# 1. Introduction & Goals

The document provides a **comprehensive but accessible explanation of how large language models (LLMs) like ChatGPT are built and trained**, along with:

- Mental models for understanding what LLMs are
- What they’re good at
- Where they fail
- Why they hallucinate
- How post-training shapes assistant behavior

The pipeline is presented in **three major stages**:

1. Pre-training
2. Post-training (Supervised Fine-Tuning)
3. Reinforcement Learning

# 2. Stage 1: Pre-Training (Building a Base Model)

## 2.1 Data Collection (Internet Download & Filtering)

The process begins by:

- Crawling large portions of the internet (e.g., Common Crawl)
- Filtering:
  - Spam
  - Malware
  - Adult content
  - Racist content
  - Low-quality text

- Extracting only clean text (removing HTML, navigation, CSS)
- Filtering by language
- Removing duplicates
- Removing personally identifiable information (PII)

Result:
Tens of terabytes of filtered text → trillions of tokens.

This becomes the raw material for training.

## 2.2 Tokenization

Text is converted into **tokens**, which are discrete symbols.

Key ideas:

- Neural networks require a 1D sequence of symbols.
- Tokens are not characters.
- GPT-4 uses ~100,000 token vocabulary.
- Byte Pair Encoding (BPE) merges frequent byte pairs into new tokens.
- Tokenization balances:
  - Vocabulary size
  - Sequence length

Example:
“hello world” may be 2 tokens.

Important:
Models do **not see letters** — they see token IDs.

## 2.3 Neural Network Training (Next-Token Prediction)

Core idea:
The model learns to predict the **next token** in a sequence.

Process:

1. Take a window of tokens (context).
2. Feed into neural network.
3. Output probability distribution over 100k+ possible next tokens.
4. Increase probability of correct next token.
5. Repeat billions/trillions of times.

Training = adjusting billions of parameters so predicted distributions match internet statistics.

## 2.4 Transformer Architecture

The neural network used is a **Transformer**.

Characteristics:

- Billions (or hundreds of billions) of parameters
- Attention layers
- Matrix multiplications
- Layer normalization
- Stateless forward pass

Important insight:
Each token prediction uses a **finite amount of computation** (limited layers).

## 2.5 Inference (Text Generation)

Once trained:

- Input prefix tokens
- Model outputs probabilities
- Sample next token
- Append
- Repeat

This is stochastic (probabilistic sampling).

Inference ≠ training.
The model weights are fixed.

# 3. Base Models in Practice

## 3.1 GPT-2 Example

GPT-2:

- 1.6B parameters
- 100B tokens
- 1024 context length

Modern models:

- Hundreds of billions of parameters
- Trained on trillions of tokens
- Much larger context windows

## 3.2 Compute & GPUs

Training requires:

- GPUs (e.g., H100)
- Massive parallel matrix multiplications
- Data centers with thousands of GPUs

Cost:

- GPT-2 in 2019: ~$40k
- Today: much cheaper due to hardware/software improvements

The “gold rush” = acquiring GPUs.

## 3.3 What a Base Model Actually Is

A base model is:

- A token simulator
- A lossy compression of the internet
- A statistical remix engine

It is NOT yet an assistant.

It:

- Autocompletes
- Can memorize
- Can hallucinate
- Is stochastic

# 4. Stage 2: Post-Training (Creating an Assistant)

Base models are not helpful assistants.

Post-training turns them into one.

## 4.1 Supervised Fine-Tuning (SFT)

Replace internet text dataset with:

→ Dataset of human-written conversations

Human labelers:

- Write prompts
- Write ideal assistant responses
- Follow labeling guidelines:
  - Helpful
  - Truthful
  - Harmless

The model is trained to imitate this conversational style.

## 4.2 Conversation Formatting & Special Tokens

Conversations are tokenized with special tokens:

- <start_user>
- <end_user>
- <start_assistant>
- etc.

Everything is still just one-dimensional token sequences.

## 4.3 Human Labelers & Instruction Design

Key insight:

You’re not talking to a magical AI.

You’re talking to a **statistical simulation of a human labeler** following company-written instructions.

The assistant imitates:

- The labeling documentation
- The example conversations

## 4.4 Synthetic Data & Modern Pipelines

Modern pipelines:

- Use LLMs to generate synthetic conversations
- Humans edit / supervise
- Large mixtures (millions of conversations)

# 5. Hallucinations & Factuality

## 5.1 Why Hallucinations Happen

If training data always answers:

“Who is X?” → confident biography

Then at test time:

“Who is RandomName?” → model confidently fabricates.

It imitates statistical style, not truth verification.

## 5.2 Mitigation: Teaching “I Don’t Know”

Procedure:

1. Generate factual questions from known documents.
2. Probe model multiple times.
3. If it fails consistently:
   - Add training example:
     “I’m sorry, I don’t know.”

This wires uncertainty neurons to refusal behavior.

## 5.3 Tool Use (Web Search)

Instead of guessing:

- Model emits special search tokens
- System calls Bing/Google
- Results inserted into context window
- Model answers using retrieved info

Key distinction:

- Parameters = vague memory
- Context window = working memory

# 6. Tools & External Computation

## 6.1 Code Interpreter

Instead of mental arithmetic:

- Model writes Python code
- System executes
- Returns result
- Model responds

More reliable than internal reasoning.

## 6.2 Context Window = Working Memory

Better prompting:

Instead of:
“Summarize Pride & Prejudice”

Do:
“Summarize this chapter:” + paste text.

Because information in context window is direct access, not recollection.

# 7. LLM Psychology & Limitations

## 7.1 Models Need Tokens to Think

Each token generation = limited computation.

Therefore:
Complex reasoning must be spread across many tokens.

Bad training example:
Jump directly to final answer.

Good training example:
Step-by-step reasoning.

## 7.2 Counting & Character Failures

Models:

- See tokens, not characters.
- Are bad at counting.
- Fail on character-level tasks.

Example:
Counting letters in “strawberry” used to fail.

Solution:
Use code tool.

## 7.3 Identity & Self-Knowledge

When asked:
“What model are you?”

Model often hallucinates identity unless:

- Hardcoded training examples exist
- System message defines identity

There is no persistent self.

## 7.4 Surprising Quirks

Example:
“Is 9.11 > 9.9?”

Sometimes model fails.

Possible reason:
Internal activation overlaps with Bible verse patterns.

Key lesson:
These are stochastic systems with jagged edges.

# 8. Stage 3: Reinforcement Learning

## 8.1 Motivation (School Analogy)

Textbook analogy:

- Exposition → Pre-training
- Worked solutions → Supervised Fine-Tuning
- Practice problems → Reinforcement Learning

RL = practice without being shown the solution.

## 8.2 Trial-and-Error Solution Discovery

Process:

1. Give model prompt.
2. Generate many candidate solutions.
3. Check which ones reach correct final answer.
4. Reward correct ones.
5. Train to increase probability of successful reasoning paths.

Humans don’t decide exact reasoning steps.
Model discovers which token sequences work for it.

## 8.3 Why This Matters

Human cognition ≠ LLM cognition.

We don’t know:

- Which reasoning style is easiest for model.
- Which token paths are efficient.

Reinforcement learning lets the model:

- Practice
- Explore
- Discover its own effective strategies.

# Big Picture Summary

LLM training pipeline:

### Stage 1: Pre-Training

Learn statistical structure of internet.
→ Base model.

### Stage 2: Supervised Fine-Tuning

Imitate human conversational behavior.
→ Assistant.

### Stage 3: Reinforcement Learning

Practice and optimize reasoning strategies.
→ More capable, reliable assistant.

# Core Mental Models

- LLMs are **token predictors**, not truth engines.
- Parameters = compressed internet memory.
- Context window = working memory.
- Reasoning = computation spread across tokens.
- Hallucinations = statistical imitation mismatch.
- Tools = external memory + computation.
- RL = practice for the model.