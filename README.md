<p>
  <h1>
    <img src="./assets/logo.png" height=120px align="right"/>
    M<sup>2</sup>UGen: Multi-modal Music Understanding and Generation with the Power of Large Language Models
  </h1>
</p>

[![PWC](https://img.shields.io/badge/%F0%9F%93%8E%20arXiv-Paper-red)](https://arxiv.org/abs/2311.11255)

This is the official repository for *[M<sup>2</sup>UGen: Multi-modal Music Understanding and Generation with the Power of Large Language Models](https://arxiv.org/abs/2308.11276)*.

## Introduction

The M<sup>2</sup>UGen model is a Music Understanding and Generation model that is capable of Music Question Answering and also Music Generation from texts, images, videos and audios, as well as Music Editing. The model utilizes encoders such as MERT for music understanding, ViT for image understanding and ViViT for video understanding and the MusicGen/AudioLDM2 model as the music generation model (music decoder), coupled with adapters and the LLaMA 2 model to make the model possible for multiple abilities. The model architecture is given in [**_m2ugen.py_**](./M2UGen/llama/m2ugen.py). 

<p align="center">
  <img src="./assets/M2UGen.png">
</p>

To train our model, we generate datasets using a music captioning and question answering model, i.e. the [MU-LLaMA](https://github.com/crypto-code/MU-LLaMA) model. The dataset generation methods are given in the [Datasets](./Datasets) folder. 

## Model Setup

We use Python 3.9.17 for this project and the library requirements are given in requirements.txt. Create a conda environment using
```
conda create --name <env> --file requirements.txt
```

For the working of our model, Facebook's LLaMA-2 model weights are required, details on obtaining these weights are given on [HuggingFace](https://huggingface.co/docs/transformers/main/model_doc/llama). 

The trained checkpoints for our model is available here:
- [M<sup>2</sup>UGen with MusicGen](https://huggingface.co/M2UGen/M2UGen-MusicGen)
- [M<sup>2</sup>UGen with AudioLDM2](https://huggingface.co/M2UGen/M2UGen-AudioLDM2)

The directory of the checkpoints folder can be organized as follows:
```
.
├── ...
├── M2UGen                
│   ├── ckpts
│   │   │── LLaMA
│   │   │   │── 7B
│   │   │   │   │── checklist.chk
│   │   │   │   │── consolidated.00.pth
│   │   │   │   │── params.json
│   │   │   │── llama.sh
│   │   │   │── tokenizer.model
│   │   │   │── tokenizer_checklist.chk
│   │   │── M2UGen-MusicGen
│   │   │   │── checkpoint.pth
│   │   │── M2UGen-AudioLDM2
│   │   │   │── checkpoint.pth
│   │   │── knn.index
└── ...
```

Once downloaded, the Gradio demo can be run using these checkpoints.

For model with MusicGen
```
python gradio_app.py --model ./ckpts/M2UGen-MusicGen --llama_dir ./ckpts/LLaMA-2 --music_decoder musicgen
```

For model with AudioLDM2
```
python gradio_app.py --model ./ckpts/M2UGen-AudioLDM2 --llama_dir ./ckpts/LLaMA-2 --music_decoder audioldm2
```

## Dataset Generation

We use the [MU-LLaMA](https://github.com/crypto-code/MU-LLaMA) and [MPT-7B](https://huggingface.co/mosaicml/mpt-7b-chat) models to generate the MUCaps, MUEdit, MUImge and MUVideo datasets. For each of the datasets, run the scripts in the folder [Datasets](./Datasets) in its numbered order to generate the datasets.

The datasets are also available for download here:
- [MUCaps](https://huggingface.co/datasets/M2UGen/MUCaps)
- [MUEdit](https://huggingface.co/datasets/M2UGen/MUEdit)
- [MUImage](https://huggingface.co/datasets/M2UGen/MUImage)
- [MUVideo](https://huggingface.co/datasets/M2UGen/MUVideo)

## Model Training

To train the M<sup>2</sup>UGen model, run the [**_train_musicgen.sh_**](./M2UGen/train_musicgen.sh) or [**_train_audioldm2.sh_**](./M2UGen/train_audioldm2.sh) script. The scripts are designed to train the model for all three stages with [MusicGen](https://huggingface.co/docs/transformers/model_doc/musicgen) and [AudioLDM2](https://huggingface.co/docs/diffusers/main/en/api/pipelines/audioldm2) music decoders respectively.

The main model architecture is given in [**_m2ugen.py_**](./M2UGen/llama/m2ugen.py) and the modified MusicGen and AudioLDM2 architectures are present within the [**_musicgen_**](./M2UGen/llama/musicgen/) and [**_audioldm2_**](./M2UGen/llama/audioldm2/) folders respectively. The [**_data_**](./M2UGen/data/) folder contains the python files to handle loading the dataset. The [**_dataset.py_**](./M2UGen/data/dataset.py) file will show the use of different datasets based on the training stage. The code for the training epochs are present in [**_engine_train.py_**](./M2UGen/engine_train.py).

## Model Testing

To test the M<sup>2</sup>UGen model, run [**_gradio_app.py_**](./M2UGen/gradio_app.py).

```
usage: gradio_app.py [-h] [--model MODEL] [--llama_type LLAMA_TYPE] [--llama_dir LLAMA_DIR]
                      [--mert_path MERT_PATH] [--vit_path VIT_PATH] [--vivit_path VIVIT_PATH]
                      [--knn_dir KNN_DIR] [--music_decoder MUSIC_DECODER]

optional arguments:
  -h, --help            show this help message and exit
  --model MODEL         Name of or path to M2UGen pretrained checkpoint
  --llama_type LLAMA_TYPE
                        Type of llama original weight
  --llama_dir LLAMA_DIR
                        Path to LLaMA pretrained checkpoint
  --mert_path MERT_PATH
                        Path to MERT pretrained checkpoint
  --vit_path VIT_PATH   Path to ViT pretrained checkpoint
  --vivit_path VIVIT_PATH
                        Path to ViViT pretrained checkpoint
  --knn_dir KNN_DIR     Path to directory with KNN Index
  --music_decoder MUSIC_DECODER
                        Decoder to use musicgen/audioldm2
```

## GPU requirements

For training, stage 1 and 2 use a single 32GB V100 GPU while stage 3 uses 2 32GB V100 GPUs. For inference, a single 32GB V100 GPU is used.

## Acknowledgements

This code contains elements from the following repo:
- [crypto-code/MU-LLaMA](https://github.com/crypto-code/MU-LLaMA)


## Cite our work
If you find this repo useful, please consider citing: 
```bibtex
@article{hussain2023m,
         title={M $\^{}$\{$2$\}$ $ UGen: Multi-modal Music Understanding and Generation with the Power of Large Language Models},
         author={Hussain, Atin Sakkeer and Liu, Shansong and Sun, Chenshuo and Shan, Ying},
         journal={arXiv preprint arXiv:2311.11255},
         year={2023}
}
```
