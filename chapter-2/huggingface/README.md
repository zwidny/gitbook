# 如何在资源有限的情况下对大型模型进行推理

本文主要介绍在大模型推理时， 当遇到单卡无法完全加载大模型时， 如何使大模型工作的步骤， 主要使用的技术为[accelerate](https://huggingface.co/docs/accelerate/usage_guides/big_modeling)

本文将已[meditron-7b](https://huggingface.co/epfl-llm/meditron-7b/tree/main)为例

## 先决条件

1. 安装了accelerate

    ```shell
    pip install 'accelerate==0.25'
    ```

1. 安装了transformers
   
    ```
    pip install transformers
    ```

## 完整示例
```python
import logging

import torch
from accelerate import (
    infer_auto_device_map, init_empty_weights, load_checkpoint_and_dispatch
)
from huggingface_hub import hf_hub_download
# 根据模型config.json确认其为`LlamaForCausalLM`架构
from transformers import LlamaConfig, LlamaForCausalLM, LlamaTokenizer

logging.basicConfig(level=logging.INFO)



checkpoint = "epfl-llm/meditron-7b"
# Download the Weights
weights_location = hf_hub_download(checkpoint, "model.safetensors.index.json")
config = LlamaConfig.from_pretrained(checkpoint)
with init_empty_weights():
    # 加载到meta设备中，不需要耗时，不需要消耗内存和显存
    model = LlamaForCausalLM._from_config(config, torch_dtype=torch.float16)


# 每张卡最大允许使用15G显存
MAX_MEMORY_PER = '15Gib'
CUDA_LIST = [0, 1, 2, 3, 4, 5]
memory_conf = {int(cuda): MAX_MEMORY_PER for cuda in CUDA_LIST}
device_map = infer_auto_device_map(
    model, max_memory=memory_conf, no_split_module_classes=LlamaForCausalLM._no_split_modules)  # 自动划分每个层的设备
logging.info(F"device_map: {device_map}")


# Load the checkpoint and dispatch it to the right devices
model = load_checkpoint_and_dispatch(
    model, weights_location, device_map=device_map
)
tokenizer = LlamaTokenizer.from_pretrained(checkpoint)


# 开始推理
torch.set_grad_enabled(False)
msg = ''
inputs = tokenizer("Cold, fever of 40 degrees", return_tensors="pt")
inputs = inputs.to(model.device)

# 设置attention_mask和pad_token_id
attention_mask = torch.ones(
    inputs["input_ids"].shape, dtype=torch.long, device=model.device)
pad_token_id = tokenizer.pad_token_id
output = model.generate(inputs["input_ids"], max_new_tokens=32*10,
                        attention_mask=attention_mask, pad_token_id=pad_token_id)
decoded_output = tokenizer.decode(output[0].tolist())
print(decoded_output)

```

### 1. 确认模型架构

一般大模型会有一个config.json


