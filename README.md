# TextNeX

Heterogeneous ensemble of lightweight Text Network eXperts.

TextNet-HeX is a modular and efficient framework for text classification using an ensemble of lightweight transformer-based models. It consists of three main phases: Generator, Selector, and Aggregator.

## Generator
---

#### Information Flow

This component trains a large number of transformer-based text classification models using randomized hyperparameters to promote diversity. Each model is evaluated on a validation set, and those exceeding a performance threshold are saved along with their metrics. The process encourages exploration of a wide configuration space to build a pool of strong and varied candidates.

#### Preparation Steps

```python
# Step 1: Specify root path of data, e.g.  
root = 'Datasets'

# Step 2: Specify Task, e.g.  
task = 'substask_1' # corrsponds to AuTexTification dataset

# Step 3: Specify number of iterations, e.g.  
N = 50  # the number of trained models to generate

# Step 4: Specify save path, e.g.  
root_path_save = 'Storage'

# tep 5: Specify validation F1 threshold to save only useful models, e.g.  
val_f1 > 0.62

# Step 6: Specify the model architecture to load, e.g.  
model_load_path = "distilbert-base-uncased"
# or  
"microsoft/MiniLM-L12-H384-uncased"  
# or  
"google/mobilebert-uncased"  

# and assign a simple name for saving and tracking, e.g.:  
model_name = "distilbert"` or "MiniLM"` or "mobilebert"

# Step 7: Run the script. After training completes, your output will be:

Storage/  
├── models/    → Saved models (e.g., distilbert_0.7435.pt)  
└── metrics/   → Excel files containing evaluation metrics of each saved model
```


>  **Note:** For enhanced ensemble diversity, it is strongly recommended to run the script **separately for each of the three architectures** (DistilBERT, MiniLM, MobileBERT). This ensures that the generated model pool contains complementary experts with varied representational power.


## Selector
---

#### Information Flow

In this stage, all saved models are evaluated based on their output probabilities across the data splits. These outputs are standardized and clustered to group similar models, from which representative "experts" are selected using a centroid-based method. The selected models are saved separately to form a compact, high-quality ensemble candidate set.

#### Output

The eXpert models are all stored in the `Storage` directory in order to be used by the `Aggregator` component.

```
Storage/  
└── HeX/   → Folder containing the selected expert models
```

## Aggregator
---
#### Information Flow
This final step loads the selected expert models and extracts their predictions on validation data. An optimization algorithm (SQP) computes the best combination of model weights to maximize ensemble performance. The resulting ensemble is tested on unseen data, and final performance metrics are reported to evaluate its effectiveness.


## How to Run

```bash
# Step 1: Clone a virtual environment
python3 -m venv .venv

# Step 2: Install required dependencies
pip install requirements.txt

# Step 3: Data preparation
unzip Datasets.zip

# Step 4: Generate models
python3 01_Generator.py

# Step 5: Select expert models
python3 02_Selector.py

# Step 6: Aggregate and evaluate
python 03_Aggregator.py
```

## Evaluation
---

#### Classification Metrics

**TextNeX** was evaluated using the **Accuracy**, Area Under the Curve (**AUC**) and Geometric Mean (**GM**) metrics. Performance was monitored across all 3 used datasets.

| Metric     | AuTexTification | TweepFake | AI Text Detection Pile |
|------------|------------------|-----------|--------------------------|
| **Accuracy**   | **0.775**        | **0.943** | **0.908**               |
| **AUC**        | **0.848**        | **0.948**    | **0.935**               |
| **GM**         | **0.772**        | **0.940** | **0.850**               |

#### Inference Speed and Resource Consumption

- Average response time: **7.2 ms**
- Memory (RAM) usage: **534 MB**