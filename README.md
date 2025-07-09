# 🎹 Piano Performance Evaluation Dataset

This dataset is built to support the analysis and evaluation of expressive piano performances for five pieces by five performers. It also contains perceptual evaluations provided by four human raters and one rule-based model (Hand_Craft).

The focus of this repository is to provide structured access to:
- 🎼 Target reference data (aligned scores)
- 🎧 Performance data from five pianists
- 📝 Evaluation data from four human raters and the Hand_Craft model

---

## 📁 Folder Structure

```text
data/
├── ground_truth/                      # Target midi, mp3
│   ├── Bach_Prelude/
│   ├── Beethoven_Sonata/
│   ├── Chopin_Nocturne/
│   ├── Chopin_Prelude/
│   └── Mozart_Sonata/
│
├── performances/                     # Performance recordings from five pianists
│   ├── Pianist_1/
│   ├── Pianist_2/
│   ├── Pianist_3/
│   ├── Pianist_4/
│   └── Pianist_5/
│
├── evaluations/                      # Evaluation scores from human raters and model
│   ├── raters/                       # Four human raters (Rater 1, Rater 2, Rater 3, Rater 4)
│   │   ├── Rater_1/
│   │   │   ├── Pianist_1/
│   │   │   ├── Pianist_2/
│   │   │   ├── Pianist_3/
│   │   │   ├── Pianist_4/
│   │   │   └── Pianist_5/
│   │   ├── Rater_2/
│   │   ├── Rater_3/
│   │   └── Rater_4/
│   │
│   └── hand_craft/                   # Model-generated scores
│       ├── Pianist_1/
│       ├── Pianist_2/
│       ├── Pianist_3/
│       ├── Pianist_4/
│       └── Pianist_5/
```

---

## 📦 Dataset Composition

- **Pieces Included**: Works by Bach_Prelude, Beethoven_Sonata, Chopin_Nocturne, Chopin_Prelude, Mozart_Sonata
- **Performers**: Pianist 1, Pianist 2, Pianist 3, Pianist 4, Pianist 5
- **Human Raters**: Rater 1, Rater 2, Rater 3, Rater 4
- **Model**: hand_craft (rule-based evaluation)

Each evaluated piece is annotated under the following categories:
- `note`, `dynamic`, `pedal`
- Performance quality tags: `perfect1`, `perfect2`, `bad1`, `bad2`

File naming format:
```
[composer]_[category]_[performance_quality]_score.csv
```
Example:
```
bach_note_perfect1_score.csv
chopin_dynamic_bad2_score.csv
```

## 📄 Feature Extraction from MIDI
To analyze and evaluate the performances, musical elements are extracted from the MIDI files. This process converts raw MIDI events into a structured format, making them suitable for computational analysis and comparison. The feature extraction process is as follows:

### The Explanation of Features 
| Feature | Description |
| :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- |
| **Time (secRep)** | Represents the event time, which has been converted from MIDI ticks to absolute **seconds** for synchronization. |
| **Note** | The pitch of the played note, expressed as a **MIDI Note Number (0-127)** and extracted from `note_on`/`note_off` messages. |
| **Velocity** | The intensity of a key press (0-127) from `note_on` events. It acts as a **core intermediate value used to calculate the `dynamic` and `accent` features**. |
| **Dynamic** | The musical dynamic, determined by calculating the **average velocity** within a time segment and classifying it into one of **8 predefined levels (ppp ~ fff)**. |
| **Accent** | Indicates the presence of an accent, marked as **'1'** if the average velocity is 76 or higher and **at least 1.2x greater** than the previous segment, and '0' otherwise. |
| **Count** | The number of concurrently pressed keys in a short time, used to determine the **density of the polyphonic texture**. |
| **Pedal** | The binarized state of the sustain pedal (CC #64), where a value > 64 is treated as **'1' (On)** and a value <= 64 is treated as **'0' (Off)**. |

### Library Selection
It is crucial for extracting meaningful features from MIDI files to analyze and evaluate piano performance. Thus, the Mido Python library was utilized for MIDI data extraction in our study. While more comprehensive libraries like Music21 exist, Mido was selected for its simplicity and directness, as it sufficiently handles the project's requirements for processing MIDI messages. In this part, it involves a comprehensive comparison of two libraries as follows:
  
| Library | Pros | Cons | Features |
| :--- | :--- | :--- | :--- |
| **Mido** | Easy to use, suitable for beginners. | Supports only MIDI data. | - Handles input and output of MIDI files.<br>- Processes various message types.<br>- Designed for easy accessibility. |
| **Music21** | Supports a wide range of musical tasks such as music theory, analysis, and score generation. | Can be challenging for beginners. | - Supports various music data formats, not just MIDI.<br>- Enables in-depth analysis of musical elements and score representation. |

### Feature Extraction Algorithm
The feature extraction algorithm follows these steps:
1. **Data Loading & Parsing**
  - The process begins by loading the Test MIDI file (the actual performance) and the Target MIDI file (the reference performance) as input.
  - The file structure and meta-information are parsed to organize the data for subsequent analysis.
2. **Precheck**
  - This stage ensures the validity and comparability of the Test and Target MIDI files.
  - It compares the total track length, the name of the main performance track (e.g., 'Piano'), and the total performance duration between the two files.
  - If significant discrepancies are found, the data pair is excluded from the analysis to ensure the consistency of extracted features and improve model reliability.
3. **Feature Extraction & Structuring**
  - **Time Synchronization**: Tempo information is extracted from meta-messages to convert all MIDI events to an absolute time standard (in seconds), aligning both files on the same timeline.
  - **Core Feature Extraction**: Specific performance features are extracted from channel messages as follows:
    - note, velocity: The note number and its corresponding velocity are directly extracted from note_on/note_off messages.
    - dynamic: The average of velocity values within a time segment is calculated and then mapped to one of eight predefined dynamic ranges (ppp, pp, p, mp, mf, f, ff, fff).
    - accent: An accent is marked as '1' only if the current segment's average velocity is 76 or higher and is at least 1.2 times greater than the previous segment's average velocity. Otherwise, it is marked as '0'.
    - count: The number of concurrently pressed keys is calculated to determine the density of the musical texture.
    - pedal: Sustain pedal (CC #64) usage is extracted. The value is binarized: '1' (On) if the pedal value is greater than 64, and '0' (Off) otherwise.
  - **Data Structuring**: All extracted features are segmented into fixed 0.1-second intervals. Each segment is converted into a feature vector representing the performance information within that timeframe. This creates a time-series dataset optimized for transformer-based evaluation models.
