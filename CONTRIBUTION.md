# Contributing

We welcome improvements to the routing logic and classification models!

## Adding New Log Sources
1. **Regex Discovery:** Use the provided notebooks to run clustering analysis on new log sources. 
2. **Update Rules:** Add highly confident patterns to `src/processor_regex.py`.
3. **Model Retraining:** If regex is insufficient, generate new embeddings and update `models/log_classifier.joblib`. 
4. **LLM Routing:** Only route to `processor_llm.py` if the log format is highly unstructured or stems from a legacy system. 

Please ensure all tests pass and your Groq API key is never committed to version control.
