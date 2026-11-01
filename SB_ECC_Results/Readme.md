
# Pretrained Checkpoints
https://drive.google.com/drive/u/2/folders/1l--gKW_H3tu0OzElsqe2ym--Hrij-xRq
This directory contains SB-ECC checkpoints trained for this reproduction using the original implementation and the training settings described in the paper. These are not checkpoints released by the original authors.

## Supported Codes

| Code | Training Batch Size |
| --- | ---: |
| BCH(63,45) | 256 |
| LDPC(49,24) | 256 |
| LDPC(121,80) | 128 |

## Training Configuration

| Parameter | Value |
| --- | --- |
| Decoder layers (`N_dec`) | 6 |
| Hidden dimension (`d_model`) | 128 |
| Attention heads (`h`) | 8 |
| Learning rate (`lr`) | 5e-4 |
| Training epochs | 1500 |
| Minimum noise level (`sigma_min`) | 0.1 |
| Maximum noise level (`sigma_max`) | 0.8 |
| Solver | Euler |
| Test batch size | 2048 |

## Usage

Each model folder contains a `best_model` checkpoint. To evaluate a model, set `--model_path` to the corresponding checkpoint and select the matching `--code_type`, `--code_n`, and `--code_k`.

## Reference

This reproduction is based on the [original SB-ECC repository](https://github.com/alonhelvits/SB-ECC).
