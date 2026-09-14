# Fold2Reason

Protein-structure post-training for language models. Fold2Reason combines **FoldingCorpus** supervision with a shared spatial workspace and a frozen geometry decoder. General reasoning is evaluated with the adapted language model; structural readout uses the workspace and decoder.

This repository contains the Qwen3.5 training and evaluation pipeline, a FoldingCorpus-only Pure-LoRA baseline, deterministic data preparation, and General-10 score aggregation.

## Install

Use Python 3.12 and a CUDA-compatible PyTorch installation. The tested environment uses PyTorch 2.10.0. Transformers and PEFT are pinned to the source revisions recorded in this repository; see [installation](docs/installation.md).

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e '.[vision]'
fold2reason doctor
fold2reason --help
```

Install the matching PyTorch/torchvision CUDA wheels for your system before the editable install. Models and benchmark data are supplied separately. No credentials, model weights, protein datasets, or proprietary fonts are bundled.

## Quick start

After preparing a local Qwen3.5-9B model, a workspace cache, and a frozen decoder:

```bash
torchrun --standalone --nproc_per_node=4 -m fold2reason train full \
  --config configs/train/full.toml \
  --model models/Qwen3.5-9B \
  --cache artifacts/cache/foldingcorpus.pt \
  --frozen-decoder-checkpoint artifacts/decoder/final \
  --output artifacts/runs/full/seed-20260729
```

The same command with `--dry-run` prints the resolved backend arguments without loading a model. CLI arguments override recipe values. Use `--seed 20260803` and `--seed 20260804` with separate output directories for the remaining seeds.

Start with the [end-to-end reproduction guide](docs/reproduction.md) for data preparation and decoder training. Use [evaluation](docs/evaluation.md) for the ten full benchmarks and [data formats](docs/data.md) for input schemas.

## Layout

```text
Fold2Reason/
├── src/fold2reason/
│   ├── data/          # FoldingCorpus, geometry and visual caches
│   ├── models/        # Language backbone, workspace and geometry heads
│   ├── losses/        # Structural objectives
│   ├── training/      # Frozen decoder, full method and Pure LoRA
│   ├── evaluation/    # Structural readout, text, image/video and FTB
│   └── analysis/      # Paired statistics and General-10 aggregation
├── configs/           # Small, editable training/evaluation recipes
├── examples/          # Synthetic data and score schemas
├── docs/              # Setup, reproduction, data and release notes
├── tests/             # CPU unit tests and packaging contracts
└── tools/             # Repository-wide release checks
```

## Tests

```bash
python -m unittest discover -s tests -v
python tools/check_release.py
```

Tests run on CPU. See the [validation report](docs/validation.md) for completed checks. Full GPU training and dataset-wide evaluation are separate reproduction steps.

## Release status

This is a source-release candidate. The [release checklist](docs/release_checklist.md) records validation and remaining publication decisions, including the project license and redistribution permissions. The repository is prepared locally; no remote repository has been published.

The public name is **FoldingCorpus**. Legacy tensor keys, checkpoint fields, and some internal class names retain `relation` for compatibility with existing checkpoints. Public commands accept `--loss-foldingcorpus` and `--corpus-variant`.

## Attribution

The implementation was reorganized from the research codebase. [Provenance](docs/provenance.json) records the source-to-package mapping and source hashes. Dependencies retain their own licenses; see [third-party resources](THIRD_PARTY_NOTICES.md). Citation metadata can be added when the paper's public title, author list, and identifier are finalized.
