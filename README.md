[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004850-blue)](https://doi.org/10.82901/nemar.on004850)

ODE dataset

This is a placeholder dataset.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 1 error + 47 warnings to 0 errors + 37 warnings. None of the raw `.set`/`.fdt` files were modified — every change is to a text sidecar.

**Event table HED dictionary (`task-nback_events.json`)**
- Added a top-level `sample` column definition. The events table has a `sample` column (integer sample index) that was not described, so the validator could not check it; the new entry documents what the column holds.
- Dropped the `value.HED` entries for event codes `"1"` and `"307"`. This dataset shares a defect with the rest of the STRONG cohort: `sub-001_task-nback_events.tsv` contains 197 onsets where two rows share the same `onset` (BIDS allows duplicate onsets, and the HED validator merges all HED annotations for rows that share an onset into one combined string per time bucket). The HED dictionary assigned the tag `"Task"` to most codes, so any onset where two codes both mapped to `"Task"` produced a `"Task,Task"` merge that the validator flagged as a duplicate-tag error. Code `"1"` fires 195 times and is always paired with `"1103"` (×120) or `"1113"` (×75), never alone; code `"307"` fires once, paired with `"2096"`. Removing just those two HED entries breaks every `"Task,Task"` collision without touching any lone event. `value.Levels` is left unchanged so all 107 codes remain documented, and the events.tsv itself is untouched.

**Channel table (`sub-001/eeg/sub-001_task-nback_channels.tsv`)**
- All 64 rows had `type=n/a` and `units=n/a`, which the validator rejects for EEG channels. The channel names (Fp1, AF7, F1, …) are standard 10-10 scalp electrodes, so `type` was set to `EEG` and `units` to `uV` (the units EEGLAB writes the data in). Channel names and order are unchanged.

**Recording sidecar (`sub-001/eeg/sub-001_task-nback_eeg.json`)**
- Added `MISCChannelCount: 0` and `TriggerChannelCount: 0` (there are no miscellaneous or trigger channels in this recording, so the counts are zero rather than missing).
- Added `EEGPlacementScheme: "10-10"` because the channel names match the 10-10 system. All other keys were left as published.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from the original value to `1.11.1` (the version the current validator checks against).
- `ReferencesAndLinks` was `[""]`; the empty-string element is not a valid reference, so the list was emptied to `[]`.
- `GeneratedBy` was left absent, exactly as the source published it — nothing was added there.

**Remaining warnings (37) — left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that isn't in the dataset (for example: `Manufacturer`, `ManufacturersModelName`, `SoftwareVersions`, `DeviceSerialNumber`, `TaskDescription`, `Instructions`, `CogAtlasID`, `CogPOID`, `InstitutionName`, `InstitutionAddress`, `InstitutionalDepartmentName`, `CapManufacturer`, `CapManufacturersModelName`, `GroundElectrode`, `HeadCircumference`, `HardwareFilters`, `SubjectArtefactDescription`, `StimulusPresentation`, and `GeneratedBy` on the dataset description). They were left blank rather than filled with guesses. One remaining HED warning notes that value `1` still appears in the events file without a matching HED entry; this is the intentional consequence of dropping the colliding HED entry above.