---
layout: default
---

# Training the Model

This chapter covers the high‑level process of training or adapting a model for your autonomous pentesting agent.


---


## Training dataset source code

Dependencies requiremente.txt 
```
unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git
unsloth_zoo
transformers>=4.47.0
datasets
trl>=0.12.0
peft
accelerate
torch>=2.4.0
torchvision
torchaudio
gguf
bitsandbytes
sentencepiece
```

how-to install:

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124

pip install -r requirements.txt
```



Training code:

```python
from unsloth import FastLanguageModel
import torch
from datasets import load_dataset
from trl import SFTTrainer
from transformers import TrainingArguments

max_seq_length = 512
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name = "unsloth/Qwen2.5-1.5B-bnb-4bit", 
    max_seq_length = max_seq_length,
    load_in_4bit = True,
)

model = FastLanguageModel.get_peft_model(
    model,
    r = 16,
    target_modules = ["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
    lora_alpha = 16,
    lora_dropout = 0,
    bias = "none",
    use_gradient_checkpointing = "unsloth", 
)


alpaca_prompt = """### Instrucción:
{}

### Respuesta:
{}"""

def format_prompts(examples):
    texts = [alpaca_prompt.format(i, o) + tokenizer.eos_token for i, o in zip(examples["instruction"], examples["output"])]
    return { "text" : texts }

dataset = load_dataset("json", data_files={"train": "dataset_final_ultra.jsonl"}, split="train")
dataset = dataset.map(format_prompts, batched = True)


trainer = SFTTrainer(
    model = model,
    tokenizer = tokenizer,
    train_dataset = dataset,
    dataset_text_field = "text",
    max_seq_length = max_seq_length,
    args = TrainingArguments(
        per_device_train_batch_size = 2, 
        gradient_accumulation_steps = 4,
        num_train_epochs = 4,            
        learning_rate = 4e-5,
        fp16 = True,
        logging_steps = 1,
        output_dir = "nmap_1.5b_outputs",
        optim = "adamw_8bit",
        weight_decay = 0.01,
        lr_scheduler_type = "cosine",
        seed = 3407,
    ),
)

trainer.train()

# save your model after training
model.save_pretrained_gguf("nmap_expert_1.5b", tokenizer, quantization_method = "q4_k_m")

```

## Training execution

clean the environment
``` bash
rm -rf nmap_1.5b_outputs/
rm -rf nmap_expert_1.5b/
rm -rf llama.cpp/
rm -rf ~/.cache/huggingface/hub/models--unsloth--Qwen2.5-1.5B-bnb-4bit
```

```bash
python full_1-5.training.py 
🦥 Unsloth: Will patch your computer to enable 2x faster free finetuning.
🦥 Unsloth Zoo will now patch everything to make training faster!
==((====))==  Unsloth 2026.2.1: Fast Qwen2 patching. Transformers: 4.57.6.
   \\   /|    NVIDIA GeForce GTX 1650 with Max-Q Design. Num GPUs = 1. Max memory: 3.628 GB. Platform: Linux.
O^O/ \_/ \    Torch: 2.10.0+cu128. CUDA: 7.5. CUDA Toolkit: 12.8. Triton: 3.6.0
\        /    Bfloat16 = FALSE. FA [Xformers = None. FA2 = False]
 "-____-"     Free license: http://github.com/unslothai/unsloth
Unsloth: Fast downloading is enabled - ignore downloading bars which are red colored!
Unsloth 2026.2.1 patched 28 layers with 28 QKV layers, 28 O layers and 28 MLP layers.
Unsloth: Tokenizing ["text"] (num_proc=7): 100%|█████████████████████████████████████████| 265/265 [00:02<00:00, 113.68 examples/s]
==((====))==  Unsloth - 2x faster free finetuning | Num GPUs used = 1
   \\   /|    Num examples = 265 | Num Epochs = 4 | Total steps = 136
O^O/ \_/ \    Batch size per device = 2 | Gradient accumulation steps = 4
\        /    Data Parallel GPUs = 1 | Total batch size (2 x 4 x 1) = 8
 "-____-"     Trainable parameters = 18,464,768 of 1,562,179,072 (1.18% trained)
{'loss': 2.5954, 'grad_norm': 1.8519741296768188, 'learning_rate': 4e-05, 'epoch': 0.03}                                           
{'loss': 3.1788, 'grad_norm': 2.093881368637085, 'learning_rate': 3.999466416223275e-05, 'epoch': 0.06}                            
{'loss': 3.1019, 'grad_norm': 1.867811679840088, 'learning_rate': 3.9978659496047456e-05, 'epoch': 0.09}                           
  2%|██                                                                                            | 3/136 [00:44<31:10, 14.07s/it]

```

## Meaning of parameters during the trainign  process

```bash
{'loss': 3.1788, 'grad_norm': 2.093881368637085, 'learning_rate': 3.999466416223275e-05, 'epoch': 0.06}                            
{'loss': 3.1019, 'grad_norm': 1.867811679840088, 'learning_rate': 3.9978659496047456e-05, 'epoch': 0.09}   
```

| Parameter       | Meaning                                                                    | Observed Value        | Is It OK?                                                      | What Would Indicate a Problem?                                                                                                       |
| --------------- | -------------------------------------------------------------------------- | --------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `loss`          | Model error (how wrong the predictions are). It should decrease over time. | 3.1788 → 3.1019       | ✅ Yes. It is decreasing, which indicates proper learning.      | ❌ If it increases consistently, oscillates heavily, becomes NaN, or decreases in training but increases in validation (overfitting). |
| `grad_norm`     | Total gradient magnitude (size of the weight update).                      | 2.09 → 1.86           | ✅ Yes. Stable and within a normal range.                       | ❌ If it becomes extremely large (exploding gradients), extremely small (vanishing gradients), or unstable across steps.              |
| `learning_rate` | Step size used to update model weights.                                    | 3.999e-05 → 3.997e-05 | ✅ Yes. Smooth decay suggests a proper scheduler is being used. | ❌ If it is too high (causing unstable loss spikes), too low (very slow learning), or not decaying when a scheduler is expected.      |
| `epoch`         | Fraction of the full dataset processed.                                    | 0.06 → 0.09 (6% → 9%) | ✅ Normal. Early stage of training.                             | ❌ If training stops improving after many epochs (underfitting) or if performance worsens after several epochs (overfitting).         |


Finish process :

```bash
{'loss': 1.1047, 'grad_norm': 4.277770042419434, 'learning_rate': 5.335837767255214e-09, 'epoch': 4.0}                                  
{'train_runtime': 1594.0583, 'train_samples_per_second': 0.665, 'train_steps_per_second': 0.085, 'train_loss': 1.2656524716054691, 'epoch': 4.0}
100%|█████████████████████████████████████████████████████████████████████████████████████████████████| 136/136 [26:34<00:00, 11.72s/it]
Unsloth: Merging model weights to 16-bit format...
Found HuggingFace hub cache directory: /home/f0ns1/.cache/huggingface/hub
Checking cache directory for required files...
Cache check failed: model.safetensors not found in local cache.
Not all required files found in cache. Will proceed with downloading.
Checking cache directory for required files...
Cache check failed: tokenizer.model not found in local cache.
Not all required files found in cache. Will proceed with downloading.
model.safetensors: 100%|███████████████████████████████████████████████████████████████████████████| 3.09G/3.09G [01:18<00:00, 39.3MB/s]
Unsloth: Preparing safetensor model files: 100%|██████████████████████████████████████████████████████████| 1/1 [01:19<00:00, 79.07s/it]
Note: tokenizer.model not found (this is OK for non-SentencePiece models)
Unsloth: Merging weights into 16bit: 100%|████████████████████████████████████████████████████████████████| 1/1 [00:13<00:00, 13.62s/it]
Unsloth: Merge process complete. Saved to `/home/f0ns1/MASTER_IAS/Modulo4/nmap_expert_1.5b`
```



---

[back](./)
