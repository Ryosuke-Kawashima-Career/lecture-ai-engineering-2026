# 第1回 解答

## 演習

- 講座に固有の質問（宿題の3レベル）へのモデルの回答（要約でよい）：
```text
uv run python -m app.ask "LoRA Explain with some examples"
[model] gemini-3.5-flash-lite
[質問] LoRA Explain with some examples

[Tokens] Input_tokens: 7, output_tokens: 1056
**LoRA (Low-Rank Adaptation)** is a popular and efficient technique used in machine learning to **fine-tune large language models (LLMs)** and image generators (like Stable Diffusion) without breaking the bank or requiring massive computer clusters.

Here is a simple, non-technical explanation, followed by real-world analogies and examples.

---

### The Problem LoRA Solves

Imagine you have a giant, genius-level AI brain (like GPT-4 or Llama 3) that knows everything about the world. This AI has **100 billion parameters** (connections in its brain). 

If you want this AI to learn a *new specific skill*—for example, to write medical prescriptions or speak like Shakespeare—you traditionally have two choices:
1. **Full Fine-Tuning:** Retrain all 100 billion parameters. This is insanely expensive, takes days/weeks, and requires supercomputers.
2. **Prompt Engineering:** Tell the AI in the prompt, *"Act like Shakespeare."* This is cheap, but the AI often forgets or breaks character.

**LoRA is the sweet spot.** Instead of changing all 100 billion connections, LoRA freezes the original AI brain and attaches a tiny "sidecar" brain (the low-rank adaptation) that handles the new skill.

---

### The Analogy: The Expert Chef

* **The Pre-trained Model (Base AI):** A world-renowned master chef who knows how to cook every cuisine in the world.
* **Full Fine-Tuning:** Sending the master chef back to culinary school for 4 years just to learn how to bake *one specific type* of French croissant. (Inefficient).
* **Prompt Engineering:** Handing the chef a sticky note that says, *"Pretend you are a French baker."* (Hit or miss results).
* **LoRA:** Buying the master chef **one specialized cookbook** just for French croissants. The chef keeps all their master skills, but now they have a small, dedicated guide for the specific task. 

---

### How LoRA Works (Simply Put)

Instead of recalculating all the weights in a massive neural network, LoRA mathematically approximates the changes using much smaller matrices. 

* **Original Model:** $10,000 \times 10,000$ matrix (100,000,000 parameters to train).
* **LoRA:** Splits that into two tiny matrices, say $10,000 \times 10$ and $10 \times 10,000$ (only 200,000 parameters to train).

**Result:** You reduce the number of trainable parameters by **up to 99%**, and GPU memory requirements drop drastically. You can train a LoRA on a single consumer graphics card (like an RTX 3090/4090) in a few hours.

---

### Real-World Examples of LoRA

#### 1. Image Generation (Stable Diffusion)
* **The Base Model:** An AI trained to generate any kind of photo.
* **The LoRA:** A 50-megabyte file trained on 20 photos of a specific anime character (e.g., *Naruto* or a specific artist's style).
* **How it's used:** You load the base Stable Diffusion model, plug in the "Anime Character LoRA," and now the AI can draw that exact character accurately without you having to retrain the whole image generator.

#### 2. Customizing LLMs for Business
* **The Base Model:** Llama 3 (an open-source language model).
* **The LoRA:** A small file trained on your company's customer support logs and product manuals.
* **How it's used:** When a customer asks a question, Llama 3 uses the LoRA weights to answer specifically using your company's return policy and brand voice.

#### 3. Voice and Accent Adaptation (Audio AI)
* **The Base Model:** A text-to-speech AI that speaks standard American English.
* **The LoRA:** A file trained on 30 minutes of a specific person's voice with a heavy Scottish accent. 
* **How it's used:** The AI now generates speech in that exact person's voice and accent.

---

### Summary of Benefits

1. **Cheap:** You don't need million-dollar supercomputers; you can train a LoRA on a standard GPU.
2. **Small File Sizes:** While the original AI model might be 20 Gigabytes, a LoRA file is usually only **10 to 100 Megabytes**. 
3. **Modular:** You can "plug and play" different LoRAs. You can have one LoRA for coding, one for medical advice, and one for poetry, and swap them out instantly on top of the same base model.

```
- 用語集の正しい意味と比べて気づいたこと：The output was more general than the content on the glossary. In this case, documents prepared by the staff in Matsuo laboratory are much better in explaining fundamental concepts. I realized that allowing AI to make a necessary and sufficient answer is important to increase the UX.

- 表示できたトークン数（入力 / 出力）：[Tokens] Input_tokens: 7, output_tokens: 1056

## 宿題

- 取り組んだレベル：発展 (Advanced)
- やったこと：Measure response time and token count; compare across models and questions. Also discuss the relation to free-quota
rate limits.
I asked a question about what is PPO.
- 結果：I got the meaning of PPO with the elapsed time and Input and output tokens as follows.
```text
uv run python -m app.ask "what is PPO?"
[model] gemini-3.5-flash-lite
[質問] what is PPO?

[Elapsed] 3.316 sec
[Tokens] Input_tokens: 6, output_tokens: 615
**PPO** stands for **Proximal Policy Optimization**. 

In short, it is one of the most popular and effective algorithms in **Reinforcement Learning (RL)**—the type of machine learning where an AI agent learns to make decisions by trial and error to achieve a goal.

Developed by researchers at OpenAI in 2017, PPO is the algorithm behind many famous AI breakthroughs, including **OpenAI Five** (which beat human champions in the video game *Dota 2*) and the underlying technology for training **ChatGPT** (specifically during the RLHF—Reinforcement Learning from Human Feedback—phase).

Here is a breakdown of how it works and why it is so popular:

---

### 1. The Core Problem PPO Solves
In Reinforcement Learning, an agent tries to maximize its reward. To do this, it updates its "policy" (its strategy or set of rules for what action to take in a given situation). 

Traditionally, updating a policy was very risky:
* If you update the policy **too little**, the AI learns too slowly.
* If you update the policy **too much** based on a single lucky batch of data, the AI's behavior can "fall off a cliff," becoming completely broken and unrecoverable.

### 2. How PPO Works (The "Proximal" Part)
"Proximal" means "close." PPO’s superpower is that it **prevents the AI from making dangerously large policy updates**. 

It uses a mathematical mechanism called a **clipped surrogate objective function**. In plain English, this means:
* PPO looks at how much the new policy has changed compared to the old policy.
* If a proposed change would alter the policy too drastically, PPO **clips** (limits) the update. 
* This ensures the AI takes small, safe, and steady steps toward improvement, resulting in very stable training.

---

### Why is PPO so popular?
Before PPO, an algorithm called TRPO (Trust Region Policy Optimization) was the state-of-the-art, but it was extremely complex and computationally expensive. PPO became the industry standard because of three main traits:

1. **Simplicity:** It is much easier to implement and tune than its predecessors.
2. **Stability:** It rarely suffers from catastrophic drops in performance during training.
3. **Sample Efficiency (Relative):** While not the absolute fastest learner, it balances stability and speed remarkably well across a wide variety of tasks.

### Where is PPO used?
* **Robotics:** Teaching robotic hands to manipulate objects, walk, or grasp items.
* **Video Games:** Training AIs to play games like *Super Mario*, *Atari*, and complex strategy games.
* **Large Language Models (LLMs):** Fine-tuning models like GPT to be helpful, harmless, and honest by aligning them with human preferences (RLHF).
```
- 気づき・疑問：
My question is about the roles of hand coding since AI Agents are faster in understanding the directory structure and writing a correct code base. I understand that hand coding is suited for adjusting the code, but I would like to delve further into how we can improve coding skills in the era of AI.
