[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004850-blue)](https://doi.org/10.82901/nemar.on004850)

ODE dataset

This is a placeholder dataset.

## NEMAR curation changes (2026-05-21)

BIDS validator: 1 error + 47 warnings → 0 errors + 36 warnings. Raw `.set`/`.fdt` binary payloads unchanged.

The 1 pre-curation `HED_ERROR:TAG_EXPRESSION_REPEATED` error is a real dataset annotation defect, shared across the entire STRONG cohort (on004849/50/52/53/54/55 all carry the byte-identical defect). Mechanism: every `sub-001_task-nback_events.tsv` has 197 onsets where two rows share an `onset` (BIDS allows duplicate onsets). The BIDS-HED validator merges all HED annotations for rows that share an `onset` into one combined HED string per time bucket. The HED dictionary in `task-nback_events.json` assigns the same tag (`"Task"`) to ~80 of the ~107 distinct event codes, so any time two rows with different codes that both map to `"Task"` share an `onset`, the merged HED string becomes `"Task,Task"` and the validator legitimately fires a duplicate-tag error. The 197 collisions split as (1, 1103) × 120, (1, 1113) × 75, (3, 55011) × 1, (2096, 307) × 1; 196 of those pairs share the same HED tag ("Task") and produce the error, while the (3, 55011) pair has two distinct HED tags and merges cleanly.

### `dataset_description.json`
- Added `DatasetType: "raw"`.
- Added `GeneratedBy: [{Name: "nemar-cli", Version: "0.8.8", CodeURL: "https://github.com/nemar-org/nemar-cli"}]`.
- `ReferencesAndLinks`: `[""]` → `[]` (an empty-string element is not a valid reference).

### `sub-001/eeg/sub-001_task-nback_channels.tsv`
- All 64 rows: `type=n/a` → `type=EEG`, `units=n/a` → `units=uV`. Channel names unchanged.

### `sub-001/eeg/sub-001_task-nback_eeg.json`
- Added `MISCChannelCount: 0`, `TriggerChannelCount: 0`, `EEGPlacementScheme: "10-10"` (channel names Fp1/AF7/F1/etc. are 10-10 labels). Other keys unchanged.

### `task-nback_events.json`
- Added top-level `sample` column definition.
- `value.HED`: dropped entries for codes `"1"` and `"307"` (105 of 107 entries preserved). Codes appear in `events.tsv` **only** in collision with another code already tagged `"Task"` — code `"1"` fires 195 times, always paired with `"1103"` (×120) or `"1113"` (×75), never alone; code `"307"` fires once, paired with `"2096"`. Dropping the two HED entries removes the `"Task,Task"` duplicate-tag merges without affecting any lone event. `value.Levels` is left unchanged (all 107 entries preserved) so the codes remain documented.