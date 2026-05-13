# QUICKSTART: Generate Your First Dataset

Get up and running with SESOMA in 5 minutes.

## Prerequisites

- Python 3.9+
- conda or pip
- ~2 GB disk space for a small dataset

## Installation

```bash
cd /path/to/SESOMA_CODEX_01

# Option 1: Using conda (recommended)
conda create -n SESOMA python=3.11
conda activate SESOMA
pip install -e .

# Option 2: Using pip
pip install -r requirements.txt
```

## 1. Generate a Small Synthetic Dataset

```bash
# Activate environment
conda activate SESOMA

# Generate a small dataset (smoke test)
python -m simgen.generate \
  --config configs/physio_supine_200_smoke.yaml \
  --out data/smoke_test \
  --workers 4

# Expected runtime: 2-5 minutes
# Output: data/smoke_test/
```

## 2. Inspect the Generated Dataset

```bash
# List generated files
ls -lh data/smoke_test/

# View dataset structure
python -c "
import zarr
ds = zarr.open('data/smoke_test/dataset.zarr', mode='r')
print('Dataset arrays:', list(ds.keys()))
print('Dataset metadata:', ds.attrs)
"
```

## 3. Train a Model (Optional)

```bash
# Prepare data for ML training
python -m ml.data.loader --dataset data/smoke_test/dataset.zarr --out data/smoke_test_ml

# Train HMTT model
python -m ml.train.train_hmtt \
  --config ml/configs/default.yaml \
  --data data/smoke_test_ml \
  --out runs/smoke_test_model

# Expected runtime: 10-30 minutes (GPU: 2-5 minutes)
```

## 4. Evaluate the Model (Optional)

```bash
# Run inference on test data
python -m ml.eval.home_policy \
  --model runs/smoke_test_model/checkpoint.pt \
  --data data/smoke_test_ml/test \
  --out runs/smoke_test_model/eval
```

## Next Steps

- **Understand the dataset:** [docs/OVERVIEW.md](docs/OVERVIEW.md)
- **Create custom configs:** [docs/CONFIG_REFERENCE.md](docs/CONFIG_REFERENCE.md)
- **Explore configs:** [configs/README.md](configs/README.md)
- **Learn about ML training:** [docs/ml/quickstart.md](docs/ml/quickstart.md)
- **Integrate with real board:** [docs/real_tof_v0_3_0/README.md](docs/real_tof_v0_3_0/README.md)

## Common Commands

```bash
# Generate production dataset (large)
python -m simgen.generate \
  --config configs/all_exercises_patient_cohort_train.yaml \
  --out data/all_exercises_train \
  --workers 60

# List all available configs
ls configs/*.yaml

# View a config
cat configs/physio_supine_200_smoke.yaml

# Run all tests
pytest simgen/tests/ ml/tests/

# Check if datasets are valid
python scripts/validate_sensor_payload.py data/smoke_test/dataset.zarr
```

## Troubleshooting

**Issue: "ModuleNotFoundError: No module named 'simgen'"**
- Solution: Install in editable mode: `pip install -e .`

**Issue: "No space left on device"**
- Solution: Use fewer workers or reduce dataset size in config

**Issue: "GPU out of memory"**
- Solution: Reduce batch size in ML config or use CPU

**Issue: Output directory not found**
- Solution: Create output directory: `mkdir -p data/smoke_test`

For more help, see [docs/ml/troubleshooting.md](docs/ml/troubleshooting.md).

## File Structure After Running

```
data/
└── smoke_test/
    ├── dataset.zarr/           # Generated sensor & label data
    ├── reports/
    │   ├── main_export_contract.json
    │   ├── wrong_type_contract/
    │   └── onset_audit/
    └── metadata/
        └── registry.json

runs/
└── smoke_test_model/           # (if ML training ran)
    ├── checkpoint.pt
    ├── config.yaml
    └── eval/                   # (if evaluation ran)
```

## Further Documentation

- Full architecture overview: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)
- Dataset generation pipeline: [docs/PIPELINE.md](docs/PIPELINE.md)
- Configuration reference: [docs/CONFIG_REFERENCE.md](docs/CONFIG_REFERENCE.md)
- Data format specification: [docs/DATA_FORMAT.md](docs/DATA_FORMAT.md)
- Real board integration: [docs/real_tof_v0_3_0/README.md](docs/real_tof_v0_3_0/README.md)

---

**Estimated time:** 5-30 minutes depending on dataset size and hardware.
