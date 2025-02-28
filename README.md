Here’s the Markdown content with explicit headings (`#`, `##`) and code block delimiters (```) as raw text, so you can easily copy and paste it into your Colab notebook or elsewhere. I’ve preserved the exact formatting you requested.

```
# AI Video Generation with Wan2.1

**Author**: Jad Tounsi El Azzoiani  
**GitHub**: [github.com/jadouse5](https://github.com/jadouse5/)  
**LinkedIn**: [linkedin.com/in/jad-tounsi-el-azzoiani-87499a21a/](https://www.linkedin.com/in/jad-tounsi-el-azzoiani-87499a21a/)  
**Date**: February 28, 2025

## Introduction

Hi, I’m Jad Tounsi El Azzoiani, and this project explores AI video generation with the open-source **Wan2.1** model by Wan-AI. Wan2.1 is a cutting-edge video generative model that creates high-quality videos from text prompts. I’m using the T2V-1.3B model (~8.19GB VRAM) to generate a 480p video of *"Two anthropomorphic cats in comfy boxing gear and bright gloves fighting intensely on a spotlighted stage"* in Google Colab’s free tier, tackling resource challenges along the way.

This notebook highlights Wan2.1’s power as an open-source tool for Text-to-Video and beyond, featuring innovations like visual text generation and a robust Video VAE. Join me as I set up, debug, and showcase AI video creation!

## Setup

Clone the Wan2.1 repo and install dependencies:

```
!git clone https://github.com/Wan-Video/Wan2.1.git
%cd Wan2.1
!pip install -r requirements.txt
!pip install huggingface_hub[cli]
```

Download the T2V-1.3B model:

```
!huggingface-cli download Wan-AI/Wan2.1-T2V-1.3B --local-dir ./Wan2.1-T2V-1.3B
!ls -lh ./Wan2.1-T2V-1.3B
```

## Overcoming RAM Limitations

Colab’s free tier (~12.8GB RAM) couldn’t handle the initial model load, so I added swap space:

```
# Create and enable 10GB swap file
!fallocate -l 10G /swapfile
!chmod 600 /swapfile
!mkswap /swapfile
!swapon /swapfile
!swapon -s  # Verify swap
```

## Running the Generation with Monitoring

Monitor RAM and GPU usage while generating the video:

```
import subprocess
import psutil
import time

cmd = 'python generate.py --task t2v-1.3B --size 832*480 --ckpt_dir ./Wan2.1-T2V-1.3B --offload_model True --t5_cpu --sample_shift 8 --sample_guide_scale 6 --prompt "Two anthropomorphic cats in comfy boxing gear and bright gloves fight intensely on a spotlighted stage."'
print("Starting command:", cmd)

process = subprocess.Popen(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)

while process.poll() is None:
    ram = psutil.virtual_memory()
    print(f"RAM: {ram.percent}% used, {ram.available / 1024**3:.2f} GB available")
    gpu_info = subprocess.check_output("nvidia-smi --query-gpu=memory.used,memory.total --format=csv,nounits,noheader", shell=True, text=True).strip().split(',')
    print(f"GPU Memory: {gpu_info[0]} MiB used / {gpu_info[1]} MiB total")
    time.sleep(5)

stdout, stderr = process.communicate()
print("Output:", stdout)
print("Errors:", stderr)
```
```

### Instructions
- Copy the entire block above.
- Paste it into a Colab Markdown cell (or a `.md` file).
- Colab will render the `#` and `##` as headings, bold the `**Author**` text, and format the code blocks with syntax highlighting (bash/python).
- Run the code sections in separate code cells to execute them.

Let me know if you need help running it or adding more sections!
