### Automated-Log-Classifier

<img width="649" height="397" alt="image" src="https://github.com/user-attachments/assets/051f2aac-5a5c-4e24-abae-23bd8fe85780" />



# Hybrid Intelligent Log Classifier

![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)
![Groq](https://img.shields.io/badge/Groq-Llama--3.3--70B-black)
![Sentence-BERT](https://img.shields.io/badge/Sentence--BERT-all--MiniLM-orange)
![License](https://img.shields.io/badge/license-MIT-green.svg)

## About the Project

The Hybrid Intelligent Log Classifier is a multi-stage routing pipeline engineered to automate the categorization of heterogeneous system logs. By combining clustering-assisted regex rules, transformer-based semantic embeddings, and LLM-assisted classification, the system efficiently maps unstructured log data to 9 target classes across 6 distinct enterprise log sources.

Designed for both speed and accuracy, the architecture uses deterministic rules for known structured patterns, a Sentence-Transformer ML model for unstructured semantic logs, and a dedicated LLM fallback for volatile legacy systems.

### Pipeline Architecture & Routing Logic
1. **Source-Aware LLM Routing:** Logs originating from `LegacyCRM` are immediately routed to `processor_llm.py`, utilizing `Llama-3.3-70B-Versatile` via the Groq API to navigate deprecated or complex workflow errors.
2. **Deterministic Fast-Path:** All other sources pass through `processor_regex.py`. Known structural patterns (e.g., user logins, backups, reboots) are instantly categorized.
3. **Semantic ML Fallback:** Unmatched logs default to `processor_bert.py`, where dense semantic embeddings (`all-MiniLM-L6-v2`) are fed into a supervised classifier. Predictions falling below a 0.5 confidence threshold are safely tagged as `Unclassified`.

## Prerequisites

* Python 3.8+
* Groq API Key (for LegacyCRM LLM routing)

## Installation

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/Harsh-7612/Automated-Smart-Log-Classifier.git](https://github.com/Harsh-7612/Automated-Smart-Log-Classifier.git)
   cd Automated-Smart-Log-Classifier
   ```
2. Setup Environment variables:
   ```bash
   cp .env.example .env
   # Edit .env and add your GROQ_API_KEY
   ```
3. Install Dependencies:
   ```bash
   pip install -r requirements.txt
   ```
