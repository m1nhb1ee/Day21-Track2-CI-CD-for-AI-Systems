# Bao Cao Lab MLOps

## Bo Sieu Tham So Da Chon

Mo hinh duoc chon la `RandomForestClassifier` voi bo sieu tham so:

```yaml
n_estimators: 200
max_depth: 20
min_samples_split: 2
```

Bo tham so nay duoc chon sau khi so sanh nhieu lan chay tren MLflow. Voi tap du lieu ban dau `train_phase1.csv`, mo hinh dat `accuracy = 0.6840`, la ket qua RandomForest tot nhat trong cac cau hinh da thu. Sau khi bo sung du lieu moi o Buoc 3, cung bo tham so nay dat `accuracy = 0.7540` va `f1_score = 0.7534`, vuot nguong kiem tra chat luong `0.70` cua pipeline.

## Tom Tat Pipeline

Pipeline CI/CD chay tren GitHub Actions gom bon job: Unit Test, Train, Eval va Deploy. Job Train dung DVC de pull du lieu da version tu Google Cloud Storage, huan luyen mo hinh, ghi ket qua vao `outputs/metrics.json`, va upload `models/model.pkl` len `gs://testingai20k12/models/latest/model.pkl`. Job Eval chan deploy neu `accuracy < 0.70`. Job Deploy SSH vao GCE VM va restart service FastAPI de tai va phuc vu model moi nhat.

## Kho Khan Va Cach Xu Ly

Lan chay pipeline dau tien dung o job Eval vi mo hinh huan luyen tren `train_phase1.csv` chi dat `accuracy = 0.6840`, thap hon nguong yeu cau `0.70`. Day la hanh vi dung cua eval gate. Sau do, toi thuc hien Buoc 3 bang cach ghep `train_phase2.csv` vao `train_phase1.csv`, cap nhat file con tro DVC, push du lieu moi len GCS truoc, roi push commit du lieu len GitHub. Lan chay pipeline tiep theo da qua ca bon job va deploy thanh cong.

API sau khi deploy da duoc kiem tra voi ket qua:

```text
GET /health -> {"status":"ok"}
POST /predict -> {"prediction":0,"label":"thap"}
```
