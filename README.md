# Classify Logs

A hybrid log classification system that uses multiple machine learning and pattern-matching techniques to automatically categorize logs from different sources.

## Overview

This project implements an intelligent log classification system that processes logs from various sources and assigns them to predefined categories. It uses a multi-tier classification approach:

1. **Regex Patterns** - Fast pattern matching for known log formats
2. **BERT Embeddings** - Deep learning-based classification using Sentence Transformers
3. **Large Language Model (LLM)** - Advanced NLP using Groq's LLaMA model for complex logs

## Features

- **Multi-source log handling** - Supports logs from different systems (ModernCRM, BillingSystem, AnalyticsEngine, ModernHR, LegacyCRM, etc.)
- **Hybrid classification approach** - Combines regex patterns, transformer models, and LLMs
- **CSV processing** - Batch process logs from CSV files
- **Fallback mechanism** - Gracefully falls back to more sophisticated methods when simpler approaches fail
- **Extensible architecture** - Easy to add new processors and classification categories

## Architecture

### Classification Workflow

```
Log Input
   ↓
Is source "LegacyCRM"?
   ├─→ YES: Use LLM Classifier → Output Label
   └─→ NO:  Use Regex Classifier
           ↓
       Match found?
           ├─→ YES: Output Label
           └─→ NO:  Use BERT Classifier → Output Label
```

### Components

#### `classify.py`
Main module containing the classification pipeline:
- `classify(logs)` - Classifies a list of (source, log_message) tuples
- `classify_log(source, log_message)` - Classifies a single log based on source
- `classify_csv(input_file)` - Processes an input CSV file and outputs classified logs

#### `processor_regex.py`
Pattern-based classifier using regular expressions for:
- User Actions (login/logout)
- System Notifications (backups, updates, file uploads)
- Basic security events

#### `processor_bert.py`
Deep learning classifier using Sentence Transformers:
- Uses the `all-MiniLM-L6-v2` embedding model
- Employs a pre-trained scikit-learn classifier (joblib)
- Confidence threshold of 0.5 for classification
- Returns "Unclassified" for low-confidence predictions

#### `processor_llm.py`
Advanced LLM-based classifier using Groq API:
- Uses LLaMA 3.3 70B model
- Specialized for complex logs from LegacyCRM
- Categories: Workflow Error, Deprecation Warning, Unclassified

## Dependencies

- **fastapi** (0.115.6) - Web framework (optional, for API deployment)
- **python-dotenv** (1.0.1) - Environment variable management
- **groq** (0.9.0) - Groq API client for LLM access
- **sentence-transformers** (3.3.1) - Sentence embeddings and transformers
- **joblib** (1.3.2) - Model serialization and loading
- **pandas** (2.2.3) - Data processing and CSV handling
- **scikit-learn** (1.6.0) - Machine learning utilities

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Classify-logs
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env and add your Groq API key
   # GROQ_API_KEY=your_api_key_here
   ```


## 📁 Project Structure

```
Classify-logs/
├── classify.py              # Main classification pipeline
├── processor_regex.py       # Regex-based classifier
├── processor_bert.py        # BERT-based classifier
├── processor_llm.py         # LLM-based classifier
├── requirements.txt         # Python dependencies
├── .env                     # Environment variables (Groq API key)
├── models/
│   └── log_classifier.joblib # Pre-trained BERT classifier
├── resources/
│   ├── test.csv            # Sample input CSV
│   └── output.csv          # Output from classification
└── training/               # Training data and scripts
```

## Input/Output Format

### Input CSV (resources/test.csv)
| source | log_message |
|--------|-------------|
| ModernCRM | IP 192.168.133.114 blocked due to potential attack |
| BillingSystem | User User12345 logged in. |
| LegacyCRM | Case escalation for ticket ID 7324 failed... |

### Output CSV (resources/output.csv)
| source | log_message | target_label |
|--------|-------------|--------------|
| ModernCRM | IP 192.168.133.114 blocked... | Security Issue |
| BillingSystem | User User12345 logged in. | User Action |
| LegacyCRM | Case escalation for ticket... | Workflow Error |


## Classification Categories

The system classifies logs into:
- **User Action** - User login/logout events
- **System Notification** - Backups, updates, file operations
- **Workflow Error** - Business process failures
- **Deprecation Warning** - API/feature deprecation notices
- **Security Issue** - Security-related events
- **Unclassified** - Logs that don't match any category with sufficient confidence

## 👤 Author

[Aruna V S]
