---
title:       "Accelerating PyTorch on Apple Silicon"
description: ""
tags:        [Python, Pytorch]
redirect_from:
 - /2024/06/03/pytorch-on-m1.html
---



- TOC
{:toc}

## Actionable Takeaways
The Apple Silicon GPUs are supported by default.

Code to enable[^1]:

```
mps_device = torch.device("mps")

# Create a Tensor directly on the mps device
x = torch.ones(5, device=mps_device)

# Or
x = torch.ones(5, device="mps")

# Any operation happens on the GPU
y = x * 2

# Move your model to mps just like any other device
model = YourFavoriteNet()
model.to(mps_device)

# Now every call runs on the GPU
pred = model(x)
```

Or detect and select the best available device automatically:

```
device = (
    "cuda"
    if torch.cuda.is_available()
    else "mps"
    if torch.backends.mps.is_available()
    else "cpu"
)

print(f"Using {device} device")

mps_device = torch.device(device)

# Create a Tensor directly on the mps device
x = torch.ones(5, device=mps_device)
```





[^1]: [StackOverflow - How to run Pytorch on Mackbook Pro (M1) GPU](https://stackoverflow.com/a/72293634)  
