# Colossal-AI

An integrated large-scale model training system with efficient parallelization techniques.

Paper: [Colossal-AI: A Unified Deep Learning System For Large-Scale Parallel Training](https://arxiv.org/abs/2110.14883)

Blog: [Colossal-AI: A Unified Deep Learning System For Large-Scale Parallel Training](https://www.hpcaitech.com/blog)

## Installation

### PyPI

```bash
pip install colossalai
```

### Install From Source

```shell
git clone git@github.com:hpcaitech/ColossalAI.git
cd ColossalAI
# install dependency
pip install -r requirements/requirements.txt

# install colossalai
pip install .
```

Install and enable CUDA kernel fusion (compulsory installation when using fused optimizer)

```shell
pip install -v --no-cache-dir --global-option="--cuda_ext" .
```

## Documentation

- [Documentation](https://www.colossalai.org/)

## Quick View

### Start Distributed Training in Lines

```python
import colossalai
from colossalai.engine import Engine
from colossalai.trainer import Trainer
from colossalai.core import global_context as gpc

model, train_dataloader, test_dataloader, criterion, optimizer, schedule, lr_scheduler = colossalai.initialize()
engine = Engine(
    model=model,
    criterion=criterion,
    optimizer=optimizer,
    lr_scheduler=lr_scheduler,
    schedule=schedule
)

trainer = Trainer(engine=engine,
                  hooks_cfg=gpc.config.hooks,
                  verbose=True)
trainer.fit(
    train_dataloader=train_dataloader,
    test_dataloader=test_dataloader,
    max_epochs=gpc.config.num_epochs,
    display_progress=True,
    test_interval=5
)
```

### Write a Simple 2D Parallel Model

Let's say we have a huge MLP model and its very large hidden size makes it difficult to fit into a single GPU. We can
then distribute the model weights across GPUs in a 2D mesh while you still write your model in a familiar way.

```python
from colossalai.nn import Linear2D
import torch.nn as nn


class MLP_2D(nn.Module):

    def __init__(self):
        super().__init__()
        self.linear_1 = Linear2D(in_features=1024, out_features=16384)
        self.linear_2 = Linear2D(in_features=16384, out_features=1024)

    def forward(self, x):
        x = self.linear_1(x)
        x = self.linear_2(x)
        return x

```

## Features

Colossal-AI provides a collection of parallel training components for you. We aim to support you to write your
distributed deep learning models just like how you write your single-GPU model. We provide friendly tools to kickstart
distributed training in a few lines.

- [Data Parallelism](./docs/parallelization.md)
- [Pipeline Parallelism](./docs/parallelization.md)
- [1D, 2D, 2.5D, 3D and sequence parallelism](./docs/parallelization.md)
- [Friendly trainer and engine](./docs/trainer_engine.md)
- [Extensible for new parallelism](./docs/add_your_parallel.md)
- [Mixed Precision Training](./docs/amp.md)
- [Zero Redundancy Optimizer (ZeRO)](./docs/zero.md)

## Cite Us

```
@article{bian2021colossal,
  title={Colossal-AI: A Unified Deep Learning System For Large-Scale Parallel Training},
  author={Bian, Zhengda and Liu, Hongxin and Wang, Boxiang and Huang, Haichen and Li, Yongbin and Wang, Chuanrui and Cui, Fan and You, Yang},
  journal={arXiv preprint arXiv:2110.14883},
  year={2021}
}
```
