# TextFlow — No-Code NLP Platform

A visualization-first, no-code web platform for end-to-end NLP on labeled and unlabeled text datasets. Built with **Flask** (backend) and **vanilla JavaScript** (frontend).

> Supervised by **Dr. Uzair Ahmad** · Designed and implemented by **Adnan Abdelkarim** (AI Engineer & First Author)

---

## Features

### Data Input & Auto-Detection
TextFlow automatically detects whether a dataset is labeled or unlabeled and routes it accordingly.

| Type | Supported Formats |
|---|---|
| Labeled | CSV (single-label), CSV (multi-label binary columns), TXT (tab-separated multi-label) |
| Unlabeled | CSV, TXT, DOCX, PDF, XLSX |

Label-dependent features (Label Distribution, Class Overlap, Preprocessing, Predictive Modeling) display an informational message for unlabeled data. All other features work on both.

### Exploratory Analysis
Word cloud · Keyword co-occurrence network (D3.js) · Zipf's Law plot · Vocabulary coverage · Label distribution · Class overlap · Corpus statistics (token count, vocabulary size, TTR, mean document length)

### Advanced NLP Analysis
Sentiment analysis (AFINN) · Named Entity Recognition (spaCy) · Topic modeling (LDA) · N-gram exploration · Text classification analysis

### Preprocessing
- Normalization: lowercasing, punctuation removal, tokenization
- Stemming / lemmatization (mutually exclusive)
- Feature extraction: TF, TF-IDF, Word2Vec (configurable vector size)
- Class imbalance: SMOTE, random over/undersampling — SMOTE auto-excluded for multi-label targets
- Live preview with class distribution before/after resampling

### Predictive Modeling
**Traditional ML** (scikit-learn): Naive Bayes, Logistic Regression, KNN, SVM, Gaussian Discriminant Analysis — available models adapt by feature extraction method (see below)

**Transformers** (fine-tuned): BERT-Tiny, BERT-Small, DistilBERT, BERT — real-time progress via Server-Sent Events

**Multi-label**: Binary relevance via `MultiOutputClassifier` (one classifier per label)

**Metrics — single-label/multi-class**: Accuracy, Precision, Recall, F1, confusion matrix, ROC/AUC, Type I/II error breakdown, misclassified document inspection

**Metrics — multi-label**: Hamming loss, F1-macro, F1-micro

---

## Setup

```bash
git clone https://github.com/AdnanAbdelkarim/TextFlow-No-Code-NLP-Platform.git
cd TextFlow-No-Code-NLP-Platform
python3.11 -m venv env311 && source env311/bin/activate
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
python main.py
```

Open `http://localhost:5000`.

---

## Requirements

```
Flask>=2.2,<3.0
flask-cors
gunicorn>=21.2
numpy<2
scikit-learn>=1.3,<1.6
scipy>=1.10,<1.12
imbalanced-learn>=0.11
transformers>=4.20
torch>=2.0
sentencepiece tiktoken tokenizers
gensim>=4.3
nltk>=3.8
spacy==3.7.5
en-core-web-sm @ https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-3.7.1/en_core_web_sm-3.7.1-py3-none-any.whl
afinn>=0.1
pypdf>=4.0
python-docx>=1.1
```

---

## Project Structure

```
TextFlow/
├── main.py
├── requirements.txt
├── README.md
├── routes/
│   ├── nlp.py                  # NER, sentiment, topic modeling endpoints
│   ├── predictive.py           # ML prediction and preprocessing preview endpoints
│   └── visualizations.py       # Word frequency, co-occurrence, Zipf endpoints
├── services/
│   ├── cache.py                # Server-side caching
│   ├── preprocessing.py        # Normalize → vectorize → resample pipeline
│   ├── tokenization.py
│   ├── topic_labels.py
│   └── nltk_setup.py
├── static/
│   ├── css/all.css
│   └── js/
│       ├── core/
│       │   ├── api.js              # Fetch/retry utilities
│       │   ├── fileHandler.js      # Upload, format detection, CSV/PDF/DOCX parsing
│       │   ├── state.js            # Global state (isDatasetLabeled, etc.)
│       │   ├── sessionCache.js
│       │   ├── debug.js
│       │   └── utils.js
│       ├── nlp/
│       │   ├── ner.js
│       │   ├── sentiment.js
│       │   └── topicModeling.js
│       ├── pages/
│       │   ├── advanced.js
│       │   ├── overview.js
│       │   └── visualizations.js
│       ├── ui/
│       │   ├── tabs.js             # Tab switching and feature gating
│       │   ├── classification.js
│       │   └── forms.js
│       ├── visualizations/
│       │   ├── classOverlap.js
│       │   ├── keywordNetwork.js
│       │   ├── labelDistribution.js
│       │   ├── pieChart.js
│       │   ├── vocabCoverage.js
│       │   ├── wordCloud.js
│       │   └── zipf.js
│       ├── predictive.js
│       ├── preprocessing.js
│       ├── script.js
│       └── afinn.json
└── templates/
    ├── index.html
    ├── overview.html
    ├── visualizations.html
    ├── advanced.html
    ├── preprocessing.html
    └── predictive.html
```

---

## Architecture Notes

### Dataset Detection
`fileHandler.js` detects dataset type at upload using column structure and a 50%-presence heuristic for XLSX. All pages consume `window.isDatasetLabeled()` from `state.js`. DOCX/PDF text is extracted in-browser (mammoth / pdfjs), capped at 5,000 rows, and stored as synthetic CSV before auto-redirecting to Overview.

### Preprocessing Pipeline (leakage-free)
1. Split into train/test first
2. Fit vectorizer on train only → transform test
3. Apply resampling to train only
4. SMOTE skipped automatically for multi-label targets

### Model Selection by Feature Extraction

| Feature Extraction | Available Models |
|---|---|
| TF / TF-IDF | Naive Bayes, Logistic Regression, SVM, KNN |
| Word2Vec | GDA, Logistic Regression, SVM, KNN, BERT variants |

---

## Academic Context

- **Dr. Uzair Ahmad** — Author & Supervisor
- **Adnan Abdelkarim** — AI Engineer & Author

Special thanks to Dr. Uzair Ahmad for his supervision and guidance throughout this project.