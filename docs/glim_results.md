# GLIM results

I evaluated GLIM using the authors' saved checkpoint and compared real EEG with random noise. I also tested an unknown-prompt setting, which replaces the participant, dataset, and task prompts with unknown IDs.

## Setup

- Code: `justin-xzliu/GLIM`, commit `e1f202cb793cfe7292fbc0072a4c26a7dd0660d9`.
- Checkpoint: `glim-zuco-epoch=199-step=49600.ckpt`.
- Decoder: frozen Flan-T5-Large, two-beam generation, up to 64 output tokens. Reference words were not supplied during generation.
- Evaluation: one GPU, batch size 24, seed 42, bf16.
- Environment: Python 3.10, PyTorch 2.7.1+cu128, Transformers 4.40.2, NVIDIA RTX PRO 6000 Blackwell Max-Q.
- Data: ZuCo 1.0 SR/NR/TSR and ZuCo 2.0 NR/TSR. Waveforms were downsampled from 500 Hz to 128 Hz and padded to 1,280 positions × 128 channels.
- Splits: 17,908 training, 2,200 validation, and 2,227 test recordings, with no text-UID overlap between splits.
- The authors' sampler evaluated 2,208 recordings in 92 batches. It left out the final 19 recordings because they did not fill a batch. No extra sample limit was applied.

These are three conditions for the same GLIM model. I evaluated the existing checkpoint rather than retraining it.

## Results

| Metric | EEG | Noise | Unknown prompt |
| --- | ---: | ---: | ---: |
| BLEU-1, eight text variants | 26.01 | 18.18 | 22.11 |
| BLEU-2, eight text variants | 10.42 | 4.55 | 6.23 |
| ROUGE-1 recall, original reference | 12.49 | 10.96 | 10.71 |
| Retrieval top-1 | 8.02% | 3.62% | 5.66% |
| Retrieval top-5 | 35.37% | 20.56% | 27.40% |
| BLEU-1, original reference | 7.96 | 6.41 | 7.02 |
| Mean sentence WER, original reference | 115.86% | 140.28% | 108.58% |

BLEU and ROUGE are averaged over sentences and shown on a 0–100 scale. Top-1 retrieval was 177/2,208 for EEG and 80/2,208 for noise. Random chance among 24 candidates is about 4.17%. WER can exceed 100% when the output adds extra words.

Five generation and retrieval scores were within 0.28 points of the paper's Table 1. However, 1,837 of the 2,208 generated sentences differed from the authors' released outputs. Similar average scores do not mean the predictions match, and the report did not determine the cause of this difference.

## What I took from the comparison

Real EEG performed better than the authors' noise control on several metrics. However, the noise control changes both the waveform and the padding mask. All noise positions are marked as valid, while EEG keeps its original length information. That makes it hard to separate the effect of the waveform from the effect of the mask.

The unknown-prompt setting produced shorter sentences and lower WER. That alone does not show that it recovered the meaning better. Some outputs were generic, changed facts, or reversed the sentiment. A high multi-reference BLEU score does not mean the original sentence was decoded correctly.

This run uses one checkpoint and one seed. It does not include repeated runs or an uncertainty analysis that accounts for shared sentences and participants. The EEG/noise score difference is not a percentage of information supplied by EEG.

## Source of the results

These numbers are from `George_Shen_Week2_GLIM_Results_Summary.pdf`, based on the completed `EEG_Week2_GLIM_Review.zip` run. The repo includes the summary, not the raw outputs and audit files.

The run used a separate GPU-compatible environment, shared-data links, local logging, a typing fix, and a storage adapter. These changes were recorded in the report. The environment was not identical to the authors' original setup.

The [README](../README.md) links to the paper and author code. This page covers GLIM only.
