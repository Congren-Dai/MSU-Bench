# MSU-Bench: Musical Score Understanding Benchmark

[![arXiv](https://img.shields.io/badge/arXiv-2511.20697-b31b1b.svg)](https://arxiv.org/abs/2511.20697)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Dataset-yellow.svg)](https://huggingface.co/datasets/Krinos/MSU-Bench)

Last updated: 14/07/2026

**Evaluating Large Language Models' Comprehension of Complete Musical Scores**

![ABC](figures/abc.png)
---

## News

- We are glad to share that this work has been accepted to **ACL 2026 Main Conference** 🎉!

## Overview

MSU-Bench is a human-curated benchmark for evaluating the musical score understanding capabilities of Large Language Models (LLMs) and Vision-Language Models (VLMs). It supports multimodal evaluation through both textual (ABC notation) and visual (PDF/image) inputs.

**Key Statistics:**
- 150 complete musical scores
- 1,800 generative question-answer pairs
- 4 hierarchical difficulty levels
- 12 questions per score (3 per level)

![Data](figures/data.png)

## Difficulty Levels

| Level | Focus | Examples |
|-------|-------|----------|
| **Level 1** - Onset Information | Metadata at the beginning of a score | Composer, title, key, time signature, tempo, instrumentation, anacrusis |
| **Level 2** - Notation & Note | Local bar-level notation details | Pitch range, accidentals, dynamics, articulations, ornaments, tempo changes |
| **Level 3** - Chord & Harmony | Harmonic structures and progressions | Chord qualities, inversions, cadences, modulations, pedal points |
| **Level 4** - Texture & Form | Large-scale structural analysis | Melodic motifs, thematic organisation, texture types, formal design |

## Dataset Structure

Each sample contains:
- **ABC notation** — text-based symbolic music representation
- **PDF** — the original rendered score
- **Page images** (PNG) — individual pages of the PDF score
- **Questions** — 12 questions (3 per difficulty level) with reference answers

### Modalities for Evaluation

| Modality | Input | Target Models |
|----------|-------|---------------|
| Textual QA | ABC notation + question | LLMs |
| Visual QA | PDF/images + question | VLMs |

## Repertoire

The benchmark covers 150 scores from the Western art music canon, spanning:
- **Periods:** Baroque, Classical, Romantic, Impressionism, 20th Century
- **Composers:** Bach, Beethoven, Chopin, Brahms, Debussy, Liszt, Schubert, Mozart, Mussorgsky, Grieg, and others
- **Genres:** Sonatas, character pieces, fugues, waltzes, nocturnes, etudes, rhapsodies, symphonies, concertos, art songs

## Usage

The dataset is hosted on HuggingFace with two configurations:

- **`flat`** (default) — 1,800 rows, one question per row (text only, lightweight)
- **`nested`** — 150 rows, one score per row with all 12 questions, PDF, and page images

### Loading the Flat Config (default)

```python
from datasets import load_dataset

ds = load_dataset("Krinos/MSU-Bench", split="test")
print(len(ds))  # 1800

sample = ds[0]
print(sample["song_id"])
print(f"Level {sample['level']}: {sample['question']} -> {sample['answer']}")
```

### Loading the Nested Config

```python
ds_nested = load_dataset("Krinos/MSU-Bench", "nested", split="test")
print(len(ds_nested))  # 150

sample = ds_nested[0]
for lvl, q, a in zip(
    sample["questions"]["level"],
    sample["questions"]["question"],
    sample["questions"]["answer"],
):
    print(f"  L{lvl}: {q} -> {a}")
```

### Visual QA with Page Images

```python
ds_nested = load_dataset("Krinos/MSU-Bench", "nested", split="test")
sample = ds_nested[0]
for i, img in enumerate(sample["images"]):
    img.save(f"page_{i}.png")
```

### Joining Flat + Nested for Visual Evaluation

```python
ds_flat = load_dataset("Krinos/MSU-Bench", split="test")
ds_nested = load_dataset("Krinos/MSU-Bench", "nested", split="test")

# Build a lookup from song_id to images
images_lookup = {row["song_id"]: row["images"] for row in ds_nested}

# Get images for a flat row
sample = ds_flat[0]
images = images_lookup[sample["song_id"]]
```


## Citation

```bibtex
@article{dai2025msubench,
  title={Musical Score Understanding Benchmark: Evaluating Large Language Models' Comprehension of Complete Musical Scores},
  author={Dai, Congren and Yang, Yue and Li, Krinos and Zhou, Huichi and Liang, Shijie and Zhang, Bo and Liu, Enyang and Jin, Ge and An, Hongran and Zhang, Haosen and Jing, Peiyuan and Lee, Kinhei and Zhang, Zhenxuan and Li, Xiaobing and Sun, Maosong},
  journal={arXiv preprint arXiv:2511.20697},
  year={2025}
}
```


## Acknowledgements

This work is supported by the Advanced Discipline Construction Project of Beijing Universities, the Special Programme of National Natural Science Foundation of China (Grant No. T2341003), and the Major Programme of National Social Science Fund of China (Grant No. 21ZD19).
