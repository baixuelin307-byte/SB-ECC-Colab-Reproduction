# SB-ECC Colab Reproduction

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/baixuelin307-byte/SB-ECC-Colab-Reproduction/blob/main/SB_ECC.ipynb)

A Google Colab reproduction of **Score Based Error Correcting Code Decoder (SB-ECC)**, including training commands, checkpoint information, evaluation logs, and BER/BLER results for three linear block codes.

This repository documents an independent reproduction based on the original authors' implementation. It is not the official SB-ECC repository, and the checkpoints provided here were trained for this reproduction.

- **Paper:** [Score Based Error Correcting Code Decoder](https://arxiv.org/abs/2605.28358)
- **Authors:** Alon Helvits and Eliya Nachmani
- **Original implementation:** [alonhelvits/SB-ECC](https://github.com/alonhelvits/SB-ECC)
- **Colab notebook:** [SB_ECC.ipynb](./SB_ECC.ipynb)
- **Checkpoint information and download instructions:** [SB_ECC_Results](./SB_ECC_Results/)
- **Evaluation logs:** [test_logs](./test_logs/)

## 1. Scope

The reproduction covers training and evaluation of the following codes over a BPSK/AWGN channel:

| Code | Block length `n` | Information length `k` | Code rate `k/n` |
| --- | ---: | ---: | ---: |
| LDPC(49,24) | 49 | 24 | 0.4898 |
| BCH(63,45) | 63 | 45 | 0.7143 |
| LDPC(121,80) | 121 | 80 | 0.6612 |

All three models were evaluated at **Eb/N0 = 2, 3, 4, 5, and 6 dB**. The reported results are taken from the completed evaluation logs, rather than values copied from the paper.

## 2. Repository Contents

| Path | Description |
| --- | --- |
| `SB_ECC.ipynb` | Colab workflow for setup, training, checkpoint selection, evaluation, and plotting. |
| `SB_ECC_Results/` | Checkpoint information and associated training-result material. See the directory for the available checkpoint files or download links. |
| `test_logs/LDPC49-24_paper.log` | Evaluation output for LDPC(49,24). |
| `test_logs/BCH63-45_paper.log` | Evaluation output for BCH(63,45). |
| `test_logs/LDPC121-80_paper.log` | Evaluation output for LDPC(121,80), including the separate 6 dB run. |
| `README.md` | Setup instructions, configurations, and reproduction results. |

The `_paper.log` suffix is a filename convention; it does not mean that the logged measurements were taken from the paper.

## 3. Quick Start in Google Colab

1. Open [SB_ECC.ipynb in Colab](https://colab.research.google.com/github/baixuelin307-byte/SB-ECC-Colab-Reproduction/blob/main/SB_ECC.ipynb).
2. Select a GPU runtime.
3. Run the repository-cloning and dependency-installation cells.
4. Choose either training from scratch or evaluation with an existing checkpoint.
5. Run the evaluation and plotting cells to generate BER and BLER figures.

### Download the original implementation

Run these commands in a Colab code cell:

```python
%cd /content
!git clone https://github.com/alonhelvits/SB-ECC.git
%cd /content/SB-ECC
!pip install -r requirements.txt
```

The implementation is downloaded into `/content/SB-ECC`. Training and evaluation commands below assume this working directory.

The original implementation and this reproduction are separate repositories. Cloning the original repository does **not** download the reproduction notebook, saved logs, or reproduced checkpoints. Obtain those from this repository and its checkpoint download instructions.

Colab's `/content` storage is temporary. Download or back up checkpoints, logs, and figures before the runtime is reset.

## 4. Training Configuration

The training commands used for this reproduction follow the settings described in the paper:

| Parameter | Value |
| --- | --- |
| Decoder layers (`N_dec`) | 6 |
| Hidden dimension (`d_model`) | 128 |
| Attention heads (`h`) | 8 |
| Learning rate (`lr`) | 0.0005 |
| Training epochs | 1500 |
| Minimum noise level (`sigma_min`) | 0.1 |
| Maximum noise level (`sigma_max`) | 0.8 |
| Solver | Euler |
| Test batch size | 2048 |
| GPU index | 0 |

The training batch size is **256** for LDPC(49,24) and BCH(63,45), and **128** for LDPC(121,80).

### LDPC(49,24)

```python
!python Main.py \
    --gpus=0 \
    --code_type=LDPC \
    --code_n=49 \
    --code_k=24 \
    --N_dec=6 \
    --d_model=128 \
    --h=8 \
    --lr=0.0005 \
    --batch_size=256 \
    --test_batch_size=2048 \
    --epochs=1500 \
    --solver=euler \
    --sigma_max=0.8 \
    --sigma_min=0.1
```

### BCH(63,45)

```python
!python Main.py \
    --gpus=0 \
    --code_type=BCH \
    --code_n=63 \
    --code_k=45 \
    --N_dec=6 \
    --d_model=128 \
    --h=8 \
    --lr=0.0005 \
    --batch_size=256 \
    --test_batch_size=2048 \
    --epochs=1500 \
    --solver=euler \
    --sigma_max=0.8 \
    --sigma_min=0.1
```

### LDPC(121,80)

```python
!python Main.py \
    --gpus=0 \
    --code_type=LDPC \
    --code_n=121 \
    --code_k=80 \
    --N_dec=6 \
    --d_model=128 \
    --h=8 \
    --lr=0.0005 \
    --batch_size=128 \
    --test_batch_size=2048 \
    --epochs=1500 \
    --solver=euler \
    --sigma_max=0.8 \
    --sigma_min=0.1
```

Training outputs are saved under `SB_ECC_Results/` in model-specific, timestamped directories. The saved checkpoint is named `best_model`.

## 5. Checkpoints and Evaluation

To evaluate without retraining, obtain the reproduced checkpoints using the information in [SB_ECC_Results](./SB_ECC_Results/). Place the model directories under `/content/SB-ECC/SB_ECC_Results/`, preserving the code identifiers in their directory names.

The notebook's evaluation cell searches recursively for files whose names start with `best_model`. It matches checkpoints to the code type and dimensions and selects the most recently modified matching file when multiple matches exist. Check the printed `[Checkpoint]` path to confirm that the intended model is selected.

### Evaluation settings

| Parameter | Value |
| --- | --- |
| Solver | Euler |
| Maximum solver steps (`num_steps`) | 10 |
| Eb/N0 | 2–6 dB, in 1 dB increments |
| Test batch size | 2048 |
| Data-loader workers | 0 |
| Evaluation random seed | 42 |

The evaluation uses the stopping logic implemented by the inference script. The 10-step setting is a maximum budget; syndrome-based early stopping can reduce the realized number of steps.

- **LDPC(49,24) and BCH(63,45):** `--ebno_range 2,7` evaluates 2, 3, 4, 5, and 6 dB. The upper endpoint is exclusive.
- **LDPC(121,80):** `--ebno_range 2,6` evaluates 2–5 dB, followed by a separate `--ebno_range 6 --max_samples 100000000` run.

Use the inference workflow provided in the notebook. The recorded evaluation supports `--num_steps`, `--ebno_range`, and `--max_samples`; if a freshly downloaded upstream inference script does not recognize these options, it must be brought into line with the notebook's evaluation setup before running the commands.

The separate LDPC(121,80) run caps evaluation at approximately 100 million codewords to limit runtime. Because evaluation processes complete batches, the recorded count is **100,001,792** codewords.

The evaluation cell streams output to the notebook and writes each model's results to `/content/SB-ECC/test_logs/`. Running the cell again opens these logs in write mode and replaces their previous contents, so back up logs that you want to retain.

## 6. Metrics and Log Format

- **BER:** total incorrectly decoded bits divided by total evaluated coded bits.
- **BLER:** fraction of decoded codewords containing at least one bit error. This is recorded as **FER** in the inference logs.
- **`-ln(BER)`:** negative natural logarithm of BER; a larger value indicates a smaller BER.

The logs include per-SNR results, per-step arrays, final summaries, sample counts, and evaluation time.

- In `Test EbN0=..., BER=[...]`, the list contains accumulated final BER results across the evaluated SNR points. Its last entry corresponds to the current SNR.
- In `BER_DDPM=[...]` and `FER_DDPM=[...]`, entries describe the decoder trajectory across solver steps. The final entry is the final reported metric at that SNR.
- The `DDPM` field names are retained from the implementation. In these runs, the selected solver is **Euler**, as shown in the run configuration.
- Final summary lines round the values. Use the detailed per-SNR records when greater numeric precision is needed.

## 7. Reproduction Results

The following tables summarize the completed test logs. Values are rounded for readability.

### Bit Error Rate (BER)

| Eb/N0 (dB) | LDPC(49,24) | BCH(63,45) | LDPC(121,80) |
| ---: | ---: | ---: | ---: |
| 2 | 2.456679e-02 | 3.566067e-02 | 3.030045e-02 |
| 3 | 5.491690e-03 | 1.061581e-02 | 4.574970e-03 |
| 4 | 6.133582e-04 | 1.615583e-03 | 1.662704e-04 |
| 5 | 2.776281e-05 | 1.159806e-04 | 1.099288e-06 |
| 6 | 5.446841e-07 | 2.815550e-06 | 1.570220e-09 |

<img width="1337" height="410" alt="BER versus Eb/N0 for LDPC(49,24), BCH(63,45), and LDPC(121,80)" src="https://github.com/user-attachments/assets/6c93379b-f35b-4cad-9aed-04d9f67ae679" />

### Block Error Rate (BLER / FER)

| Eb/N0 (dB) | LDPC(49,24) | BCH(63,45) | LDPC(121,80) |
| ---: | ---: | ---: | ---: |
| 2 | 1.582330e-01 | 3.521006e-01 | 3.195950e-01 |
| 3 | 3.719906e-02 | 1.158721e-01 | 5.176778e-02 |
| 4 | 4.217740e-03 | 1.921237e-02 | 1.930237e-03 |
| 5 | 1.942292e-04 | 1.459031e-03 | 1.344632e-05 |
| 6 | 3.609935e-06 | 3.756010e-05 | 1.999964e-08 |

<img width="1337" height="410" alt="BLER versus Eb/N0 for LDPC(49,24), BCH(63,45), and LDPC(121,80)" src="https://github.com/user-attachments/assets/2008ee3d-ab7e-4410-ad40-9a7ef7cd529e" />

### Sample counts at 6 dB

| Code | Evaluated codewords | Observed erroneous codewords |
| --- | ---: | ---: |
| LDPC(49,24) | 100,001,792 | 361 |
| BCH(63,45) | 13,338,624 | 501 |
| LDPC(121,80) | 100,001,792 | 2 |

**Statistical limitation:** the LDPC(121,80) result at 6 dB is based on only two erroneous codewords. Its BER and BLER estimates therefore have substantial sampling uncertainty. The sample-capped run completed, but it did not accumulate 500 frame errors at this operating point. Additional independent samples would be needed for a more precise estimate.

The results document this reproduction run. They should not be interpreted as exact numerical equality with every result in the original paper.

## 8. Plotting and Outputs

Run the notebook's plotting cells after evaluation, or place the provided logs in `/content/SB-ECC/test_logs/` to plot existing results without running inference again.

The plotting workflow saves:

- `figures/SB_ECC_BER_Comparison.png`
- `figures/SB_ECC_BER_Comparison.pdf`
- `figures/SB_ECC_BLER_Comparison.png`
- `figures/SB_ECC_BLER_Comparison.pdf`

Use the complete measured logs when regenerating figures. The numeric tables above reflect the recovered full evaluation output, including the measured 6 dB values for both LDPC codes.

## 9. Reproducibility Notes

- Training from scratch is optional if a compatible reproduced checkpoint is already available.
- High-SNR evaluation can take much longer because errors are rare and many codewords must be decoded.
- Evaluation seed 42 is recorded in the logs; it does not by itself guarantee identical results across software versions, devices, or independently trained checkpoints.
- The setup cell downloads the upstream repository's current default branch and installs its requirements. For stricter reproducibility, record the upstream commit and installed package versions alongside each new run.
- Checkpoint paths must refer to actual files in the active runtime. Paths from another Colab session may no longer exist.
- The Colab commands use `!python` and `%cd`. In a terminal, use `python` and `cd` instead.

## 10. Acknowledgments

The SB-ECC method and original implementation are by Alon Helvits and Eliya Nachmani. Please credit and cite their paper when using the method. This repository contributes a Colab reproduction workflow, independently trained checkpoints, and recorded evaluation results.

Original source: [alonhelvits/SB-ECC](https://github.com/alonhelvits/SB-ECC). Retain the original copyright and license notices when redistributing upstream code.
