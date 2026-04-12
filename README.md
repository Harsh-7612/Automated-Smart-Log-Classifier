### Automated-Log-Classifier

<img width="649" height="397" alt="image" src="https://github.com/user-attachments/assets/051f2aac-5a5c-4e24-abae-23bd8fe85780" />

Summary:  
Developed a hybrid intelligent log-classification pipeline that combines regex-based pattern matching, transformer embeddings (MiniLM/Sentence-BERT), traditional ML classification, and LLM-based fallback routing to automate categorization of heterogeneous system logs. The system uses clustering-assisted pattern discovery for regex generation, semantic embedding models for unstructured logs, and source-aware routing for legacy-system specific classification.


Detailed:  
I started with a log dataset in Dataset/synthetic_logs.csv and loaded it into a notebook using pandas. The dataset contained 2410 log entries with fields including timestamp, source, log_message, target_label, complexity, clusters, and regex_label. I analyzed the dataset distribution across 6 log sources (ModernCRM, AnalyticsEngine, ModernHR, BillingSystem, ThirdPartyAPI, LegacyCRM) and 9 target classes (HTTP Status, Critical Error, Security Alert, Error, System Notification, Resource Usage, User Action, Workflow Error, Deprecation Warning).

To identify recurring structural patterns in the logs, I performed exploratory clustering analysis and grouped semantically similar messages into clusters. This allowed me to discover repetitive and highly structured log formats—such as login/logout events and email-related failures—that were suitable for deterministic regex-based classification.

Using these cluster insights, I manually engineered regex rules for structured and repetitive log patterns. I labeled logs matching these deterministic patterns via a regex_label field and separated the remaining unmatched logs into a non-regex subset for semantic classification.

I implemented the rule-based classification module in processor_regex.py, where structured log patterns such as user login/logout events, backup notifications, reboot messages, upload confirmations, and account creation logs are directly mapped to their respective categories. This serves as the system’s fast-path deterministic classifier.

For logs not handled by regex, I built a semantic classification pipeline in processor_bert.py using Sentence Transformers (all-MiniLM-L6-v2) to generate dense semantic embeddings. These embeddings are passed into a trained supervised classifier stored as log_classifier.joblib to predict the final label. I also implemented confidence thresholding, returning Unclassified when prediction confidence falls below 0.5.

To handle legacy-system logs originating from LegacyCRM, I designed a dedicated LLM-based classifier in processor_llm.py. Using the Groq API with Llama-3.3-70B-Versatile, I prompt the model to classify legacy logs into Workflow Error, Deprecation Warning, or Unclassified, addressing cases where traditional regex/ML approaches underperform.

Finally, I integrated all components into a unified routing pipeline in app.py. The routing logic first checks the log source:

If the source is LegacyCRM, the log is routed to the LLM classifier
Otherwise, regex-based classification is attempted first
If no regex rule matches, the semantic embedding + ML classifier is used as fallback

I also implemented batch CSV processing support through classify_csv, which processes an input CSV of logs, classifies each entry, and outputs predictions to output.csv.

The final system is a hybrid multi-stage intelligent log classification architecture that combines:

Clustering-assisted regex rule discovery
Deterministic rule-based classification
Transformer embedding-based semantic classification
LLM-based source-aware fallback routing

This architecture enables scalable, robust classification of heterogeneous structured and unstructured logs across modern and legacy enterprise systems.
