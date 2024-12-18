1. Artificial Intelligence (AI):

AI refers to systems or machines that mimic human intelligence to perform tasks and can improve themselves based on the information they collect. AI includes both rule-based systems and systems that learn from data. It’s a broad umbrella term covering ML, DL, and more.
	•	Key Components: Problem-solving, reasoning, natural language processing (NLP), vision.
	•	Example: AI in a chatbot understands user questions and provides answers using rules and data analysis.


2. Machine Learning (ML):

ML is a subset of AI that focuses on creating algorithms that let machines learn patterns from data instead of being explicitly programmed. It includes supervised learning, unsupervised learning, and reinforcement learning.
	•	How It Works: You provide a dataset (features and labels for supervised learning), and the model learns patterns to predict outcomes.
	•	Example in Software: A fraud detection system in banking, where ML models flag suspicious transactions based on patterns.


3. Deep Learning (DL):

DL is a specialized ML subset that uses multi-layered neural networks (hence “deep”) to model and solve highly complex problems. These networks process input data through layers, extracting and learning features automatically.
	•	Why It’s Different: Unlike traditional ML, DL doesn’t rely on manual feature engineering. It’s computationally intensive and works well with big data.
	•	Example in Software: Object detection in images using convolutional neural networks (CNNs) or automatic speech recognition systems.


4. Generative AI (Gen AI):

Gen AI is a branch of AI focused on generating new, original content like text, images, or audio. It uses models like Generative Adversarial Networks (GANs) and transformers.
	•	How It Works: Models like GPT (text generation) and Stable Diffusion (image generation) learn from massive datasets to create outputs based on user prompts.
	•	Example in Software:
	•	Code assistants (like GitHub Copilot) generate boilerplate or optimize code snippets.
	•	DALL-E generates UI designs based on text descriptions.

---

## Generative AI Overview  
- **Generative AI (Gen AI)**: Subset of deep learning → subset of machine learning → subset of AI.  
- **Purpose**: Generates new data similar to training data (e.g., text, images, audio, code, video).  
- **Example**: Training data includes dog images and cartoons. The model combines features to generate cartoon dogs.  

## Foundation Models  
- **Definition**: Broad, large models trained on various inputs for multiple general tasks (e.g., text generation, summarization, chatbot functionality).  
- **Training Cost**: High computational requirements; typically developed by large companies (e.g., OpenAI, Meta, Amazon, Google).  
- **Types**:  
  - Open Source: Free models (e.g., Meta models, Google BERT).  
  - Commercial: Licensed models requiring payment (e.g., GPT by OpenAI).  

## Large Language Models (LLMs)  
- **Definition**: AI models relying on foundation models to generate coherent, human-like text.  
- **Examples**: ChatGPT (based on GPT-4).  
- **Training**: Trained on vast text data (books, articles, websites) and billions of parameters.  
- **Use Cases**: Translation, summarization, content creation, Q&A, etc.  
- **Prompt and Output**:  
  - **Prompt**: User query (e.g., "What is AWS?").  
  - **Output**: Non-deterministic text generation (different answers for the same prompt).  

## Gen AI for Images  
- **Capabilities**:  
  - Generate images from text prompts (e.g., "blue sky with clouds").  
  - Transform image styles (e.g., real image → anime style).  
  - Extract data from images (e.g., counting objects).  
- **Diffusion Models**:  
  - **Forward Diffusion**: Adds noise to images progressively.  
  - **Reverse Diffusion**: Starts with noise and de-noises to generate new images based on prompts.  

## Key Concepts  
- **Non-deterministic Outputs**: Text or image results vary due to probabilistic methods.  
- **LLM Operation**: Predicts and selects words based on statistical probabilities.  
