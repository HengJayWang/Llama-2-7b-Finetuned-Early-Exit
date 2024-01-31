# Llama-2-7b-Finetuned-Early-Exit
> Experiment for fine-tuning the Llama-2-7b model for Early Exit inference.

## Description

This repository presents an experiment of LLaMA-2-7b fine-tuning with early exit purposes.

This repo extracted [modeling_llama.py](https://github.com/huggingface/transformers/blob/881e966aced6f0f208f43d7b7e7e55bc680f8fa5/src/transformers/models/llama/modeling_llama.py) from huggingface/transformers(commit 881e966) and injected **loss of intermediate layers (8/12/16/20/24 layers)** calculation within the training process.

## Environment

1. Use `startDocker.sh` to create the torch environment.

2. **(Optional)** `startJupyterLabOnly.sh` can be used to create a Jupyter Lab environment.

## Running the sample

To reproduce the experiment:

### 1. Fine-tuning with [mlabonne/guanaco-llama2](https://huggingface.co/datasets/mlabonne/guanaco-llama2) dataset ✅
> 💡 follow this [**blog post**](https://mlabonne.github.io/blog/posts/Fine_Tune_Your_Own_Llama_2_Model_in_a_Colab_Notebook.html)
1. Install the library in the `Fine_tune_Llama_2_with_Early_Exit_Finetuning.ipynb` first cell at first time.
2. Replace the `modeling_llama.py` in your python library by a soft link.
   - something like e.g. `ln -s /usr/local/lib/python3.10/dist-packages/transformers/models/llama/modeling_llama.py {project_folder}/modeling_llama.py`
3. Running the training script in `Fine_tune_Llama_2_with_Early_Exit_Finetuning.ipynb`, and it will save the merge model by `model.save_pretrained(new_model)` command.
4. Evaluating the new model with HELM MMLU scenario at locally.

### 2. Instruction-tuning with [Alpaca-lora](https://github.com/tloen/alpaca-lora) : Failed ⛔

1. Using the [Alpaca-lora](https://github.com/tloen/alpaca-lora) original project.
2. Install dependencies with `pip install -r requirements.txt`
3. Replace the `modeling_llama.py` in your python library by a soft link.
   - e.g. `ln -s /usr/local/lib/python3.10/dist-packages/transformers/models/llama/modeling_llama.py {project_folder}/modeling_llama.py`
4. Training with `finetune.py`
    ```bash
    python finetune.py \
        --base_model 'meta-llama/Llama-2-7b-hf' \
        --data_path 'yahma/alpaca-cleaned' \
        --output_dir './lora-alpaca-ee-1-epoch' \
        --batch_size 32 \
        --micro_batch_size 4 \
        --num_epochs 1
    ```
5. ⚠️ Encounter the gradient exploding during the training, need to find the solution or try different training hyperparameters.
