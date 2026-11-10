# GLIM checkpoint evaluation: recorded results

This document transcribes the completed Week 2 GLIM results summary. It is a record of an earlier experiment, not a new run performed while preparing this repository.

## Protocol

- Author source: `justin-xzliu/GLIM`, commit `e1f202cb793cfe7292fbc0072a4c26a7dd0660d9`.
- Checkpoint: `glim-zuco-epoch=199-step=49600.ckpt`.
- Frozen Flan-T5-Large decoder; two-beam generation, maximum 64 output tokens; no word-level teacher forcing during generation.
- One GPU, test batch size 24, seed 42, bf16 precision.
- Recorded environment: Python 3.10, PyTorch 2.7.1+cu128, Transformers 4.40.2, NVIDIA RTX PRO 6000 Blackwell Max-Q.
- ZuCo 1.0 SR/NR/TSR and ZuCo 2.0 NR/TSR waveforms, converted from 500 Hz to 128 Hz and padded to 1,280 positions by 128 channels.
- Accepted splits: 17,908 training, 2,200 validation, and 2,227 test recordings; no text-UID overlap between splits.
- The native sampler evaluated 2,208 recordings in 92 batches, omitting 19 incomplete-batch recordings. No additional evaluation cap was introduced.
- Conditions: real EEG, released author noise control, and real EEG with unknown participant/dataset/task prompts. The third condition is an ablation of GLIM.

## Outcomes

| Metric | EEG | Noise | Unknown prompt |
| --- | ---: | ---: | ---: |
| BLEU-1, eight text variants | 26.01 | 18.18 | 22.11 |
| BLEU-2, eight text variants | 10.42 | 4.55 | 6.23 |
| ROUGE-1 recall, original reference | 12.49 | 10.96 | 10.71 |
| Retrieval top-1 | 8.02% | 3.62% | 5.66% |
| Retrieval top-5 | 35.37% | 20.56% | 27.40% |
| BLEU-1, original reference | 7.96 | 6.41 | 7.02 |
| Mean sentence WER, original reference | 115.86% | 140.28% | 108.58% |

BLEU/ROUGE are sentence-averaged, on a 0–100 scale. Retrieval top-1 was 177/2,208 for EEG and 80/2,208 for noise; random top-1 chance among 24 candidates is approximately 4.17%. WER can exceed 100% because insertions add edits.

Five generation/retrieval scores reported in the paper's Table 1 were within 0.28 points of the recorded run. Nevertheless, 1,837 of 2,208 generated strings differed from the authors' public export. Matching aggregate scores does not establish identical predictions. The available report did not isolate the cause of those differences.

## Interpretation and limits

The real-EEG condition outperformed the released noise control on several metrics. The author noise implementation changes both waveforms and masks: all noise positions are marked valid, while real EEG keeps padding/length information. This experiment therefore does not isolate linguistic information in the waveform alone.

The unknown-prompt condition had shorter outputs and lower WER, which is not by itself evidence of better meaning recovery. The report also documented repeated generic outputs, factual errors, and sentiment reversals. Multi-reference BLEU is not an exact-decoding success rate.

This is one saved checkpoint and one seed, without repeated-run or cluster-aware uncertainty analysis. No claim is made that the EEG/noise performance gap measures a percentage of information from EEG versus the language model.

## Provenance

Source artifact: `George_Shen_Week2_GLIM_Results_Summary.pdf`, prepared from the completed `EEG_Week2_GLIM_Review.zip` experiment archive. This repository includes a transcription of selected aggregate results; it does not include that archive's raw outputs or audit files.

The completed run used shared-data links, an isolated GPU-compatible environment, local logging, typing compatibility, and a storage adapter. These infrastructure adaptations were disclosed in the report; this is not a claim of bit-for-bit execution in the authors' original environment.

The paper and author implementation are linked in the repository README. SemKey is a separate pending experiment and no SemKey, DeWave, or BELT result is claimed here.
