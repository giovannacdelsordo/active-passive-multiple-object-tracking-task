# AP-MOT: Active and Passive Multiple Object Tracking

PsychoPy implementation of the AP-MOT paradigm, an operationalization of the distinction between active and passive modes of sustained attention. The repository contains two versions of the task: a behavioral-only version and a pupillometry version designed for use with an SR Research EyeLink 1000 Plus eye-tracker.

## Requirements

- **PsychoPy 2026.1.3** (the scripts are tested with 2026.1.3 and use the modern `gui.Dlg` and ioHub APIs)
- A working Python 3 environment with the following modules available on the import path: `psychopy`, `MathUtils`, `MovingCircle` (these last two are local helper modules included in this repository)
- For the pupillometry version only:
  - An SR Research **EyeLink 1000 Plus** eye-tracker
  - SR Research `pylink` and the EyeLink Developer Kit installed on the host machine
  - PsychoPy ioHub properly configured for the EyeLink
  - The plugin "psychopy-eyetracker-sr-research" installed. 

## Repository layout

The repository is organized into two folders, one per task version:

```
behavioral/
    APMOT_behavioral.py
    libsmot_behavioral.py
    MathUtils.py
    MovingCircle.py
    amot.png
    pmot.png

pupillometry/
    APMOT_pup.py
    libsmot.py
    MathUtils.py
    MovingCircle.py
    amot.png
    pmot.png
```

**Important: all files within a given folder must be located in the same folder on the local computer for the task to run.** The main script imports the engine (`libsmot` or `libsmot_behavioral`) and the helper modules (`MathUtils`, `MovingCircle`) by relative name, and loads `amot.png` and `pmot.png` from the working directory at runtime. Do not split these files across subdirectories.

## The two versions

### Behavioral version (`behavioral/APMOT_behavioral.py`)

A purely behavioral implementation. No eye-tracker, no ioHub server, and no calibration. The task records accuracy, reaction time, and trial parameters to a CSV file.

### Pupillometry version (`pupillometry/APMOT_pup.py`)

Identical task structure, but with concurrent pupil and gaze recording via an **SR Research EyeLink 1000 Plus**. The script launches the ioHub server, runs the EyeLink calibration procedure at the start of the session, records to an EDF file on the EyeLink host PC, and sends trial-level messages (`TRIALID`, `fixation`, `experiment`, `interval`, `TRIAL_VAR ...`, `TRIAL_RESULT`) for downstream Data Viewer segmentation.

> **Participant number constraint (pupillometry version only):** the participant number entered in the start-up dialog must not exceed **3 digits**. The EDF filename is constructed as `A_<participant_number>` and the EyeLink host has a hard limit on EDF filename length. Longer participant numbers will cause the EDF file to fail to save.

## Instruction images

Each folder contains two PNG diagrams used inside the task instructions:

- `amot.png`: schematic of an A-MOT trial (balls appear, some turn red as targets, all balls turn blue and move, then stop for response)
- `pmot.png`: schematic of a P-MOT trial (balls appear and start moving, some briefly turn red as targets, then stop for response)

These images are displayed below the verbal task description before the practice trials of each block. They must be present in the same folder as the main script.

## Output

When the participant finishes the task, the script closes the data file and the experiment window. Output files are saved in the same folder as the script that produced them:

- **Behavioral version**: a single CSV file named `A_<participant_number>.csv`
- **Pupillometry version**: a CSV file named `A_<participant_number>.csv` plus an EDF file with the same base name (`A_<participant_number>.edf`) transferred from the EyeLink host PC at the end of the session

Both CSV files share the same columns:

| Column | Description |
|---|---|
| `age` | Participant age (from start-up dialog) |
| `gender` | Participant gender (lowercase) |
| `task` | `AMOT` or `PMOT` |
| `trialNumber` | Trial index within block |
| `difficulty` | Number of target balls (2 or 4) |
| `reactionTime` | Time from selection-phase onset to space-bar confirmation (seconds) |
| `correctAnswers` | Number of targets correctly selected |
| `wrongAnswers` | Number of non-targets selected |
| `conditionSuccess` | 1 if all targets correctly selected, 0 otherwise |
| `amot_duration` | Random movement duration for A-MOT trials (empty for P-MOT) |
| `pmot_duration` | Random initial movement duration for P-MOT trials (empty for A-MOT) |

## Running the task

From PsychoPy, open:
APMOT_behavioral.py for the behavioral version and APMOT_pup.py for the pupillometry version

A start-up dialog will request the participant number, age, and gender. The experiment begins immediately after these are submitted.

### Key bindings during the experiment

- `Space`: advance through instructions, confirm a selection, start a new trial
- `Escape`: quit the experiment at any time

## Task structure

Each session contains two blocks (A-MOT and P-MOT) presented in counterbalanced order across participants. Each block consists of 40 trials (20 with 2 targets and 20 with 4 targets) preceded by 2 practice trials. The difficulty order is shuffled independently for each block. This parameters are easily modifiable. 

## Citation

If using this task, please cite the following publication: 

Del Sordo, G.C., Papesh, M.H., Alonso Carillo, M., & Hout, M.C. (2026). Beyond vigilance: A dual-process perspective on visual sustained attention. Cognitive, Affective, & Behavioral Neuroscience. https://doi.org/10.3758/s13415-026-01458-6

