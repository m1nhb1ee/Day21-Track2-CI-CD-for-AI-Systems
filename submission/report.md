# MLOps Lab Report

## Selected Hyperparameters

The selected model is `RandomForestClassifier` with:

```yaml
n_estimators: 200
max_depth: 20
min_samples_split: 2
```

This configuration was chosen after comparing multiple MLflow runs. On the initial phase-1 training set it reached `accuracy = 0.6840`, which was the best local RandomForest result found during the lab. After adding the phase-2 data in the continuous training step, the same configuration reached `accuracy = 0.7540` and `f1_score = 0.7534`, passing the CI/CD eval gate.

## Pipeline Summary

The completed pipeline runs on GitHub Actions with four jobs: Unit Test, Train, Eval, and Deploy. The Train job pulls versioned data from GCS using DVC, trains the model, writes `outputs/metrics.json`, and uploads `models/model.pkl` to `gs://testingai20k12/models/latest/model.pkl`. The Eval job blocks deployment when accuracy is below `0.70`. The Deploy job restarts the FastAPI service on the GCE VM so it downloads and serves the latest model.

## Issues And Fixes

The first pipeline run failed at the Eval job because the phase-1 model reached only `accuracy = 0.6840`, below the required `0.70` threshold. This was expected behavior from the quality gate. I then completed the continuous training step by appending `train_phase2.csv` into `train_phase1.csv`, updating the DVC pointer, pushing the new data to GCS, and pushing the data commit to GitHub. The second pipeline run passed all jobs and deployed successfully.

The deployed API was verified with:

```text
GET /health -> {"status":"ok"}
POST /predict -> {"prediction":0,"label":"thap"}
```
