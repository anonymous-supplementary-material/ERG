# ERG

## Preparing Data

Download the required data and evaluation tools in the following anonymous link and unzip to the project folder

```markdown
https://www.dropbox.com/s/yw71tl4x7i4gt9x/data.tar.gz
```

## Running Scripts

All running scripts is packaged into `scripts.zip`.
Download the `scripts.zip` in the repository and unzip to the project folder. 
Then running code in the following steps:

## Extraction

* Fine-tuning a BERT-based Style Classifier
```sh
sh train_extraction_flickr.sh
sh train_extraction_senticap.sh
```
* run the following script and output a experiment folder that saving **the extraction results**
```sh
# parameters:
# dataset: # choices=[flickr7k, senticap]
# bsz: # batch_size, it's up to your GPU memory size
# thr: # extraction proportion. default thr=0.25
# e.g. sh run_extraction.sh senticap 64 0.25
sh run_extraction.sh [dataset] [bsz] [thr]
```

## Retrieval

* Map all the images and texts into d-dimensional vectors using a pre-trained multi-modal transformer

  * Option-1:  extract the embeddings by yourself
      * before that, you should prepare the bottom-up image features at this [link](https://github.com/UKPLab/MMT-Retrieval/blob/master/documentation/image_features.md#download)
      * then, run the following command
        ```
        python extract_multimodal_retrieval_embeddings.py
        ```
  * Option-2: we have prepared extracted embeddings files, so you can skip the step if you've downloaded the required data.
* Retrieval and output a experiment folder containing retrieval results in csv format
```sh
sh retrieve_flickr.sh
sh retrieve_senticap.sh
```


## Generation

* Fine-tuning a GPT-2 caption generator
```sh
#exp: the name of experiment folder where the extraction results are saved.
#dataset: choices=[flickr7k, senticap]
#s0: style, choices=[humor, neg]
#s1: style, choices=[romantic, pos]
#e.g. sh train_gpt.sh exp-0320-1108# senticap neg pos
sh train_gpt.sh [exp] [dataset] [s0] [s1]
```
* inferring the augmented data and output a experiment folder containing inferring results in csv format
```sh
#exp: the name of experiment folder where the retrieval results of the corresponding style are saved.
#style: choices=[humor, romantic, neg, pos]
# e.g. sh gpt_infer.sh exp-0406-0701# humor
sh gpt_infer_flickr.sh [exp] [style]
sh gpt_infer_senticap.sh [exp] [style]
```
* evaluating the augmented data and output a experiment folder containing augmented data files in csv format
```sh
#exp: the name of experiment folder where the inferring results of the corresponding style are saved.
#style: choices=[humor, romantic, neg, pos]
# e.g. sh gpt_eval.sh exp-0406-0722# humor
sh gpt_eval.sh [exp] [style]
```

* open the `select_data.ipynb` and generate augmented data in `.txt` format to train various models on the downstream task

## Train and Test Experimental models on augmented data

* before training, you should prepare the bottom up image features.
  * the pretrained features can be download at this [link](https://github.com/UKPLab/MMT-Retrieval/blob/master/documentation/image_features.md#download)
* or you can extract pretrained features manually at this [link](https://github.com/peteanderson80/bottom-up-attention#pretrained-features)

> take the NIC model as a example.
>
> assume the augmented data is located in data/augdata
>
> e.g. data/augdata/aug_humor_train_flickr.txt

```sh
cd baseline/nic
```

* baseline (with original data)

```sh
sh run_base.sh
```

* augmented (with augmented data)

```sh
sh run_aug.sh
```

