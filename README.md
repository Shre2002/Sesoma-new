# SESOMA: Synthetic Multimodal Exercise Sensor Dataset Generator

A synthetic multimodal dataset generator for human movement. SESOMA simulates coherent radar (A121-style) and multi-zone time-of-flight (VL53L8CH-style) sensors, then labels each motion with correctness, wrong-form variants, fatigue dynamics, and patient-specific metadata.

## Project Goal

**Phase 1 (Current):**
1. Use simulation to generate a synthetic dataset that represents real sensor boards.
2. Start with one simple exercise.
3. Train an ML model on the synthetic data.
4. Test/adapt the model on real board data.

## Quick Links

- **New to SESOMA?** Start with [QUICKSTART.md](QUICKSTART.md)
- **Architecture and design:** See [docs/README.md](docs/README.md)
- **ML training:** See [docs/ml/README.md](docs/ml/README.md)
- **Real board integration:** See [docs/real_tof_v0_3_0/README.md](docs/real_tof_v0_3_0/README.md)
- **Generate a dataset:** See [QUICKSTART.md](QUICKSTART.md) or [docs/OVERVIEW.md](docs/OVERVIEW.md)

## Repository Structure

```
SESOMA_CODEX_01/
├── README.md                    # This file
├── QUICKSTART.md                # Get started in 5 minutes
├── simgen/                      # Simulation & dataset generation (79 Python files)
│   ├── generate.py              # CLI entrypoint
│   ├── dataset/                 # Dataset generation pipeline
│   ├── motion/                  # Motion generation & kinematics
│   ├── radar_a121/              # Coherent radar simulator
│   ├── tof_vl53l8ch/            # ToF depth sensor simulator
│   ├── patient/                 # Patient profiles & cohorts
│   ├── scene/                   # Geometry, materials, timing
│   ├── labels/                  # Exercise registry & feature extraction
│   ├── realboard/               # Real board integration
│   ├── fusion/                  # Sensor fusion utilities
│   ├── validation/              # Validation tools
│   ├── utils/                   # Utilities
│   └── tests/                   # Unit tests
├── ml/                          # ML model training & evaluation (40 Python files)
│   ├── train/                   # Training pipeline (train_hmtt.py)
│   ├── eval/                    # Evaluation (home policy, calibration)
│   ├── models/                  # Model architectures (HMTT)
│   ├── labels/                  # Label processing
│   ├── utils/                   # ML utilities
│   ├── data/                    # Data loading
│   ├── configs/                 # ML training configs
│   └── tests/                   # Unit tests
├── configs/                     # Dataset generation YAML configs (32 files)
│   └── README.md                # Config documentation (see configs/README.md)
├── scripts/                     # Analysis & utility scripts (31 files)
│   ├── generate_*.py            # Data generation scripts
│   ├── audit_*.py               # Audit/validation scripts
│   ├── analyze_*.py             # Analysis scripts
│   ├── diag_*.py                # Diagnostic scripts
│   ├── figures/                 # Figure generation
│   ├── thesis_figures/          # Thesis figure generation
│   └── README.md                # Script documentation (see scripts/README.md)
├── docs/                        # Technical documentation (78 files)
│   ├── README.md                # Doc index & reading order
│   ├── ARCHIVE/                 # Historical & experimental docs
│   ├── real_tof_v0_3_0/         # Real board integration docs
│   └── ml/                      # ML-specific documentation
├── data/                        # Generated datasets (empty - output directory)
├── runs/                        # ML training outputs (empty - output directory)
├── real_ToF/                    # Real board sensor captures (empty - output directory)
├── figures/                     # Generated figures (empty - output directory)
└── labels/                      # Label exports (empty - output directory)
```

## Key Modules

### simgen: Simulation & Dataset Generation
- **Purpose:** Generate synthetic multimodal sensor data with labeled motion correctness
- **Entrypoint:** `python -m simgen.generate --config <config.yaml> --out <output_dir>`
- **Output:** Zarr/HDF5 datasets with radar IQ, ToF depth maps, kinematic signals, and rich labels
- **Tests:** `simgen/tests/`

### ml: ML Training & Evaluation
- **Purpose:** Train HMTT model on synthetic datasets, evaluate on real board data
- **Entrypoint:** `python -m ml.train.train_hmtt --config <ml_config.yaml>`
- **Output:** Trained models, checkpoints, evaluation reports
- **Tests:** `ml/tests/`

### Real Board Integration
- **Capture:** Collect sensor data from real hardware
- **Inference:** Run model on real board data
- **Validation:** Evaluate synthetic→real domain adaptation
- **Documentation:** `docs/real_tof_v0_3_0/`

## Dataset Generation Pipeline

1. Load YAML config with motion, sensor, patient, and output specifications
2. Sample patient profiles (explicit or cohort-based)
3. Generate canonical motion sequences
4. Apply variations (timing, depth, stance, wrong-form, fatigue)
5. Simulate radar and ToF sensor signals
6. Compute correctness labels and scores
7. Write output to Zarr or HDF5

See [docs/OVERVIEW.md](docs/OVERVIEW.md) and [docs/PIPELINE.md](docs/PIPELINE.md) for details.

## ML Training Pipeline

1. Load synthetic dataset from generated Zarr/HDF5
2. Prepare data loaders with patient/motion stratification
3. Initialize HMTT model
4. Train with multi-task losses (correctness, wrong-form, family, fatigue)
5. Calibrate home policy thresholds
6. Evaluate on held-out synthetic and real board data

See [docs/ml/README.md](docs/ml/README.md) for details.

## Configuration

Dataset generation is driven by YAML configs in `configs/`:
- **Production configs:** `all_exercises_patient_cohort_train*.yaml`
- **Example configs:** `physio_supine_*.yaml`, `multisensor_*.yaml`
- **Smoke test configs:** `*_smoke*.yaml`

See [configs/README.md](configs/README.md) for details on each config.

## Scripts

Utility and analysis scripts in `scripts/`:
- Generate, audit, analyze, and visualize datasets
- Extract and validate real board results
- Create figures for papers/presentations

See [scripts/README.md](scripts/README.md) for details on each script.

## Documentation

Comprehensive technical documentation in `docs/`:
- System overview, architecture, and design
- Simulation pipeline and sensor models
- Dataset format and labeling
- ML model architecture and training
- Real board integration and deployment
- Validation and reproducibility

Start with [docs/README.md](docs/README.md) for the recommended reading order.

## Installation & Setup

See [QUICKSTART.md](QUICKSTART.md).

## Key References

- **System Architecture:** [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)
- **Data Format:** [docs/DATA_FORMAT.md](docs/DATA_FORMAT.md)
- **Sensor Models:** [docs/SENSOR_MODELS.md](docs/SENSOR_MODELS.md)
- **Motion Models:** [docs/MOTION_MODELS.md](docs/MOTION_MODELS.md)
- **Patient Modeling:** [docs/PATIENT_MODELING.md](docs/PATIENT_MODELING.md)
- **Scoring & Labels:** [docs/SCORING_AND_LABELS.md](docs/SCORING_AND_LABELS.md)
- **ML Architecture:** [docs/ml/model.md](docs/ml/model.md)
- **Real Board Status:** [docs/real_tof_v0_3_0/README.md](docs/real_tof_v0_3_0/README.md)

## Contributing

See [docs/EXTENDING.md](docs/EXTENDING.md) for guidance on extending simgen with new:
- Exercise types
- Sensor modalities
- Patient cohorts
- Scoring methods

## Tests

Run tests with pytest:

```bash
# All tests
pytest simgen/tests/ ml/tests/

# Specific module
pytest simgen/tests/test_dataset_shapes.py

# With coverage
pytest --cov=simgen --cov=ml simgen/tests/ ml/tests/
```

## Known Limitations

See [docs/LIMITATIONS.md](docs/LIMITATIONS.md).

## Real Board Integration Status

See [docs/real_tof_v0_3_0/README.md](docs/real_tof_v0_3_0/README.md) for current system status, capture pipeline, and next steps.

## Archived Documentation

Historical and experimental documentation is preserved in [docs/ARCHIVE/](docs/ARCHIVE/). See [docs/ARCHIVE/README.md](docs/ARCHIVE/README.md) for index.

---

**Last Updated:** May 12, 2026  
**Repository:** SESOMA_CODEX_01
