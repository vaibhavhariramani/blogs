---
title: "MLOps with MLflow: Tracking, Packaging, and Deploying Models Properly"
excerpt: "MLOps turns messy notebook experiments into reproducible, deployable, monitored systems — and MLflow is one of the most popular open-source tools for doing it."
header:
  image: /assets/images/machine_learning/t2.jpg
  teaser: /assets/images/machine_learning/machine-learning.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Training a good model, as covered in our [Essentials of Machine Learning](/MachineLearning/Essentials-of-the-Machine-Learning/) post, is only part of the job. In practice teams run hundreds of experiments, tweak hyperparameters, swap datasets, and eventually need to deploy a chosen model and keep it running reliably in production. **MLOps** (Machine Learning Operations) is the discipline that manages that entire lifecycle, and **MLflow** is one of the most widely used open-source tools for implementing it.

## What Problem Does MLOps Solve?

Without MLOps practices, ML projects tend to accumulate these problems:

- **"Which run produced this model?"** — Notebooks get re-run with slightly different parameters and nobody remembers which combination produced the model currently in production.
- **"Why did accuracy drop after redeploying?"** — Without versioning, you can't easily roll back to a previous known-good model.
- **"How do we even ship this?"** — A model trained in a notebook depends on a specific set of library versions and preprocessing code that isn't captured anywhere reusable.
- **"Is the model still good six months later?"** — Once deployed, model performance can silently degrade as real-world data drifts away from the training distribution, and nobody notices until something breaks downstream.

MLOps addresses these with four connected practices: **experiment tracking**, **model versioning**, **reproducible packaging/deployment**, and **monitoring** — mirroring the discipline that DevOps brought to traditional software.

## MLflow's Core Components

MLflow is organized around four modules that map directly onto those MLOps needs:

### 1. MLflow Tracking
Logs parameters, metrics, and artifacts (model files, plots, datasets) for every run of your training code, so you can compare experiments later in a UI or programmatically.

### 2. MLflow Projects
A standard, reproducible format for packaging data science code — a simple YAML file specifies the environment (conda/pip dependencies) and entry points, so anyone (or any automated pipeline) can rerun your exact training job.

### 3. MLflow Models
A standard format for packaging models from any framework (scikit-learn, PyTorch, TensorFlow, XGBoost, etc.) so they can be deployed consistently — as a REST API, a batch scoring job, or loaded directly in Python — without custom glue code per framework.

### 4. MLflow Model Registry
A centralized store for versioning models, moving them through stages (e.g., Staging -> Production -> Archived), and tracking lineage back to the run and dataset that produced them.

## A Simple Worked Example

Here is a minimal example that trains a model and logs the experiment with MLflow:

```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.datasets import load_iris

X, y = load_iris(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

mlflow.set_experiment("iris-classifier")

with mlflow.start_run():
    n_estimators = 100
    max_depth = 5

    model = RandomForestClassifier(n_estimators=n_estimators, max_depth=max_depth)
    model.fit(X_train, y_train)

    preds = model.predict(X_test)
    accuracy = accuracy_score(y_test, preds)

    # Log hyperparameters and metric
    mlflow.log_param("n_estimators", n_estimators)
    mlflow.log_param("max_depth", max_depth)
    mlflow.log_metric("accuracy", accuracy)

    # Log the model itself as an artifact
    mlflow.sklearn.log_model(model, artifact_path="model")

    print(f"Logged run with accuracy: {accuracy:.4f}")
```

Running this launches a tracked experiment. You can then start the MLflow UI locally:

```bash
mlflow ui
```

...and open a browser to compare every run side by side — parameters, metrics, and the exact model artifact for each — instead of hunting through notebook cell outputs.

Once you're happy with a run, you register the model:

```python
result = mlflow.register_model(
    "runs:/<run_id>/model",
    "iris-classifier-prod"
)
```

That model now has a version number and a lineage back to the exact run, hyperparameters, and metrics that produced it — the pieces you need to answer "which model is live, and why."

## Deployment and Monitoring

Once registered, MLflow Models can be served directly:

```bash
mlflow models serve -m "models:/iris-classifier-prod/1" -p 5001
```

This spins up a REST endpoint that accepts JSON input and returns predictions, letting you move from "model in a notebook" to "model behind an API" without hand-writing a serving wrapper. In a full production setup you would pair this with:

- **Monitoring** — tracking input data distributions and prediction distributions over time to detect drift (see how EDA techniques from our [Step-by-Step Guide to EDA](/MachineLearning/Step-by-Step-Guide-to-EDA/) post apply just as much to monitoring live data as to training data).
- **Automated retraining triggers** — kicking off a new training run (logged as a new MLflow run, naturally) when performance degrades past a threshold.
- **CI/CD integration** — running MLflow Projects as part of a pipeline that tests, validates, and promotes models through Staging to Production automatically.

## Where MLOps Fits in the Workflow

Think of the ML lifecycle as a loop, not a line:

1. Explore and prepare data (EDA).
2. Train and tune models (experimentation) — tracked with MLflow Tracking.
3. Evaluate and select the best model — registered with MLflow Model Registry.
4. Package and deploy — via MLflow Models.
5. Monitor performance in production.
6. Detect drift or degradation, and loop back to step 1 or 2 with fresh data.

MLOps tooling like MLflow doesn't replace good modeling practice — it makes the whole loop repeatable, auditable, and safe to run continuously, which is exactly what's needed once a model moves from "interesting notebook result" to "system other people depend on."

# Made with ❤️ by Vaibhav Hariramani
