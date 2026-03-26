# APEXGo [![DOI](https://zenodo.org/badge/887529555.svg)](https://doi.org/10.5281/zenodo.19241740)
Official implementation of APEXGo method from the paper [A generative artificial intelligence approach for antibiotic optimization](https://www.biorxiv.org/content/10.1101/2024.11.27.625757v1). This repository includes base code to run APEXGo on all templates from the paper.


## Environment setup

A Docker image with the requirements is provided at ```yimengzeng/apexgo:v1```, the Dockerfile is also provided. To run optimization, first start the docker image with the following command for optimization
```shell
docker run -it -v ~/APEXGo/optimization/:/workspace/ --gpus 'device=0' yimengzeng/apexgo:v1
```

or 

```shell
docker run -it -v ~/APEXGo/generation/:/workspace/ --gpus 'device=0' yimengzeng/apexgo:v1
```
to train the VAE used for latent space optimization.


For a local setup using conda, run the following:
```shell
conda create --name apexgo python=3.10
conda activate apexgo
conda install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
pip install tqdm==4.65.0 wandb==0.18.6 botorch==0.12.0 selfies==2.1.2 guacamol==0.5.5 rdkit==2024.3.6 lightning==2.4.0 joblib==1.4.2 fire==0.7.0 levenshtein==0.26.1 rotary_embedding_torch==0.8.5 gpytorch==1.13 pandas==2.2.3 numpy==1.24.3 fcd_torch==1.0.7 matplotlib==3.9.2
```

This is tested on Debian GNU/Linux 11 (bullseye) with a NVIDIA RTX A6000 with driver version 535.86.10 and CUDA driver version 12.2, and a Intel(R) Xeon(R) Gold 6342 CPU, installation should take no more than 5~10 minutes with the correct setup.

## How to use APEXGo

**Reproduce Paper Results & Run a Demo Optimization**

To reproduce the optimization results from the paper, you can run a demonstration script that optimizes a template peptide. A pretrained VAE model is included in the repository and will be loaded automatically.

+ Navigate to the ```optimization/constrained_bo_scripts``` directory.
+ Start Docker with the optimization workspace mounted:
    ```shell
    docker run -it -v ~/APEXGo/optimization/:/workspace/ --gpus 'device=0' yimengzeng/apexgo:v1
    ```

+ Run the script:
    ```bash
    bash optimize_gramnegative_only.sh
    ```

This script, optimize_gramnegative_only.sh, runs an example optimizing template 8 ("GHLLIHLIGKATLAL") for gram-negative bacteria, with a similarity of at least 75% to the template, producing 20 different optimized peptides. Remember to replace ```YOUR_WANDB_ENTITY``` in the script with your own wandb user ID to log results.

**Optimize Your Own Peptide**

You can adapt the demo script to optimize your own custom peptide sequence.

+ Navigate to the [scripts directory](./optimization/constrained_bo_scripts/).
+ Modify the script: In the [optimize_gramnegative_only.sh](./optimization/constrained_bo_scripts/optimize_gramnegative_only.sh) script, change the --constraint_types argument to your desired seed peptide for optimization.

Results, including the final diverse set of peptides and their APEX oracle scores will be uploaded to your wandb account and also saved locally in ```APEXGo/optimization/constrained_bo_scripts/optimization_all_collected_data```.

**Train the VAE Model from Scratch (Optional)**

If you wish to train the Variational Autoencoder (VAE) from scratch instead of using the provided pretrained model:

+ Download the training data from [here](https://drive.google.com/file/d/1WZyR-UZ78jktdO-w2yeKEVT-Bgfe9QRo/view?usp=sharing).
+ Place the downloaded CSV into the ```APEXGo/generation/data``` directory.
+ Start Docker with the generation workspace mounted:
    ```shell
    docker run -it -v ~/APEXGo/generation/:/workspace/ --gpus 'device=0' yimengzeng/apexgo:v1
    ```
+ Run the training script:
    ```bash
    bash train.sh
    ```
You can find the script at: ```APEXGo/generation/train.sh```. Remember to replace YOUR_WANDB_ENTITY in the script with your wandb user ID. Checkpoints will be saved locally in ```APEXGo/generation/saved_models```.
