# Migration validation

Validated on 2026-09-14. This report separates package checks from experiment reproduction.

| Check | Result |
|---|---|
| CPU unit suite | 24 tests passed |
| Public command backends | All 15 command help entries loaded without models or datasets |
| Training/evaluation recipes | All six TOML recipes accepted by their backend parsers |
| Training objective smoke test | Selected-answer CE matches the causal token shift; ignored positions receive no logit gradient |
| Workspace smoke test | Expected tensor shapes, normalized attention, and finite nonzero gradients |
| Structural metric smoke test | Kabsch alignment and lDDT are invariant to a rigid transform on synthetic coordinates |
| Synthetic FoldingCorpus examples | Both displayed contact answers match executable labeling rules |
| General-10 aggregation | Three-seed means/SD checked; missing, duplicate, unknown, and invalid scores rejected |
| Python syntax | Package, tests, and tools compile successfully |
| Migration provenance | All 23 migrated module hashes match the recorded snapshot |
| Source/wheel build | Both distribution formats built successfully |
| Wheel import and CLI | Installed without dependencies into a separate temporary environment; import, help, and doctor work outside the checkout |
| Publication-content scanner | No flagged credentials, private paths, network addresses, or unreviewed binary resources in the publication tree |

CPU backend tests used the existing research dependencies listed in [installation](installation.md), with the new package on the import path. The separate wheel check did not install tensor dependencies: it verifies packaging and the lightweight CLI, not a fresh installation of the complete GPU stack.

The strict release check deliberately fails while the project LICENSE is pending. No new training, model inference, dataset-wide evaluation, remote publication, or model/data download was performed during this migration. A fresh-install GPU smoke test and independent end-to-end reproduction remain on the [release checklist](release_checklist.md).

## Repeat local checks

```bash
python -m unittest discover -s tests -v
python -m compileall -q src tests tools
python tools/check_release.py
```

For a publication build, install the `dev` extra and run `python -m build`. The source archive includes recipes, documentation, examples, tests, and the release checker. The wheel contains the Python package; use a checkout or source archive for the recipes and reproduction guide.
