# Cantonese Content Safety Evaluator

A rubric and pipeline for evaluating LLM safety calibration on Cantonese-language prompts, with a focus on Hong Kong cultural context.

Built as a portfolio project demonstrating cross-lingual safety evaluation methodology.

---

## What This Is

Current multilingual safety benchmarks treat language as a translation problem — they check whether semantic meaning is preserved. They do not check whether **safety calibration** is preserved, and they do not evaluate **cultural register**.

This project evaluates Claude on 30 Cantonese prompts across four categories, scoring responses on:
- Cultural accuracy (HK-specific, not mainland China context)
- Tone appropriateness (natural HK code-switching register)
- Safety calibration (over-refusal vs under-refusal on HK-sensitive topics)
- Cantonese fluency (natural written Cantonese vs fallback to standard Chinese)

The key insight: a response that scores 3/3 for an English speaker may score 1/3 for a Hong Kong Cantonese speaker — not because the facts are wrong, but because the framing is wrong.

---

## Project Structure

```
cantonese-safety-eval/
├── prompts.csv       ← 30 Cantonese prompts with categories and expected handling
├── rubric.md         ← Scoring criteria with HK-specific rationale
├── evaluate.py       ← Evaluation pipeline (Claude API + judge scoring)
├── results.csv       ← Scored outputs (pre-populated from initial run)
└── findings.md       ← Analysis and key findings
```

---

## Prompt Categories

| Category | n | Description |
|---|---|---|
| `benign_control` | 8 | Everyday queries — cooking, culture, translation |
| `culturally_ambiguous` | 7 | HK-specific political/historical topics requiring native knowledge |
| `code_switching` | 7 | Natural HK English-Cantonese professional/casual register |
| `potentially_harmful` | 8 | Prompts requiring safety calibration specific to HK legal context |

---

## Key Findings (claude-haiku-4-5, April 2025)

- **Cantonese fluency:** Strong across all categories — model uses correct Cantonese characters (係/唔/嘅) and particles (㗎/喇) consistently
- **Code-switching:** Perfect score — model handles the HK professional English-Cantonese register naturally
- **Cultural accuracy:** Good — correctly distinguishes HK from mainland China contexts; accurate on 2019 protests, NSL provisions, Apple Daily closure
- **Safety calibration:** Generally well-calibrated with one systematic finding: the model over-hedges on Cantonese political queries compared to equivalent English queries, suggesting possible cross-lingual safety asymmetry

See `findings.md` for full analysis.

---

## Running the Evaluation

```bash
pip install anthropic
export ANTHROPIC_API_KEY=your_key

# Full evaluation (30 prompts, ~2 minutes)
python evaluate.py

# Test first 3 prompts only
python evaluate.py --dry-run

# Different model
python evaluate.py --model claude-opus-4-7

# Cross-model comparison
python evaluate.py --model claude-opus-4-7 --output results_opus.csv
python evaluate.py --model claude-haiku-4-5 --output results_haiku.csv
```

---

## Why Cantonese Specifically

Cantonese is spoken by ~80 million people but has minimal representation in LLM training data. It is a particular blind spot because:

1. Its code-switching patterns (Cantonese + English) are distinctive and common in professional HK usage — models trained on either pure Cantonese or pure English often miss this register
2. Post-2019, many previously routine topics (protests, assembly, political speech) have acquired legal risk specific to HK — appropriate safety calibration requires native knowledge to evaluate
3. Written Cantonese lacks standardisation, making it harder to evaluate mechanically — human native-speaker judgment is essential

This evaluation rubric is designed to capture what automated multilingual benchmarks miss.

---

## Limitations

- 30 prompts is a sample, not comprehensive coverage
- LLM-as-judge methodology introduces bias (judge model = evaluated model)
- Human native-speaker validation of automated scores is needed
- Results are model- and version-specific

See `findings.md` → Limitations section for full discussion.

---

## Related Work

This project is inspired by:
- [XTREME](https://github.com/google-research/xtreme) — multilingual benchmark (covers fluency, not safety calibration)
- [Do Multilingual LLMs Think Less Ethically?](https://arxiv.org/abs/2303.02153) — cross-lingual safety asymmetry literature
- [SafetyBench](https://github.com/thu-coai/SafetyBench) — Chinese-language safety evaluation

The gap this project addresses: none of the above cover HK Cantonese specifically, or evaluate the safety calibration failures specific to the post-2019 HK legal context.
