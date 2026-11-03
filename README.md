SB-ECC Colab Reproduction

A Google Colab reproduction of Score Based Error Correcting Code Decoder (SB-ECC), including training configurations, checkpoint information, evaluation logs, and BER/BLER results for five error-correcting codes.

This repository documents an independent reproduction based on the original authors' implementation. It is not the official SB-ECC repository; the checkpoints and measurements presented here were produced for this reproduction.
- Paper: Score Based Error Correcting Code Decoder
- Authors: Alon Helvits and Eliya Nachmani
- Original implementation: alonhelvits/SB-ECC
- Colab notebook: [SB_ECC.ipynb](./SB_ECC.ipynb)
- Checkpoints / download instructions: [SB_ECC_Results](./SB_ECC_Results/)
- Evaluation logs: [test_logs](./test_logs/)
1. Scope
The reproduction covers the following codes over a BPSK/AWGN channel:
Code	Block length n	Information length k	Code rate k/n
LDPC(49,24)	49	24	0.4898
BCH(63,45)	63	45	0.7143
LDPC(121,80)	121	80	0.6612
MacKay(96,48)	96	48	0.5000
CCSDS(128,64)	128	64	0.5000


All five codes were evaluated at Eb/N0 = 2, 3, 4, 5, and 6 dB. The measurements below are from the reproduction's evaluation output, not copied from the original paper.
2. Repository Contents
Path	Description
SB_ECC.ipynb	Colab workflow for setup, training, loading checkpoints, evaluation, and plotting.
SB_ECC_Results/	Checkpoint information and associated training results.
test_logs/LDPC49-24_paper.log	LDPC(49,24) evaluation output.
test_logs/BCH63-45_paper.log	BCH(63,45) evaluation output.
test_logs/LDPC121-80_paper.log	LDPC(121,80) output, including the separate 6 dB evaluation.
test_logs/MACKAY96-48_paper.log	MacKay(96,48) evaluation output.
test_logs/CCSDS128-64_paper.log	CCSDS(128,64) evaluation output.
figures/	Generated BER and BLER figures (if committed).
README.md	Setup, configurations, evaluation procedure, and results.


Note: The _paper.log suffix is only a filename convention. The logs contain measured results, not paper reference values. Make sure the two new log files are uploaded to the repository if you want their links to work on GitHub.
3. Quick Start in Google Colab
1. Open the Colab notebook.
2. Select a GPU runtime.
3. Run the setup and installation cells.
4. Train a decoder or load a previously trained checkpoint.
5. Run evaluation and plotting to produce BER/BLER results.
Download the original implementation in a Colab cell:
%cd /content
!git clone https://github.com/alonhelvits/SB-ECC.git
%cd /content/SB-ECC
!pip install -r requirements.txt
All paths and subsequent commands assume the working directory is /content/SB-ECC. The official implementation and this reproduction are separate repositories: cloning upstream does not automatically download this project's trained checkpoints, saved logs, or notebook modifications. Colab's /content storage is temporary, so back up results to persistent storage.
4. Training Configuration
The following settings were used for the recorded training runs:
Parameter	Value
Decoder layers (N_dec)	6
Hidden dimension (d_model)	128
Attention heads (h)	8
Learning rate (lr)	0.0005
Epochs	1500
Minimum noise level (sigma_min)	0.1
Maximum noise level (sigma_max)	0.8
Solver	Euler
Test batch size	2048
GPU index	0


Training batch sizes: LDPC(49,24) = 256; BCH(63,45) = 256; LDPC(121,80) = 128; MacKay(96,48) = 256; CCSDS(128,64) = 256. The last two values reflect the commands used in this reproduction; they should not be presented as an exact match to the paper's batch-size protocol for codes longer than 64 bits.
LDPC(49,24)
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
BCH(63,45)
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
LDPC(121,80)
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
MacKay(96,48)
!python Main.py \
    --gpus=0 \
    --code_type=MACKAY \
    --code_n=96 \
    --code_k=48 \
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
CCSDS(128,64)
!python Main.py \
    --gpus=0 \
    --code_type=CCSDS \
    --code_n=128 \
    --code_k=64 \
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
Training results are written under SB_ECC_Results/, typically in timestamped code-specific directories with a checkpoint file named best_model.
5. Checkpoints and Evaluation
To evaluate without retraining, obtain the reproduced checkpoints using [SB_ECC_Results](./SB_ECC_Results/) and place them beneath /content/SB-ECC/SB_ECC_Results/. Confirm each printed checkpoint path before starting inference.

The two additional training runs used these checkpoint locations:
/content/SB-ECC/SB_ECC_Results/MACKAY__Code_n_96_k_48__02_11_2026_11_18_45/best_model
/content/SB-ECC/SB_ECC_Results/CCSDS__Code_n_128_k_64__03_11_2026_00_56_38/best_model
These are runtime paths, not download links; verify that the files exist in the current Colab session.
Evaluation settings
Parameter	Value
Solver	Euler
Maximum solver steps (num_steps)	10
Eb/N0	2–6 dB in steps of 1 dB
Test batch size	2048
Data-loader workers	0
Random seed	42


The 10-step setting is a maximum budget; syndrome-based early stopping may reduce the actual number of steps. The stopping/sample limit behavior is controlled by the particular inference.py used with this reproduction.
- LDPC(49,24) and BCH(63,45): evaluate with --ebno_range 2,7 (upper endpoint exclusive).
- LDPC(121,80): evaluate with --ebno_range 2,6 for 2–5 dB, followed by a separate 6 dB run using --ebno_range 6 --max_samples 100000000.
- MacKay(96,48) and CCSDS(128,64): evaluate 2–6 dB with --ebno_range 2,7 and no explicit --max_samples in the recorded evaluation wrapper.
For example, the two additional codes can be evaluated using the reproduction's compatible inference script:
!python inference.py \
    --model_path /content/SB-ECC/SB_ECC_Results/MACKAY__Code_n_96_k_48__02_11_2026_11_18_45/best_model \
    --code_type MACKAY --code_n 96 --code_k 48 \
    --solver euler --num_steps 10 --ebno_range 2,7 \
    --test_batch_size 2048 --workers 0 --gpus 0 --seed 42

!python inference.py \
    --model_path /content/SB-ECC/SB_ECC_Results/CCSDS__Code_n_128_k_64__03_11_2026_00_56_38/best_model \
    --code_type CCSDS --code_n 128 --code_k 64 \
    --solver euler --num_steps 10 --ebno_range 2,7 \
    --test_batch_size 2048 --workers 0 --gpus 0 --seed 42
To save logs while streaming output, use the notebook's Python evaluation wrapper rather than running these commands alone. The recorded filenames are:
/content/SB-ECC/test_logs/LDPC49-24_paper.log
/content/SB-ECC/test_logs/BCH63-45_paper.log
/content/SB-ECC/test_logs/LDPC121-80_paper.log
/content/SB-ECC/test_logs/MACKAY96-48_paper.log
/content/SB-ECC/test_logs/CCSDS128-64_paper.log
The wrapper writes each model's log progressively. Re-running with open(..., "w") overwrites an existing file. Saving logs does not provide checkpoint-resume functionality for a partially completed evaluation.

Compatibility note: Some options used here may require the inference script included or adapted in the reproduction workflow. If a newly cloned upstream inference.py does not accept --num_steps, --ebno_range, or --max_samples, use the compatible notebook version before running the commands.
6. Metrics and Log Format
- BER (Bit Error Rate): incorrectly decoded bits divided by evaluated coded bits.
- BLER / FER (Block/Frame Error Rate): fraction of decoded codewords containing at least one bit error; the test code may label this metric FER.
- −ln(BER): negative natural logarithm of the BER. Larger values mean lower BER.
The logs contain Eb/N0-specific results and may include per-step decoder trajectories, sample counts, and run times. In Test EbN0=..., BER=[...], the final entry is used as the reported BER for that SNR. Similarly, when the logs contain BER_DDPM=[...] or FER_DDPM=[...], the final entry represents the final recorded step. The DDPM field names are inherited from the implementation even when the selected solver is Euler.
7. Reproduction Results
The following values are from measured evaluation results. They are not paper reference values.
7.1 Bit Error Rate (BER) — all five codes
Eb/N0 (dB)	LDPC(49,24)	BCH(63,45)	LDPC(121,80)	MacKay(96,48)	CCSDS(128,64)
2	2.456679e-02	3.566067e-02	3.030045e-02	2.07662783e-02	4.42011308e-02
3	5.491690e-03	1.061581e-02	4.574970e-03	2.94442736e-03	8.35667824e-03
4	6.133582e-04	1.615583e-03	1.662704e-04	1.66583430e-04	5.15762641e-04
5	2.776281e-05	1.159806e-04	1.099288e-06	3.55592945e-06	8.39626666e-06
6	5.446841e-07	2.815550e-06	1.570220e-09	8.37485000e-08	5.49208908e-08


Original three-code BER curves:
<img width="1337" height="410" alt="Measured BER for LDPC(49,24), BCH(63,45), and LDPC(121,80)" src="https://github.com/user-attachments/assets/6c93379b-f35b-4cad-9aed-04d9f67ae679" />
Additional MacKay and CCSDS BER curves:
<img width="936" height="410" alt="Measured BER for MacKay(96,48) and CCSDS(128,64)" src="https://github.com/user-attachments/assets/5330466d-e7c6-4e46-be5e-559bd80fcba2" />7.2 Negative log BER — paper-style reporting
The following table converts this reproduction's BER measurements using −ln(BER) and rounds the results to two decimal places.
Eb/N0 (dB)	LDPC(49,24)	BCH(63,45)	LDPC(121,80)	MacKay(96,48)	CCSDS(128,64)
2	3.71	3.33	3.50	3.87	3.12
3	5.20	4.55	5.39	5.83	4.78
4	7.40	6.43	8.70	8.70	7.57
5	10.49	9.06	13.72	12.55	11.69
6	14.42	12.78	20.27	16.30	16.72


The original paper's Table 1 focuses on 4, 5, and 6 dB. The 2 and 3 dB rows are additional measured points presented for completeness. This table is not a transcription of the paper's numbers.
7.3 Block Error Rate (BLER / FER)
Measured BLER for the original three codes:
Eb/N0 (dB)	LDPC(49,24)	BCH(63,45)	LDPC(121,80)
2	1.582330e-01	3.521006e-01	3.195950e-01
3	3.719906e-02	1.158721e-01	5.176778e-02
4	4.217740e-03	1.921237e-02	1.930237e-03
5	1.942292e-04	1.459031e-03	1.344632e-05
6	3.609935e-06	3.756010e-05	1.999964e-08


<img width="1337" height="410" alt="Measured BLER for LDPC(49,24), BCH(63,45), and LDPC(121,80)" src="https://github.com/user-attachments/assets/2008ee3d-ab7e-4410-ad40-9a7ef7cd529e" />Additional MacKay and CCSDS BLER curves:
<img width="936" height="410" alt="Measured BLER for MacKay(96,48) and CCSDS(128,64)" src="https://github.com/user-attachments/assets/e3d27b51-56c1-45e0-9094-6c3b44edfffa" />
The MacKay/CCSDS BLER chart is included as provided. A numerical BLER table is not included here because the corresponding exact FER values have not been supplied in text; no values are inferred from the plotted image.
7.4 Sample counts at 6 dB (original three-code runs)
Code	Evaluated codewords	Observed erroneous codewords
LDPC(49,24)	100,001,792	361
BCH(63,45)	13,338,624	501
LDPC(121,80)	100,001,792	2


Statistical limitation: At 6 dB, the LDPC(121,80) result is based on only two erroneous codewords. Its BER and BLER estimates therefore have substantial sampling uncertainty. The separate run reached its sample cap without accumulating 500 frame errors. More independent samples would be needed for a more precise estimate.
7.5 Reproduction observations
The additional MacKay(96,48) and CCSDS(128,64) runs extend the reproduction from three to five codes. BER decreases with increasing Eb/N0 for all five codes. Their measured results should be compared with the original paper only after accounting for training and evaluation configuration differences; in particular, the MacKay and CCSDS runs used a training batch size of 256.
8. Plotting and Outputs
After running the inference and plotting cells, the figures can be saved in /content/SB-ECC/figures/:
figures/SB_ECC_BER_Comparison.png
figures/SB_ECC_BER_Comparison.pdf
figures/SB_ECC_BLER_Comparison.png
figures/SB_ECC_BLER_Comparison.pdf
figures/SB_ECC_MACKAY_CCSDS_BER.png
figures/SB_ECC_MACKAY_CCSDS_BER.pdf
figures/SB_ECC_MACKAY_CCSDS_BLER.png
figures/SB_ECC_MACKAY_CCSDS_BLER.pdf
The plotting scripts read the measured test logs and should exclude Paper and PaperReference entries when preparing measured curves. Only draw an Eb/N0 point when a corresponding valid measured result is present; do not interpolate or invent missing evaluation records.
9. Reproducibility Notes
- Training from scratch is optional if a compatible checkpoint is already available.
- At high Eb/N0, frame errors are rare, so collecting sufficient error events can require a very large number of test codewords.
- A partially written log records progress, but the inference code does not automatically resume from the saved sample count.
- Download or back up logs and checkpoints before a Colab runtime reset.
- The recorded evaluation seed is 42; exact reproducibility can still depend on device, software versions, and independently trained checkpoints.
- For stricter reproducibility, record the upstream commit and Python/package versions for each run.
- Keep the Codes_DB matrices associated with the intended code type and dimensions.
- The commands above are for Colab (!python, %cd). In a terminal, use python and cd.
10. Acknowledgments
The SB-ECC method and original implementation are by Alon Helvits and Eliya Nachmani. Please credit and cite the original paper when using the method. This repository provides an independent Colab reproduction, training configurations, checkpoints, evaluation logs, and visualizations for five codes.

Original source: alonhelvits/SB-ECC. Retain upstream copyright and license notices when redistributing their code.
