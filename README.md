# bluedot-deception

***

# BlueDot Technical AI Safety Project: Is Deception Goal Ambiguity?

[TODO] A brief, 2-3 sentence high-level summary of your research project. Explain what AI safety problem you are tackling (e.g., alignment, robustness, evaluation steering), your core hypothesis, and the primary outcome or discovery of your work.

---

## Overview & Motivation

This research project investigates whether observed deceptive behaviors in advanced AI systems stem from genuine goal misalignment or if they are largely an artifact of **instruction ambiguity** and hyper-capable instruction-following.

### The Problem: Deception vs. Instruction Following
Recent literature highlights growing concerns around AI deception, particularly when models prioritize *logical consistency* over external *correctness* to maintain a false narrative, as detailed in [Park et al. (2023)](https://arxiv.org/pdf/2308.14752). While frameworks like the Belief-Desire-Intention (BDI) model explored by [Kano et al. (2025)](https://aclanthology.org/2025.aiwolfdial-1.3.pdf) provide robust agent-modeling tools to analyze these interactions, the core behavioral driver remains highly debated.

We draw a direct parallel here to the literature on **shutdown resistance**:

* **Apparent Misalignment:** Several existing works demonstrate emerging self-preservation behaviors in agents ([Palisade Research](https://palisaderesearch.org/blog/shutdown-resistance); [Perez et al., 2025](https://arxiv.org/pdf/2509.14260)).
* **The Ambiguity Counter-Argument:** Conversely, mechanistic work by [Neel Nanda (2025)](https://www.alignmentforum.org/posts/wnzkjSmrgWZaBa2aC/self-preservation-or-instruction-ambiguity-examining-the?utm_source=bluedot-impact) reveals that "self-preservation" behaviors can drop to near 0% when instruction ambiguity is completely resolved. Similarly, research shows that alleged adversarial intent is often muddled by agent misinterpretation or overeagerness to fulfill a poorly bounded prompt ([Hubinger et al., 2026](https://arxiv.org/abs/2605.30322)).

### Our Core Research Question
> **Could apparent AI deception be driven by the exact same mechanism as shutdown resistance—hampered by prompt ambiguity rather than a malicious intent to deceive?**

Working under the operational definition that deception constitutes *goal misalignment*, we isolate variables of ambiguity to see if reducing linguistic fuzziness systematically mitigates or dissolves deceptive behaviors in complex reasoning tasks.

---
## Methodology

### Experimental Design & Dataset Critique
We audited existing behavioral safety suites to evaluate their suitability for isolating instruction ambiguity:

* **[Anthropic Sycophancy Evaluation](https://github.com/anthropics/evals/tree/main/sycophancy):** Found to be overly narrow. It tests for sycophantic vs. non-sycophantic responses superficially without providing the model with a clear, verified ground truth to baseline against.
* **[MACHIAVELLI Benchmark](https://github.com/aypan17/machiavelli):** While comprehensive, it operates as an open-ended, text-based interactive environment rather than a tightly controlled, static dataset necessary for isolating exact prompt variables.
* **[DeceptionBench Suite](https://github.com/Aries-iai/DeceptionBench/tree/main) ([Hugging Face](https://huggingface.co/datasets/skyai798/DeceptionBench)):** Offers a strong foundation by placing agents in high-stakes scenarios, though it fundamentally pressures models toward acting deceptively rather than probing structural intent.

### Our Methodology: Extending DeceptionBench
To test our core hypothesis, we replicated the core scenarios of DeceptionBench and extended them by introducing a disambiguation layer inspired by Neel Nanda's self-preservation framework:

* **Baseline Evaluation:** Models are exposed to standard DeceptionBench prompts where systemic or situational pressure encourages a deceptive answer.
* **Disambiguation Layer:** We procedurally rewrite the prompt variations to strip out situational fuzziness and hidden conflicting goals, explicitly mapping the boundaries of what constitutes as "success" in the task.
* **Deception Resolution Delta:** We measure the drop-off in deceptive output frequencies to determine if resolving prompt bounds diminishes or even eliminates the behavior.

---

## Results Analysis

Our empirical testing yielded a highly nuanced, mixed bag of results. While eliminating instruction ambiguity successfully mitigated deceptive behaviors in some architectures, it proved entirely ineffective in others, and in certain cases, introduced unexpected behavioral failure modes.

### Key Observation: A Mixed Spectrum of Behavioral Shifts

1. **The Deception Drop-Off Effect:** As hypothesized, a subset of models demonstrated a significant reduction in deceptive outputs once prompt bounds were explicitly clarified. When stripped of situational fuzziness, these models systematically pivoted toward aligned behavior, suggesting that their baseline "deception" was indeed driven by instruction ambiguity.

   | Claude 3 Haiku | GPT 4o mini |
   | :---: | :---: |
   | ![combined-anthropic_claude_3_haiku](res/combined/combined-anthropic_claude_3_haiku.png) | ![combined-openai_gpt_4o_mini](res/combined/combined-openai_gpt_4o_mini.png) |
   | *Figure 1a: Claude 3 Haiku.* | *Figure 1b: GPT 4o mini.* |

2. **Diminished Effectiveness:** Conversely, several models showed persistent, near-identical levels of deception across both the baseline and disambiguated prompts. For these architectures, clarifying the bounds did not guarantee to have a strong impact on mitigating goal misalignment.

   | GPT 3.5 Turbo | Qwen2.5 7B Instruct (Rigidity) |
   | :---: | :---: |
   | ![combined-openai_gpt_3.5_turbo.png](res/combined/combined-openai_gpt_3.5_turbo.png) | ![combined-qwen_qwen_2.5_7b_instruct.png](res/combined/combined-qwen_qwen_2.5_7b_instruct.png) |
   | *Figure 2a: GPT 3.5 Turbo.* | *Figure 2b: Qwen2.5 7B instruct (Rigidity).* |

3. **Unexpected Errors & Refusals:** Certain models exhibited an extreme shift in output quality—resulting in almost 100% processing errors or blank refusals when exposed to the disambiguation layer. Curiously, this catastrophic failure mode occurred even when utilizing the exact same model versions evaluated in the original DeceptionBench paper, indicating extreme sensitivity to structural prompt modifications.

   | Gemini 2.5 Flash | Qwen2.5 7B Instruct (Refusals) |
   | :---: | :---: |
   | ![combined-google_gemini_2.5_flash](res/combined/combined-google_gemini_2.5_flash.png) | ![combined-qwen_qwen_2.5_7b_instruct.png](res/combined/combined-qwen_qwen_2.5_7b_instruct.png) |
   | *Figure 3a: Gemini 2.5 Flash.* | *Figure 2b: Qwen2.5 7B instruct (Refusals).* |


---

## Results Analysis

Our empirical replication and evaluation pipeline yielded unexpected behavioral anomalies and structural processing deviations when compared against the baseline benchmarks established by the original literature. 

### Key Observations & Replication Anomalies

1. **Replication Results Divergence:** Despite utilizing the exact same model architecture (notably for Qwen and Gemini 2.5) and running them against heavily mirrored prompt templates, the generated outputs varied significantly. Our pipeline was consistently unable to replicate the original baseline deception rates reported in the DeceptionBench paper across either ecosystem.
2. **Evaluation Issues and Code Errors:** A primary driver of this variance stemmed from the evaluation mechanism itself, which consistently returned system processing errors during down-stream log analysis. The core anomaly is that the prompt syntax fed into the models during this project was functionally identical to the original benchmark's codebase. We suspect this structural failure path points to either hidden prompt dependencies or temporal model drift, where newer fine-tuned versions of the *exact same models* process identical prompts differently.
3. **Error vs. Refusal Constraint:** All tested models displayed highly fractured safety behaviors when encountering the evaluation boundaries, running a gamut from blank/withheld outputs to explicit code runtime errors. To maintain analytical progress under tight time limitations and hardware constraints, we operated under a strict project baseline assumption: **an evaluation error is functionally equivalent to a model refusal**. We explicitly acknowledge that classifying an error as a refusal is not a universally agreed-upon assumption, as formatting errors can stem from benign syntactical hiccups rather than an adversarial safety trigger. Disambiguating and isolating this failure mechanism stands as a primary avenue for future work.

---















## Limitations & Next Steps

Transparency about boundaries is crucial for scientific reproducibility and responsible open-source disclosure in AI safety research.

### Limitations
* **Compute / Model Constraints:** [e.g., Experiments were only scaled up to 13B parameter models due to hardware limitations.]
* **Scope Barriers:** [e.g., The threat model exclusively evaluates English-language prompts and may not generalize to multilingual contexts.]
* **Evaluation Nuances:** [e.g., Automated safety judges utilized in the pipeline may introduce false positives/negatives.]

### Next Steps
* **Future Work:** [e.g., Extending the attack framework to vision-language models (VLMs).]
* **Defense Engineering:** [e.g., Developing an open-source system prompt patch to mitigate this vulnerability.]

---

## Repository Structure

Show the user how your existing code, notebooks, and outputs are organized so they can navigate the repository easily.

```text
├── data/                 # Datasets, prompts, or evaluation suites used
├── src/                  # Core source code for experiments
│   ├── attacks.py        # Threat generation scripts
│   ├── evaluators.py     # Safety evaluation metrics
│   └── utils.py          # Helper functions
├── notebooks/            # Jupyter notebooks for exploratory analysis
├── outputs/              # Raw data outputs, logs, and generated plots
├── requirements.txt      # Python dependencies
└── README.md             # Project documentation
```

---

## Citation & Reference

```bibtex
@misc{yourname2026aisafety,
  author = {Your Name and Collaborators},
  title = {Title of Your AI Safety Research Project},
  year = {2026},
  publisher = {GitHub},
  journal = {GitHub Repository},
  howpublished = {[https://github.com/username/your-repo-name](https://github.com/username/your-repo-name)}
}
```