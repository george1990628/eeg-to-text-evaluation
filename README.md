# EEG-to-text

**In progress**

I'm studying how much EEG-to-text models rely on the EEG input and how much comes from the language model. I compare real EEG with random noise and look at both the scores and the sentences the models generate.

This repo includes a small BART experiment and results from my evaluation of the released GLIM model.

## What's here

- [BART notebook](notebooks/01_bart_eeg_vs_noise.ipynb): train with EEG or noise, then test all four training/input combinations. It reports free-generation results separately from scores that use teacher forcing.
- [GLIM results](docs/glim_results.md): an evaluation of the authors' saved checkpoint on 2,208 recordings, including EEG, noise, and an unknown-prompt comparison.
- [GLIM metric table](results/glim_checkpoint_metrics.csv): the reported scores in CSV format.

## Run the BART experiment

Open the notebook in Google Colab, choose a GPU runtime, and run the cells in order. It installs the required packages and downloads the author code and public ZuCo data.

Start with `PRESET='pilot'`. It uses one participant, two ZuCo 1.0 tasks, up to 256 training, 32 development, and 64 test examples, and two epochs for each training condition. This is a small experiment to check the setup, not a full reproduction of the paper. The `full_v1` setting expands the data but still covers only BART.

The pilot downloads about 1.51 GB of EEG files plus BART-large weights. Saving training state for both models can take around 7 GB, with extra space needed while replacing checkpoints. Check the storage settings before starting. Saving to Google Drive is optional.

## GLIM results

| Metric | Real EEG | Author noise control |
| --- | ---: | ---: |
| BLEU-1 against eight text variants | 26.01 | 18.18 |
| Top-1 retrieval among 24 candidates | 8.02% | 3.62% |
| BLEU-1 against the original reference | 7.96 | 6.41 |
| Mean sentence WER against the original reference | 115.86% | 140.28% |

BLEU scores use a 0–100 scale. Retrieval measures whether the correct reference ranks first among 24 choices, which is different from generating that sentence. I evaluated the authors' trained checkpoint rather than retraining GLIM.

Real EEG performed better than noise on several metrics. One limitation is that the authors' noise control also changes the padding mask, so the comparison does not isolate the EEG waveform alone. The score difference also does not tell us what percentage of the information comes from EEG.

The BART notebook uses different features and scoring methods. Its scores should not be compared directly with GLIM's multi-reference scores. The GLIM execution files, raw EEG, and model checkpoints are not included in this repo.

## References

- [Evaluating EEG-to-text models through noise-based performance analysis](https://www.nature.com/articles/s41598-025-29587-x) and [the authors' code](https://github.com/khu-aims/EEG-To-Text).
- [Original EEG-to-text preprocessing code](https://github.com/MikeWangWZHL/EEG-To-Text).
- [GLIM paper](https://arxiv.org/abs/2505.17099) and [code](https://github.com/justin-xzliu/GLIM), evaluated at commit `e1f202cb793cfe7292fbc0072a4c26a7dd0660d9`.
- [ZuCo data](https://osf.io/q3zws/).

The models come from the cited authors. My work here focuses on running the experiments, comparing the inputs, and analyzing the results.
