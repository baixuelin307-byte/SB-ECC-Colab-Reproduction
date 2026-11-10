# Pretrained Checkpoints

[Google Drive – SB-ECC Pretrained Checkpoints](https://drive.google.com/drive/u/2/folders/1l--gKW_H3tu0OzElsqe2ym--Hrij-xRq)

This directory contains SB-ECC checkpoints trained for this reproduction using the original implementation and the training settings described in the paper. These are independently reproduced checkpoints and are not checkpoints released by the original authors.

## Supported Codes

A total of **14 error-correcting codes** are included in this reproduction, covering BCH, LDPC, Polar, MacKay LDPC, and CCSDS codes.

| **Code** | **Training Batch Size** |
|---|---|
| BCH(63,45) | 256 |
| BCH(63,36) | 256 |
| BCH(63,51) | 256 |
| LDPC(49,24) | 256 |
| LDPC(121,80) | 128 |
| LDPC(121,60) | 128 |
| LDPC(121,70) | 128 |
| POLAR(64,32) | 256 |
| POLAR(64,48) | 256 |
| POLAR(128,64) | 128 |
| POLAR(128,86) | 128 |
| POLAR(128,96) | 128 |
| MACKAY(96,48) | 256 |
| CCSDS(128,64) | 256 |

## Training Configuration

All models were trained for 1,500 epochs using the following configuration, with code-specific batch sizes as listed above.

| **Parameter** | **Value** |
|---|---|
| Decoder layers (`N_dec`) | 6 |
| Hidden dimension (`d_model`) | 128 |
| Attention heads (`h`) | 8 |
| Learning rate (`lr`) | 5e-4 |
| Training epochs | 1500 |
| Minimum noise level (`sigma_min`) | 0.1 |
| Maximum noise level (`sigma_max`) | 0.8 |
| Solver | Euler |
| Test batch size | 2048 |

## Checkpoint Information

Each model checkpoint is stored as `best_model`, corresponding to the checkpoint saved during training.

The checkpoints cover five code families:

- **BCH:** BCH(63,45), BCH(63,36), BCH(63,51)
- **LDPC:** LDPC(49,24), LDPC(121,80), LDPC(121,60), LDPC(121,70)
- **Polar:** POLAR(64,32), POLAR(64,48), POLAR(128,64), POLAR(128,86), POLAR(128,96)
- **MacKay LDPC:** MACKAY(96,48)
- **CCSDS:** CCSDS(128,64)

## Usage

To evaluate a pretrained model, download the corresponding checkpoint and specify its path using `--model_path`.

Make sure the following arguments match the selected checkpoint:

- `--code_type`: Error-correcting code family
- `--code_n`: Codeword length
- `--code_k`: Information length
- `--model_path`: Path to the pretrained checkpoint

The evaluation should use the corresponding code configuration and decoder settings.

## Reference

This reproduction is based on the [original SB-ECC repository](https://github.com/alonhelvits/SB-ECC).

All checkpoints were trained independently as part of this reproduction project and are provided for reproducibility and experimental evaluation.
