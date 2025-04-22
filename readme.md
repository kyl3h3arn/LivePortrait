## LivePortrait Google Cloud Platform Deployment Instructions </br> ITCS 6166 - Team 10 </br> Kyle Hearn, Aman Waghchoure, Emil Kozicki, Jake Pinos
-  The goal of this project was to select a service to then be deployed onto a cloud platform for others to access. We chose the service [LivePortrait.](https://github.com/KwaiVGI/LivePortrait/tree/main) Throughout the semester, we had the pleasure of learning the inner workings of LivePortrait, deepening our knowledge in python libraries, deep learning, and more.
-  After trying several services, we decided on Google Cloud Platform (GCP). They offer $300 in free credits for first time users, and allows GPU access upon request.
-  This READme will document the deployment process, for others to follow along with in the future.
## GCP Setup
![GCP Settings](https://github.com/kyl3h3arn/LivePortrait/blob/main/Screenshot%201.png)
## Deployment
> Make sure your system has [`git`](https://git-scm.com/), [`conda`](https://anaconda.org/anaconda/conda), and [`FFmpeg`](https://ffmpeg.org/download.html) installed. For details on FFmpeg installation, see [**how to install FFmpeg**](assets/docs/how-to-install-ffmpeg.md).

```bash
git clone https://github.com/KwaiVGI/LivePortrait
cd LivePortrait

# create env using conda
conda create -n LivePortrait python=3.10
conda activate LivePortrait
```

#### For Linux or Windows Users
[X-Pose](https://github.com/IDEA-Research/X-Pose) requires your `torch` version to be compatible with the CUDA version.

Firstly, check your current CUDA version by:
```bash
nvcc -V # example versions: 11.1, 11.8, 12.1, etc.
```

Then, install the corresponding torch version. Here are examples for different CUDA versions. Visit the [PyTorch Official Website](https://pytorch.org/get-started/previous-versions) for installation commands if your CUDA version is not listed:
```bash
# for CUDA 11.1
pip install torch==1.10.1+cu111 torchvision==0.11.2 torchaudio==0.10.1 -f https://download.pytorch.org/whl/cu111/torch_stable.html
# for CUDA 11.8
pip install torch==2.3.0 torchvision==0.18.0 torchaudio==2.3.0 --index-url https://download.pytorch.org/whl/cu118
# for CUDA 12.1
pip install torch==2.3.0 torchvision==0.18.0 torchaudio==2.3.0 --index-url https://download.pytorch.org/whl/cu121
# ...
```

**Note**: On Windows systems, some higher versions of CUDA (such as 12.4, 12.6, etc.) may lead to unknown issues. You may consider downgrading CUDA to version 11.8 for stability. See the [downgrade guide](https://github.com/dimitribarbot/sd-webui-live-portrait/blob/main/assets/docs/how-to-install-xpose.md#cuda-toolkit-118) by [@dimitribarbot](https://github.com/dimitribarbot).

Finally, install the remaining dependencies:
```bash
pip install -r requirements.txt
```

#### For macOS with Apple Silicon Users
The [X-Pose](https://github.com/IDEA-Research/X-Pose) dependency does not support macOS, so you can skip its installation. While Humans mode works as usual, Animals mode is not supported. Use the provided requirements file for macOS with Apple Silicon:
```bash
# for macOS with Apple Silicon users
pip install -r requirements_macOS.txt
```

### 2. Download pretrained weights 📥

The easiest way to download the pretrained weights is from HuggingFace:
```bash
# !pip install -U "huggingface_hub[cli]"
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"
```

If you cannot access to Huggingface, you can use [hf-mirror](https://hf-mirror.com/) to download:
```bash
# !pip install -U "huggingface_hub[cli]"
export HF_ENDPOINT=https://hf-mirror.com
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"
```

Alternatively, you can download all pretrained weights from [Google Drive](https://drive.google.com/drive/folders/1UtKgzKjFAOmZkhNK-OYT0caJ_w2XAnib) or [Baidu Yun](https://pan.baidu.com/s/1MGctWmNla_vZxDbEp2Dtzw?pwd=z5cn). Unzip and place them in `./pretrained_weights`.

Ensuring the directory structure is as or contains [**this**](assets/docs/directory-structure.md).

### 4. Gradio interface 🤗

We also provide a Gradio <a href='https://github.com/gradio-app/gradio'><img src='https://img.shields.io/github/stars/gradio-app/gradio'></a> interface for a better experience, just run by:

```bash
# For Linux and Windows users (and macOS with Intel??)
python app.py # humans mode

# For macOS with Apple Silicon users, Intel not supported, this maybe 20x slower than RTX 4090
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py # humans mode
```

We also provide a Gradio interface of animals mode, which is only tested on Linux with NVIDIA GPU:
```bash
python app_animals.py # animals mode 🐱🐶
```

You can specify the `--server_port`, `--share`, `--server_name` arguments to satisfy your needs!

🚀 We also provide an acceleration option `--flag_do_torch_compile`. The first-time inference triggers an optimization process (about one minute), making subsequent inferences 20-30% faster. Performance gains may vary with different CUDA versions.
```bash
# enable torch.compile for faster inference
python app.py --flag_do_torch_compile
```
**Note**: This method is not supported on Windows and macOS.

**Or, try it out effortlessly on [HuggingFace](https://huggingface.co/spaces/KwaiVGI/LivePortrait) 🤗**
