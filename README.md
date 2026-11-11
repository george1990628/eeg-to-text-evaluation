# EEG-to-text evaluation

**Status: active research; completed experiments and pending work are identified below.**

Reproduction and evaluation work asking how EEG-to-text performance changes when real EEG is replaced with noise. The focus is on separating evidence from neural input from the language patterns a pretrained decoder already knows.

This repository includes an adapted BART experiment notebook and a documented summary of a completed GLIM checkpoint evaluation. The original model architectures belong to the cited research authors.

## Included work

| Component | Scope |
| --- | --- |
| BART EEG/noise notebook | A runnable, small-scale adaptation using word-level ZuCo features. Includes EEG/noise training, four train/test input combinations, and separately labeled free-generation and teacher-forced evaluation. |
| GLIM results | Recorded evaluation of the authors' released checkpoint on 2,208 recordings, using real EEG, the author noise control, and an unknown-prompt ablation. |
| Evaluation notes | Metric definitions, protocol differences, and limitations on interpreting EEG/noise gaps. |

## Start with the BART notebook

Open [`notebooks/01_bart_eeg_vs_noise.ipynb`](notebooks/01_bart_eeg_vs_noise.ipynb) in Google Colab, select a GPU runtime, and run the cells in order. The notebook installs its pinned dependencies, retrieves pinned author code, and downloads public data from the original host.

The default `pilot` uses one participant, two ZuCo 1.0 reading tasks, at most 256/32/64 train/development/test examples, and two epochs per training condition. It is a pipeline experiment, not a full paper reproduction. The `full_v1` preset still covers only the BART portion of the original study. The notebook contains no prefilled experiment scores.

The pilot downloads approximately 1.51 GB of source EEG files plus BART-large weights. Two resumable best checkpoints can require roughly 7 GB, with additional temporary space during replacement. Review the notebook's storage settings before starting. Google Drive mounting is optional and handled in your own Colab session.

## Recorded GLIM results

| Metric | Real EEG | Author noise control |
| --- | ---: | ---: |
| BLEU-1 against eight text variants | 26.01 | 18.18 |
| Top-1 retrieval among 24 candidates | 8.02% | 3.62% |
| BLEU-1 against the original reference | 7.96 | 6.41 |
| Mean sentence WER against the original reference | 115.86% | 140.28% |

These values come from the completed evaluation report. BLEU uses a 0–100 scale. Retrieval is distinct from generating a correct sentence. This was evaluation of saved author weights, not a reproduction of GLIM's original training run.

See [the protocol and results](docs/glim_results.md) and [the transcribed metric table](results/glim_checkpoint_metrics.csv). The BART notebook does not run GLIM; the full GLIM execution package, raw EEG, checkpoints, and generated sample records are not included here.

## What the comparison supports

Real EEG improved several scores for this GLIM checkpoint. However, the released noise control also replaces the padding mask, so the difference does not isolate waveform content alone. It is not a percentage of information attributable to EEG. The BART adaptation retains matching masks and uses different inputs and metrics; its scores must not be directly compared to GLIM's multi-reference scores.

## Sources and attribution

- [A.1: Evaluating EEG-to-text models through noise-based performance analysis](https://www.nature.com/articles/s41598-025-29587-x) and [author code](https://github.com/khu-aims/EEG-To-Text).
- [Original EEG-to-text preprocessing reference](https://github.com/MikeWangWZHL/EEG-To-Text).
- [GLIM paper](https://arxiv.org/abs/2505.17099) and [author code](https://github.com/justin-xzliu/GLIM), evaluated at commit `e1f202cb793cfe7292fbc0072a4c26a7dd0660d9`.
- [ZuCo public data](https://osf.io/q3zws/).


## Validation

During portfolio preparation, notebook structure and code-cell syntax were checked.
