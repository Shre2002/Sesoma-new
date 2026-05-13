# Dataset Generation Configuration Reference

This directory contains YAML configuration files for the `simgen.generate` CLI. Configs specify what motions to generate, sensor parameters, patient profiles, and output settings.

## Quick Start

```bash
# Generate a small dataset (5-10 minutes)
python -m simgen.generate \
  --config configs/physio_supine_200_smoke.yaml \
  --out data/test_dataset

# Generate production dataset (1-3 hours)
python -m simgen.generate \
  --config configs/all_exercises_patient_cohort_train.yaml \
  --out data/all_exercises_train \
  --workers 60
```

## Configuration Categories

### Production Configs (Current Main)

| Config | Purpose | Exercises | Samples | Notes |
|--------|---------|-----------|---------|-------|
| `all_exercises_patient_cohort_train.yaml` | **MAIN PRODUCTION** — All 23 exercises with patient cohort sampling | 23 exercises | 1000 per exercise | 23,000 total samples; ~2-3 hours to generate |
| `all_exercises_patient_cohort_train_fast.yaml` | Fast version of main production for testing (2 samples per exercise) | 23 exercises | 2 per exercise | 46 total samples; ~1 minute; for smoke testing pipeline |

### Floor-Based Stage 1 Configs (Real Board Pilot)

Focus on 5 "floor standing" exercises for real board integration testing.

| Config | Size | Purpose | Notes |
|--------|------|---------|-------|
| `all_exercises_patient_cohort_train_tof4_floor_stage1.yaml` | 5000 | Full stage 1 dataset (5 exercises × 1000 samples) | Baseline for real board adaptation |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500.yaml` | 2500 | Medium size stage 1 (5 exercises × 500 samples) | Faster training experiments |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_hardened_v1.yaml` | 2500 | Hardened variant 1 (experimental sensor noise/robustness) | Sensitivity analysis |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_hardened_v2.yaml` | 2500 | Hardened variant 2 (different robustness parameters) | Sensitivity analysis |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_hardened_v3_pose.yaml` | 2500 | Hardened variant 3, pose-only output | Ablation study (no radar) |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_pose_only_v1.yaml` | 2500 | Pose-only (kinematic signals, no sensor data) | Ablation study |
| `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_pose_only_v2.yaml` | 2500 | Pose-only variant 2 | Ablation study |

**Floor exercises:** bodyweight_squat, sit_down, sit_to_stand, squat, stand_up

### Example Configs (Documentation & Prototyping)

| Config | Purpose | Exercises | Samples | Notes |
|--------|---------|-----------|---------|-------|
| `physio_supine_200_smoke.yaml` | **QUICK START** — Smallest test config | 6 supine exercises | 1 each | 6 total; <1 minute; good for testing pipeline |
| `physio_supine_200_smoke_v2.yaml` | Smoke test variant 2 | 6 supine exercises | 1 each | Identical to v1; unclear purpose |
| `physio_supine_200_smoke_v3.yaml` | Smoke test variant 3 | 6 supine exercises | 1 each | Identical to v1; unclear purpose |
| `physio_supine_200_smoke_v4.yaml` | Smoke test variant 4 | 6 supine exercises | 1 each | Identical to v1; unclear purpose |
| `physio_supine_200.yaml` | Medium supine dataset | 6 supine exercises | 34 each | ~200 samples; ~10 minutes |
| `physio_supine_patient_smoke.yaml` | Supine + patient cohort smoke test | 4 supine exercises | 2 each | 8 samples; <1 minute |
| `physio_supine_patient_cohort_200.yaml` | Supine + patient cohort | 4 supine exercises | 50 each | ~200 samples; ~15 minutes |
| `physio_supine_fatigue_example.yaml` | Supine with fatigue dynamics enabled | 4 supine exercises | 15 each | ~60 samples; demonstrates fatigue features |
| `physio_200_example.yaml` | Standing physiotherapy exercises | 6 standing exercises | ~33 each | ~200 samples; ~10 minutes |
| `multisensor_example.yaml` | Multi-sensor setup (radar + ToF) | 3 exercises | 50 each | ~150 samples; demonstrates dual-sensor |
| `multisensor_envelope_example.yaml` | Multi-sensor with envelope scoring | 2 exercises | 40 each | ~80 samples; demonstrates envelope features |

**Supine exercises:** bridge_iso_supine, bridge_dyn_supine, abs_straight_upper_iso_supine, abs_straight_upper_dyn_supine, abs_oblique_upper_iso_supine_left, abs_oblique_upper_iso_supine_right

**Standing exercises:** sit_to_stand, bodyweight_squat, step_up, lunge_or_split_squat, hip_hinge, single_leg_balance

## Config Structure

Each YAML config specifies:

```yaml
# Motions to generate
motions:
  samples_per_motion:  # Dict[motion_id, num_samples]
    sit_to_stand: 100
    squat: 100

# Patient profiles (explicit or sampled)
patients:
  mode: cohort  # or 'explicit'
  # ... patient parameters

# Sensor configurations
radar:
  enabled: true
  # ... radar parameters

tof:
  enabled: true
  # ... ToF parameters

# Scoring and correctness
scoring:
  # ... scoring parameters

# Output format
output:
  format: zarr  # or 'hdf5'
  # ... output parameters
```

See [docs/CONFIG_REFERENCE.md](../docs/CONFIG_REFERENCE.md) for complete schema.

## Config Naming Conventions

Configs follow a naming pattern:

```
[scope]_[variant]_[size]_[modifier1]_[modifier2].yaml
```

| Component | Examples | Meaning |
|-----------|----------|---------|
| `scope` | `physio_supine`, `all_exercises`, `multisensor` | Which exercises/setup |
| `variant` | `patient_cohort`, `patient`, (none) | Patient sampling strategy |
| `size` | `200`, `smoke`, `medium500`, `tof4_floor_stage1` | Dataset size/name |
| `modifier` | `hardened_v1`, `pose_only`, `fatigue`, `fast` | Special modifications |

Examples:
- `physio_supine_200_smoke.yaml` — Supine exercises, 200-level, smoke test
- `all_exercises_patient_cohort_train.yaml` — All exercises, patient cohort, training
- `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_hardened_v1.yaml` — Floor stage 1, medium (500 samples), hardened variant 1

## Recommended Usage

**For getting started:**
```bash
python -m simgen.generate --config configs/physio_supine_200_smoke.yaml --out data/test
```

**For testing/development:**
```bash
python -m simgen.generate --config configs/all_exercises_patient_cohort_train_fast.yaml --out data/dev
```

**For production training:**
```bash
python -m simgen.generate --config configs/all_exercises_patient_cohort_train.yaml --out data/training --workers 60
```

**For real board adaptation testing:**
```bash
python -m simgen.generate --config configs/all_exercises_patient_cohort_train_tof4_floor_stage1_medium500.yaml --out data/floor_stage1
```

## Understanding Config Versions

### Smoke Variants (v1-v4)
`physio_supine_200_smoke_v*.yaml` files have nearly identical content. Reason for multiple versions is unclear; **TODO: Audit and consolidate if they're truly identical.**

### Hardened Variants (v1-v3)
`*_hardened_vX.yaml` configs test robustness with different sensor noise/physics parameters. Used for sensitivity analysis and model hardening. See [docs/ARCHIVE/](../docs/ARCHIVE/) for related experiment reports.

### Pose-Only Variants
`*_pose_only*.yaml` generate kinematic signals only (no sensor data). Used for ablation studies to isolate sensor vs. motion contributions.

## Creating Custom Configs

To create a new config:

1. Copy an existing config as a template
2. Modify the exercises and sample counts in `motions.samples_per_motion`
3. Update sensor/patient settings as needed
4. Test with a small sample count first:

```bash
# Test config (1 sample per motion)
cp configs/physio_200_example.yaml configs/my_test.yaml
# Edit my_test.yaml: set all samples to 1
python -m simgen.generate --config configs/my_test.yaml --out data/test_my_config
```

See [docs/CONFIG_REFERENCE.md](../docs/CONFIG_REFERENCE.md) for all available options.

## Config File Summary (32 total)

### By Purpose
- **Production configs:** 2 files
- **Floor stage 1 configs:** 7 files
- **Example/documentation configs:** 11 files
- **Smoke test configs:** 4 variants of same config
- **Unclear/experimental status:** ~8 files (see below)

### Files with Unclear Status

These configs may be outdated or experimental. Audit before using for critical work:

- `all_exercises_patient_cohort_train_tof4_floor_stage1_medium500_hardened_v*.yaml` — Hardened variants; check if still actively used
- `*_pose_only_v*.yaml` — Ablation studies; verify current relevance

## Troubleshooting Config Issues

**"Config file not found"**
- Ensure you're in the repo root directory
- Use absolute path: `python -m simgen.generate --config /path/to/config.yaml`

**"Unknown exercise name"**
- Check YAML syntax and exercise names in `motions.samples_per_motion`
- Run: `python -c "from simgen.labels.exercises import EXERCISE_REGISTRY; print(list(EXERCISE_REGISTRY.keys()))"` to see valid exercises

**"Invalid patient config"**
- Check `patients.mode` is `explicit` or `cohort`
- Verify `patients` section matches schema in [docs/CONFIG_REFERENCE.md](../docs/CONFIG_REFERENCE.md)

For more help, see [docs/CONFIG_REFERENCE.md](../docs/CONFIG_REFERENCE.md) and [../QUICKSTART.md](../QUICKSTART.md).

---

**Last Updated:** May 12, 2026  
**Total Configs:** 32 YAML files  
**Status:** Production-ready with experimental variants for research
