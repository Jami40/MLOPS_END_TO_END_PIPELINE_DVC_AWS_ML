<div align="center">

# [✉] MLOPS End-to-End Spam Classifier

### A reproducible pipeline for detecting spam messages with machine learning

<p>
        <img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white" alt="Python">
        <img src="https://img.shields.io/badge/scikit--learn-ML-F7931E?logo=scikitlearn&logoColor=white" alt="scikit-learn">
        <img src="https://img.shields.io/badge/DVC-pipeline-945DD6?logo=dvc&logoColor=white" alt="DVC">
        <img src="https://img.shields.io/badge/DVCLive-experiments-1F2937?logo=chartdotjs&logoColor=white" alt="DVCLive">
</p>

**Spam** or **ham**, classified through a versioned and experiment-ready workflow.

</div>

## [◆] What This Project Does

## What This Project Does

The pipeline takes raw SMS messages through a sequence of versioned stages:

1. Downloads and splits the source dataset into training and test sets.
2. Cleans and normalizes the text.
3. Converts text into TF-IDF features.
4. Trains a Random Forest classifier.
5. Evaluates the classifier with accuracy, precision, recall, and AUC.
6. Records parameters, metrics, and plots for experiment comparison.

## [▦] Repository Structure

```text
MLOPS_END_TO_END/
├── .dvc/                         # DVC metadata and local cache configuration
├── .dvcignore                    # Files ignored by DVC
├── data/
│   ├── raw/                      # Train/test CSV files after ingestion
│   │   ├── train.csv
│   │   └── test.csv
│   ├── interim/                  # Cleaned and normalized text data
│   │   ├── train_processed.csv
│   │   └── test_processed.csv
│   └── processed/                # Numerical TF-IDF features
│       ├── train_tfidf.csv
│       └── test_tfidf.csv
├── dvclive/
│   ├── metrics.json              # Metrics tracked by DVCLive
│   ├── params.yaml               # Parameters captured for the run
│   └── plots/metrics/             # Metric history files for DVC plots
│       ├── accuracy.tsv
│       ├── precision.tsv
│       └── recall.tsv
├── experiments/
│   ├── basic_ml_code.ipynb       # Exploratory notebook
│   └── spam.csv                  # Source dataset used by ingestion
├── logs/                         # Runtime log files created by pipeline stages
├── models/
│   └── model.pkl                 # Serialized trained Random Forest model
├── reports/
│   └── metrics.json              # Final model evaluation metrics
├── src/
│   ├── data_ingestion.py         # Downloads, cleans, and splits raw data
│   ├── pre-processing.py         # Encodes labels and normalizes message text
│   ├── feature_engineering.py    # Builds TF-IDF features
│   ├── model_building.py         # Trains and saves the classifier
│   └── model_evaluation.py       # Evaluates the model and logs metrics
├── dvc.yaml                      # DVC pipeline definition
├── dvc.lock                      # Locked pipeline dependency and output state
├── params.yaml                   # Configurable pipeline parameters
└── README.md                     # Project documentation
```

## [→] Pipeline Flow

```text
External SMS dataset
        |
        v
src/data_ingestion.py
        |
        v
 data/raw/*.csv
        |
        v
src/pre-processing.py
        |
        v
 data/interim/*_processed.csv
        |
        v
src/feature_engineering.py
        |
        v
 data/processed/*_tfidf.csv
        |
        v
src/model_building.py
        |
        v
 models/model.pkl
        |
        v
src/model_evaluation.py
        |
        +--------------------> reports/metrics.json
        |
        +--------------------> dvclive/metrics.json and plots/
```

## [⚙] Tech Stack

- **Python** for data processing and model training
- **pandas** and **NumPy** for data manipulation
- **NLTK** for tokenization, stopword removal, and stemming
- **scikit-learn** for train/test splitting, TF-IDF vectorization, and Random Forest classification
- **DVC** for reproducible pipelines, data tracking, and parameterized experiments
- **DVCLive** for experiment metrics and plots
- **PyYAML** for configuration loading

## [▶] Getting Started

### 1. Clone the repository

```bash
git clone <repository-url>
cd MLOPS_END_TO_END
```

### 2. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install pandas numpy scikit-learn nltk pyyaml dvc dvclive
```

The preprocessing stage downloads the required NLTK resources automatically:

```python
nltk.download('stopwords')
nltk.download('punkt')
```

## Run the Complete Pipeline

Run every stage in the order defined by `dvc.yaml`:

```bash
dvc repro
```

The pipeline creates or updates:

- `data/raw/`
- `data/interim/`
- `data/processed/`
- `models/model.pkl`
- `reports/metrics.json`
- `dvclive/`
- `logs/`

To run stages individually:

```bash
python src/data_ingestion.py
python src/pre-processing.py
python src/feature_engineering.py
python src/model_building.py
python src/model_evaluation.py
```

Running `dvc repro` is preferred because DVC only reruns stages whose dependencies or parameters have changed.

## [✦] Configuration

Pipeline parameters are stored in `params.yaml`:

```yaml
data_ingestion:
  test_size: 0.25

feature_engineering:
  max_features: 40

model_building:
  n_estimators: 20
  random_state: 2
```

Change a parameter and reproduce the pipeline:

```bash
dvc repro
```

For example, to compare a larger Random Forest:

```bash
dvc exp run -S model_building.n_estimators=100
```

## [▤] Inspect Results

View the final evaluation metrics:

```bash
cat reports/metrics.json
```

Compare tracked experiments:

```bash
dvc exp show
```

Inspect the pipeline graph:

```bash
dvc dag
```

Show metric history and plots:

```bash
dvc plots show
```

On Windows PowerShell, use `Get-Content reports/metrics.json` instead of `cat` if needed.

## [✓] Model Evaluation

The evaluation stage writes the following metrics to `reports/metrics.json`:

- **Accuracy**: Overall proportion of correctly classified messages
- **Precision**: Proportion of predicted spam messages that are actually spam
- **Recall**: Proportion of actual spam messages detected by the model
- **AUC**: Ranking quality across classification thresholds

## [↻] Reproducibility Notes

- `dvc.yaml` defines the pipeline stages, dependencies, parameters, and outputs.
- `dvc.lock` records the exact state of tracked dependencies and outputs.
- `params.yaml` makes key experiment settings easy to change.
- `dvclive/` stores metrics and parameter snapshots for experiment comparison.
- Generated datasets, model files, logs, and reports may change after `dvc repro`.

## [⌘] Development Workflow

```text
Edit params.yaml or source code
              |
              v
          dvc repro
              |
              v
      inspect metrics and plots
              |
              v
       compare with dvc exp show
              |
              v
        keep the best experiment
```

## [§] License

No license file is currently included in this repository. Add one before distributing the project publicly.
