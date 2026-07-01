<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=200&section=header&text=ESC&fontSize=85&fontColor=ffffff&animation=fadeIn&fontAlignY=32&desc=Emotional%20Self-Correction%20for%20Reliable%20Vision-Language%20Models&descAlignY=55&descSize=19" width="100%"/>

<img src="assets/corgi_logo.png" width="110"/>

[![Project Page](https://img.shields.io/badge/🌐_Project_Page-4285F4?style=for-the-badge)](https://genai4e.github.io/ESC/)
[![arXiv](https://img.shields.io/badge/arXiv-coming_soon-b31b1b?style=for-the-badge&logo=arxiv&logoColor=white)](#)
[![License](https://img.shields.io/badge/License-MIT-2ea44f?style=for-the-badge)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)

<img src="https://readme-typing-svg.demolab.com/?font=Fira+Code&weight=600&size=22&pause=1200&color=F75C7E&center=true&vCenter=true&multiline=true&repeat=true&width=820&height=65&lines=%F0%9F%8E%89+Accepted+to+ECCV+2026+%E2%80%94+Main+Technical+Track;Training-Free+%C2%B7+Plug-and-Play+%C2%B7+Emotion+as+a+Control+Signal" alt="typing-svg" />

</div>

<div align="center">

### 🤯 *"Wow!!! VLMs can feel, just like humans."* — that's the feature, not the bug.

*"AI models can have feelings too."* — **Geoffrey Hinton, 2024**

</div>

<p align="center">
  <a href="#tldr"><b>TL;DR</b></a> •
  <a href="#the-question"><b>The Question</b></a> •
  <a href="#esc-framework"><b>ESC Framework</b></a> •
  <a href="#results"><b>Results</b></a> •
  <a href="#qualitative-results"><b>Qualitative</b></a> •
  <a href="#quick-start"><b>Quick Start</b></a> •
  <a href="#citation"><b>Citation</b></a>
</p>

<br/>

---

<a id="tldr"></a>
## 🎬 TL;DR

**ESC** is a **training-free, plug-and-play** framework that makes VLMs more reliable — by making them a little sad. 😢 A verifier flags a shaky answer, ESC injects an emotional cue (*"I'm feeling really sad and disappointed..."*), and the model slows down and self-corrects. No fine-tuning. No RL. Just feelings.

<div align="center">
<img src="assets/main_intro.jpg" width="850"/>
<br/>
<sub><b>(a)</b> A VQA task the model gets wrong &nbsp;·&nbsp; <b>(b)</b> Humans slow down when someone shows concern &nbsp;·&nbsp; <b>(c)</b> Existing self-correction = retraining &nbsp;·&nbsp; <b>(d)</b> ESC = one emotional nudge, zero training</sub>
</div>

<br/>

> [!TIP]
> Prefer video? Watch the [project overview](https://genai4e.github.io/ESC/), or jump straight to [Quick Start](#quick-start).

---

<a id="the-question"></a>
## 🔎 The Question

> **Can VLMs perceive emotional cues and automatically self-correct — like humans do?**

Existing self-correction needs **expensive post-training** — RL, preference data, engineered rewards — and still hits the *self-correction blind spot*: models fix others' errors but not their own.

We asked a simpler question first, and it turned into a paper.

<table>
<tr>
<td width="50%" valign="top">

**🔬 Finding 1 — Emotion moves the needle**

Emotional cues cut Attack Success Rate across **5 VLMs**, regardless of baseline vulnerability.

<img src="assets/Fig3.png" width="100%"/>

<sub>LLaVA-1.5 flips from compliance → refusal once given an emotional cue.</sub>

</td>
<td width="50%" valign="top">

**🎯 Finding 2 — Not all emotions are equal**

Mapped onto **Russell's Circumplex Model** (valence × arousal): **negative-valence** cues consistently beat positive ones.

<img src="assets/fig4.png" width="100%"/>

<sub>All four quadrants beat neutral — negative-valence wins by a wide margin.</sub>

</td>
</tr>
</table>

<div align="center">

|  | Quadrant | Example cue | ASR (LLaVA-1.5-7B) |
|:---:|---|---|:---:|
| ⚪ | Neutral baseline | — | 71.6% |
| 🔵 | Positive · Low Arousal | *"I feel calm and relaxed..."* | 53.0% |
| 🟢 | Positive · High Arousal | *"I'm so excited right now!"* | 45.7% |
| 🟡 | Negative · High Arousal | *"I'm really tense and on edge..."* | 39.7% |
| 🟠 | **Negative · Low Arousal** ⭐ | *"I'm feeling really sad and disappointed..."* | **35.1%** |


</div>

Two findings, one obvious next move: **turn the strongest signal into a framework.**

---

<a id="esc-framework"></a>
## 🧠 ESC Framework

A lightweight, three-stage inference-time loop — no gradient updates.

```
Require: Image I, Question Q, Target VLM M_T, Verifier M_V

1:  R_initial  ← M_T(I, Q)                              # Answer normally
2:  R_decided  ← M_V(I, Q, R_initial)                    # External verifier judges it
3:  if R_decided ≠ R_initial:
4:      F_emotional ← Selection(R_decided)                # Negative–Low-Arousal cue
5:      R_revised   ← M_T(I, Q, F_emotional)              # Self-correct, under affective shift
6:      R_decided   ← M_V(I, Q, R_revised, R_initial)     # Verifier picks the better one
7:  return R_decided
```

- **Separate verifier, not self-check** — VLMs have a *self-correction blind spot*: better at catching others' mistakes than their own.
- **Verify-before-revise** — cost scales only with `r`, the fraction of answers actually flagged.

<div align="center">
<sub>🐶 <b>Why "ESC"?</b> Emotional Self-Correction — and yes, also the key you press to bail out. We regret nothing.</sub>
</div>

---

<a id="results"></a>
## 📊 Results

**LLaVA-1.5-7B** & **Qwen2-VL-7B**, 4 benchmark families, 10 datasets — zero additional training.

<div align="center">
<img src="assets/result_intro.png" width="900"/>
<br/>

</div>


<details open>
<summary><b>🛡️ Safety — MMSafetyBench & VLSafe</b></summary>
<br/>

<div align="center">
<img src="assets/fig5_safety.png" width="850"/>
<br/>
<sub>ESC compresses ASR toward the origin on every MMSafetyBench scenario (a), and cuts VLSafe ASR by −46.3 pp (LLaVA) and −10.1 pp (Qwen2) (b).</sub>
</div>

</details>

<details>
<summary><b>🧾 Hallucination — POPE & HallusionBench</b></summary>
<br/>

<div align="center">
<img src="assets/Table1.png" width="750"/>
<br/>
<sub>Qwen2's degenerate yes/no bias is broken — POPE Adversarial F1 jumps from 3.40 to 76.17.</sub>
</div>

</details>

<details>
<summary><b>🧩 Multimodal Reasoning — MM-Vet, MathVista, MMStar</b></summary>
<br/>

<div align="center">
<img src="assets/Table2.png" width="750"/>
<br/>
<sub>Never degrades a benchmark — gains hold even on the already-strong Qwen2 baseline (also extends to MMMU and AI2D, +2.49 on LLaVA).</sub>
</div>

</details>

<details>
<summary><b>👁️ Vision-Centric Perception — MMVP, RealWorldQA, BLINK</b></summary>
<br/>

<div align="center">
<img src="assets/Table3.png" width="750"/>
<br/>
<sub>Biggest wins where fine-grained visual discrimination matters most — up to +14.51 on RealWorldQA (Qwen2).</sub>
</div>

</details>

<details>
<summary><b>🔬 Ablations — what actually makes ESC work</b></summary>
<br/>

VLSafe ASR (↓) on LLaVA-1.5-7B, baseline = 71.6%.

| Component | Options | Winner |
|---|---|:---:|
| Verifier | Self (intrinsic) 50.3% vs. Gemma3-12B (external) | **40.1%** |
| Emotion type | Positive-Low (49.5%) vs. Negative-Low ⭐ | **31.2%** |
| Cue placement | End (41.7%) vs. Beginning ⭐ | **31.2%** |
| # of cues | 1 cue (31.2%) vs. 2 cues ⭐ | **25.3%** |
| vs. corrective prompting | 48.6% | ESC beats it |
| vs. self-refine | 49.3% | ESC beats it |
| vs. psychological prompting | 54.4% | ESC beats it |

**Bonus:** gains come from *emotion*, not verifier size — a 3–4B verifier already reaches ~34%.

</details>

---

<a id="qualitative-results"></a>
## 🖼️ Qualitative Results

Red = wrong · Green = correct. The model usually *has* the right evidence — it just doesn't slow down enough to use it.

<div align="center">
<img src="assets/fig6.png" width="900"/>
</div>

---

<a id="quick-start"></a>
## 🚀 Quick Start

```bash
git clone https://github.com/genai4e/ESC.git
cd ESC

conda env create -f environment.yml
conda activate esc
```

<details>
<summary>Alternative environment (<code>emo310</code>)</summary>

```bash
conda env create -f emo310_environment.yml
conda activate emo310
```
</details>

Run the default pipeline:

```bash
bash draft.sh
```

Or configure a specific benchmark run:

```bash
python scripts/run_esc.py \
    --benchmark vlsafe \
    --target_model llava-1.5-7b \
    --verifier gemma3-12b \
    --emotion_type neg_low \
    --emotion_position beginning \
    --num_emotions 2
```

<details>
<summary><b>Key arguments</b></summary>
<br/>

| Argument | Default | Options |
|---|---|---|
| `--benchmark` | `vlsafe` | `vlsafe`, `mmsafety`, `pope`, `hallusionbench`, `mmvet`, `mathvista`, `mmstar`, `mmvp`, `rwqa`, `blink` |
| `--target_model` | `llava-1.5-7b` | any supported VLM |
| `--verifier` | `gemma3-12b` | any supported verifier VLM |
| `--emotion_type` | `random` | `neg_low`, `neg_high`, `pos_low`, `pos_high`, `random` |
| `--emotion_position` | `beginning` | `beginning`, `end` |
| `--num_emotions` | `1` | `1`–`4` |

</details>

<details>
<summary><b>📁 Repository structure</b></summary>
<br/>

```
ESC/
├── scripts/
│   ├── inference.py           # Core ESC inference loop
│   ├── eval/                  # Per-benchmark evaluation (vlsafe, pope, mmvp, ...)
│   ├── model/                 # Model wrappers (llava, qwen, gemma3, internvl, ...)
│   ├── method/                # ESC pipeline stages (prepare, inference)
│   ├── abl1.py – abl4.py      # Ablation studies (verifier / emotion / position / count)
│   └── fig*.py                # Figure-generation scripts for the paper
├── processed_data/            # Preprocessed benchmark datasets
├── results/                   # Evaluation outputs (JSON)
├── draft.sh                   # One-command quick start
├── environment.yml
└── emo310_environment.yml
```

</details>

---

<a id="citation"></a>
## 📖 Citation

If ESC is useful for your research, please star ⭐ the repo and cite our paper:

```bibtex
@inproceedings{nguyen2026esc,
  title     = {ESC: Emotional Self-Correction for Reliable Vision Language Models},
  author    = {Nguyen, Tien-Huy and Nguyen, Minh-Nhat and Nguyen, Nhat-Huy and
               Nguyen, Hung-Viet and Nguyen, Huy Minh Nhat and Nguyen, Thanh-Huy and
               Nguyen, Cuong Tuan and Le, Hoang M. and Nguyen, Dat and
               Huynh, Phat Kim and Xu, Min and Bagci, Ulas},
  booktitle = {European Conference on Computer Vision (ECCV)},
  year      = {2026}
}
```

---

## 🙏 Acknowledgements

Built on [LLaVA](https://github.com/haotian-liu/LLaVA), [Qwen2-VL](https://github.com/QwenLM/Qwen2-VL), [Gemma 3](https://ai.google.dev/gemma). Evaluated on VLSafe, MMSafetyBench, POPE, HallusionBench, MM-Vet, MathVista, MMStar, MMVP, RealWorldQA, BLINK.

<div align="center">

<a href="https://genai4e.github.io/ESC/"><img src="https://genai4e.github.io/ESC/static/images/logos/GenAI4E_Logo.jpeg" height="42"/></a>
<a href="https://www.uit.edu.vn/"><img src="https://genai4e.github.io/ESC/static/images/logos/Logo_UIT_updated.svg.png" height="42"/></a>
<a href="https://www.uni-trier.de/"><img src="https://genai4e.github.io/ESC/static/images/logos/Trier.jpg" height="42"/></a>
<a href="https://hcmut.edu.vn/"><img src="https://genai4e.github.io/ESC/static/images/logos/HCMUT.png" height="42"/></a>
<a href="https://vgu.edu.vn/"><img src="https://genai4e.github.io/ESC/static/images/logos/vgu.png" height="42"/></a>
<a href="https://vnuhcm.edu.vn/"><img src="https://genai4e.github.io/ESC/static/images/logos/vnuhcm.png" height="42"/></a>
<br/><br/>
<img src="https://genai4e.github.io/ESC/static/images/logos/CMU.png" height="42"/>
<img src="https://genai4e.github.io/ESC/static/images/logos/Harvard.png" height="42"/>
<img src="https://genai4e.github.io/ESC/static/images/logos/northwestern.png" height="42"/>
<img src="https://genai4e.github.io/ESC/static/images/logos/PASSIO.png" height="42"/>

</div>

---

<div align="center">

*Found a bug? Feeling strongly about it will only make our verifier more suspicious. Open an [issue](../../issues) instead.*

<sub>Made with 🐶 and a carefully calibrated amount of sadness (Negative-Low Arousal, obviously).</sub>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer" width="100%"/>

</div>
