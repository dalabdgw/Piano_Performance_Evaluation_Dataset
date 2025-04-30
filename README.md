# 🎹 Piano Performance Evaluation Dataset

This dataset is built to support the analysis and evaluation of expressive piano performances for five pieces by five performers. It also contains perceptual evaluations provided by four human raters and one rule-based model (Hand_Craft).

The focus of this repository is to provide structured access to:
- 🎼 Target reference data (aligned scores)
- 🎧 Performance data from five human performers
- 📝 Evaluation data from four human raters and the Hand_Craft model

---

## 📁 Folder Structure

```text
data/
├── ground_truth/                      # Target midi, mp3
│   ├── bach/
│   ├── beethoven/
│   ├── chopin/
│   ├── chopin_prelude/
│   └── mozart/
│
├── performances/                     # Performance recordings from performers
│   ├── jeo/
│   ├── jungwook/
│   ├── jihyun/
│   ├── jinhee/
│   └── hyunsung/
│
├── evaluations/                      # Evaluation scores from human raters and model
│   ├── raters/                       # Four human raters (김진희, 박재오, 유현성, 이정욱)
│   │   ├── Jinhee_Kim/
│   │   │   ├── jeo/
│   │   │   ├── jungwook/
│   │   │   ├── jihyun/
│   │   │   ├── jinhee/
│   │   │   └── hyunsung/
│   │   ├── Jeo_Park/
│   │   ├── Hyunsung_Ryu/
│   │   └── Jungwook_Lee/
│   │
│   └── hand_craft/                   # Model-generated scores
│       ├── jeo/
│       ├── jungwook/
│       ├── jihyun/
│       ├── jinhee/
│       └── hyunsung/
```

---

## 📦 Dataset Composition

- **Pieces Included**: Works by Bach_Prelude, Beethoven_Sonata, Chopin_Nocturne, Chopin_Prelude, Mozart_Sonata
- **Performers**: jeo, jungwook, jihyun, jinhee, hyunsung
- **Human Raters**: 김진희 (Jinhee_Kim), 박재오 (Jeo_Park), 유현성 (Hyunsung_Ryu), 이정욱 (Jungwook_Lee)
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

