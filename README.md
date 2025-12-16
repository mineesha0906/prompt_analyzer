# 🚀 Prompt Analyzer: A Hybrid Ensemble Approach

[![Live Demo](https://img.shields.io/badge/Live-Demo-brightgreen)](https://prompt-analyzer-omega.vercel.app/)

## 1. Introduction
With the rapid increase in demand for Large Language Models (LLMs), Prompt Engineering has emerged as a critical skill. However, evaluating prompt quality remains largely subjective, relying on human intuition rather than objective metrics. Users often lack a standardized way to determine what constitutes a "good" input, and automated systems frequently struggle to distinguish between well-written literary text and actual actionable instructions.

The **Prompt Analyzer** was developed to bridge this gap by providing an end-to-end system that grades LLM prompts empirically on a scale of 0-100. By transitioning from a local prototype to a cloud-native production system, the project introduces a robust framework for automated prompt engineering education and quality assurance.

---

## 2. Key Features
* **Empirical Scoring**: Provides a consistent numerical rating between 0-100 through a weighted fusion of structural (BERT) and semantic (LLM) analyses.
* **Resource Optimization**: Employs a lightweight BERT model as a "Gatekeeper" to reject low-quality inputs before they reach expensive LLM APIs.
* **Intent Verification**: Uses a probabilistic classifier to identify and remove non-instructional texts, such as poems or stories, from the scoring pipeline.
* **Data Balancing**: Mitigates "Mode Collapse" (the "Tower of 65" problem) through data up-sampling, ensuring high and low-quality prompts are detected accurately.
* **Live Demo**: https://prompt-analyzer-omega.vercel.app/

---

## 3. System Architecture & Methodology
The system utilizes a "Defense-in-Depth" architecture, decoupling structural analysis from semantic reasoning to optimize computational efficiency.
### Architecture Flow
```mermaid
graph TD
    A[User Prompt] --> B[Stage 1: Heuristic Guardrails]
    B -->|Language/Length Check| C{Is Valid?}
    C -->|No| R[Rejected: Score 0]
    C -->|Yes| D[Stage 2: BERT Gatekeeper]
    D --> E{Score >= 30?}
    E -->|No| R
    E -->|Yes| F[Stage 3: LLM Semantic Analysis]
    F --> G{Intent Strength < 20?}
    G -->|Yes: Trapdoor| R
    G -->|No| H{Role Score > 80?}
    H -->|Yes| I[Apply +10% Reward Boost]
    H -->|No| J[Calculate Weighted Average]
    I --> J
    J --> K[Final Hybrid Score 0-100]
```

### 🛠️ The Multi-Stage Pipeline
The system implements a "Defense-in-Depth" strategy to ensure high quality and relevance while optimizing computational costs.



#### 1. Stage 1: Heuristic Guardrails (Pre-Processing)
This layer performs instant sanitation before any API calls are triggered.
* **Language Detection**: Uses `langdetect` to reject any non-English text, preventing "Lorem Ipsum" or junk inputs.
* **Sanity Checks**: Rejects prompts that are too short (< 3 words) or consist of repetitive spam patterns.

#### 2. Stage 2: Structural Scoring (The BERT Gatekeeper)
A fine-tuned **BERT regressor** evaluates the technical structure, grammar, and token distribution.
* **Resource Optimization**: If the Technical Score is **< 30**, the prompt is rejected immediately as "incoherent gibberish" to save expensive GPU cycles.
* **Service**: Hosted as a containerized Docker service via Hugging Face Spaces for low-latency inference.

#### 3. Stage 3: Semantic Scoring & Intent Verification (Cloud LLM)
High-quality inputs that pass the Gatekeeper reach the **Mistral-7B-Instruct** model for deep analysis.
* **Probabilistic Intent Classifier**: Quantifies Intent Strength (0-100) to distinguish between actionable instructions and non-instructional content like poems or speeches.
* **6-Point Rubric**: Evaluates prompts based on Clarity, Specificity, Contextual Priming, Constraint Adherence, Reasoning Complexity, and Goal Alignment.
* **Logic Trapdoor**: If the Intent Score is **< 20**, all scores are overridden to 0, and the status is set to REJECTED to prevent "hallucinated" quality scores for unrelated text.
---
## 4. Mathematical Logic

### Calibration Formula
Raw outputs from the BERT regressor are often conservative. We apply linear scaling to map the effective range to a human-readable 0-100 scale:
* $$Final Score = (Raw Score - 30) \times 3.33$$

### Hybrid Weighting Strategy
To solve the "Intent vs. Content" dichotomy, the system evolved from rewarding fluency to rewarding utility:
* **V1.0 (The Grammar Trap)**: $65\%$ BERT / $35\%$ LLM. This incorrectly gave high scores to "pretty" text like poems.
* **V2.0 (The Intent Shift)**: $30\%$ BERT / $70\%$ LLM. This prioritizes meaning and utility over linguistic fluency.

---

## 5. Technical Implementation & Setup

### Tech Stack
* **Machine Learning**: Hugging Face Transformers (BERT), PyTorch, LangChain.
* **Backend**: FastAPI for microservices and Flask for orchestration.
* **Deployment**: Vercel (Serverless Functions) and Hugging Face Inference API.

### Installation & Local Setup
1.  **Clone the Repository**:
    ```bash
    git clone [https://github.com/genaivitbcommunity/prompt_analyzer.git](https://github.com/genaivitbcommunity/prompt_analyzer.git)
    cd prompt_analyzer
    ```
2.  **Install Dependencies**:
    ```bash
    pip install -r requirements.txt
    ```
3.  **Run the Application**:
    ```bash
    python main.py
    ```

---

## 6. Project Contributors
* Aditya Mishra, Amritanshu Gupta, Abhinav Kumar, Ayush Mishra, Anuj Srivastava, Mineesha Ranjan Swain.
