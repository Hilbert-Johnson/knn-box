# :card_file_box: kNN-box
[![GitHub license](https://badgen.net/github/license/Naereen/Strapdown.js)](https://github.com/Naereen/StrapDown.js/blob/master/LICENSE) [![made-with-python](https://img.shields.io/badge/Made%20with-Python-1f425f.svg)](https://www.python.org/) [![Active](http://img.shields.io/badge/Status-Active-green.svg)](https://tterb.github.io) [![Ask Me Anything !](https://img.shields.io/badge/Ask%20me-anything-1abc9c.svg)](https://GitHub.com/Naereen/ama)

kNN-box is an open-source toolkit to build kNN-MT models. We take inspiration from the code of [kNN-LM](https://github.com/urvashik/knnlm) and [adaptive kNN-MT](https://github.com/zhengxxn/adaptive-knn-mt), and develope this more extensible toolkit based on [fairseq](https://github.com/facebookresearch/fairseq). Via kNN-box, users can easily implement different kNN-MT baseline models and further develope new models.

- [:card\_file\_box: kNN-box](#card_file_box-knn-box)
  - [Features](#features)
  - [Requirements and Installation](#requirements-and-installation)
  - [Overview](#overview)
  - [Visualization](#visualization)
  - [Benchmark](#benchmark)
    - [Dataset and Base Model](#dataset-and-base-model)
    - [Datastore Size](#datastore-size)
    - [BLEU Score](#bleu-score)
  - [Contributor](#contributor)

## Features
* :dart: easy-to-use: a few lines of code to deploy a kNN-MT model
* :telescope: research-oriented: provide implementations of various papers
* :building_construction: extensible: easy to develope new kNN-MT models with our toolkit.
* :bar_chart:visualized: the whole translation process of the kNN-MT can be visualized

## Requirements and Installation
* python >= 3.7
* pytorch >= 1.10.0
* faiss-gpu >= 1.7.3
* sacremoses == 0.0.41
* sacrebleu == 1.5.1
* fastBPE == 0.1.0
* streamlit >= 1.13.0
* scikit-learn >= 1.0.2
* seaborn >= 0.12.1

You can install this toolkit by
```shell
git clone git@github.com:NJUNLP/knn-box.git
cd knn-box
pip install --editable ./
```

Note: Installing faiss with pip is not suggested. For stability, we recommand you to install faiss with conda

```bash
CPU version only:
conda install faiss-cpu -c pytorch

GPU version:
conda install faiss-gpu -c pytorch # For CUDA
```

## Overview
Basically, there are two steps for runing a kNN-MT model: building datastore and translating with datastore. In this toolkit, we unify different kNN-MT variants into a single framework, albeit they manipulate datastore in different ways. Specifically, the framework consists of three modules (basic class):
* **datastore**: save translation knowledge in key-values pairs
* **retriever**: retrieve useful translation knowledge from the datastore
* **combiner**: produce final prediction based on retrieval results and NMT model

Users can easily develope different kNN-MT models by customizing three modules. This toolkit also provide example implementations of various popular kNN-MT models (listed below) and push-button scripts to run them, enabling researchers conveniently reproducing their experiment results:

<!-- In this toolkit, we provide reference implementations of various papers: -->
* [ Nearest Neighbor Machine Translation (Khandelwal et al., 2021)](https://openreview.net/pdf?id=7wCBOfJ8hJM)
* [ Adaptive Nearest Neighbor Machine Translation (Zheng et al., 2021)](https://aclanthology.org/2021.acl-short.47.pdf)
* [ Learning Kernel-Smoothed Machine Translation with Retrieved Examples (Jiang et al., 2021)](https://aclanthology.org/2021.emnlp-main.579.pdf)
* [ Efficient Machine Translation Domain Adaptation (PH Martins et al., 2022) ](https://aclanthology.org/2022.spanlp-1.3.pdf)
* [ Efficient Cluster-Based k-Nearest-Neighbor Machine Translation (Wang et al., 2022)](https://aclanthology.org/2022.acl-long.154.pdf)

<details>
<summary><b><ins>Preparation: download pretrained models and dataset</ins></b></summary>

You can prepare pretrained models and dataset by executing the following command:

```bash
cd knnbox-scripts
bash prepare_dataset_and_model.sh
```

> use bash instead of sh. If you still have problem running the script, you can manually download the [wmt19 de-en single model](https://github.com/facebookresearch/fairseq/blob/main/examples/wmt19/README.md) and [multi-domain de-en dataset](https://drive.google.com/file/d/18TXCWzoKuxWKHAaCRgddd6Ub64klrVhV/view), and put them into correct directory (you can refer to the path in the script).
</details>
<details>
<summary><b><ins>Run base neural machine translation model (our baseline)</ins></b></summary>
To translate using base neural model, execute the following command:

```bash
cd knnbox-scripts/base-nmt
bash inference.sh
```
</details>
<details>
<summary><b><ins>Run vanilla knn-mt</ins></b></summary>

To translate using knn-mt, execute the following command:

```bash
cd knnbox-scripts/vanilla-knn-mt
# step 1. build datastore
bash build_datastore.sh
# step 2. inference
bash inference.sh
```
</details>
<details>
<summary><b><ins>Run adaptive knn-mt</ins></b></summary>

To translate using adaptive knn-mt, execute the following command:

```bash
cd knnbox-scripts/adaptive-knn-mt
# step 1. build datastore
bash build_datastore.sh
# step 2. train meta-k network
bash train_metak.sh
# step 3. inference
bash inference.sh
```
</details>
<details>
<summary><b><ins>Run kernel smoothed knn-mt</ins></b></summary>

To translate using kernel smoothed knn-mt, execute the following command:

```bash
cd knnbox-scripts/kernel-smoothed-knn-mt
# step 1. build datastore
bash build_datastore.sh
# step 2. train kster network
bash train_kster.sh
# step 3. inferece
bash inference.sh
```
</details>
<details>
<summary><b><ins>Run greedy merge knn-mt</ins></b></summary>

implementation of [ Efficient Machine Translation Domain Adaptation (PH Martins et al., 2022) ](https://aclanthology.org/2022.spanlp-1.3.pdf)

To translate using Greedy Merge knn-mt, execute the following command:

```bash
cd knnbox-scripts/greedy-merge-knn-mt
# step 1. build datastore and prune using greedy merge method
bash build_datastore_and_prune.sh
# step 2. inferece (You can decide whether to use cache by --enable-cache)
bash inference.sh
```
</details>

</details>
<details>
<summary><b><ins>Run pck knn-mt</ins></b></summary>

implementation of [ Efficient Cluster-Based k-Nearest-Neighbor Machine Translation (Wang et al., 2022)](https://aclanthology.org/2022.acl-long.154.pdf)

To translate using pck knn-mt, execute the following command:

```bash
cd knnbox-scripts/pck-knn-mt
# step 1. build datastore 
bash build_datastore.sh
# step 2. prune datastore (size and dimension)
bash prune_datastore.sh
# step 3. train meta-k network
bash train_metak.sh
# step 4. inference
bash inference.sh
```
</details>

<img src="https://s1.ax1x.com/2022/11/22/z3Cpiq.png" width="80%" alt="design">


## Visualization
With kNN-box, you can even visualize the whole translation process of your kNN-MT model.
You can launch the visualization service by running the following commands. Have fun with it!
<!--with knnbox, you can easily obtain a web page to visualize the kNN-MT translation process interactively, and the web page will also display some useful information about the constructed datastore.-->

```bash
cd knnbox-scripts/vanilla-knn-mt-visual
# step 1. build datastore for visualization (save more information for visualization)
bash build_datastore_visual.sh
# step 2. configure the model that you are going to visualize
vim model_configs.yml 
# step 3. launch the web page
bash start_app.sh
```

![](https://s1.ax1x.com/2022/11/25/zY3djf.png)

## Benchmark

Here are the results obtained by using the knnbox toolkit to reproduce popular papers.

### Dataset and Base Model

we use the script mentioned above to download preprocessed OPUS multi-domain De-En dataset
and pretrained WMT19 De-En winner model. If you are interested in the general domain dataset for training pretrained models, download it from [here](https://www.statmt.org/wmt19/translation-task.html).

### Datastore Size

<table>
  <tr>
    <th align="center">Domain</th>
    <td align="center">IT</td>
    <td align="center">Medical</td>
    <td align="center">Koran</td>
    <td align="center">Law</td>
  </tr>
  <tr>
    <th align="center">Size</th>
    <td align="center">3602862</td>
    <td align="center">6903141</td>
    <td align="center">524374</td>
    <td align="center">19061382</td>
  </tr>
</table>

### BLEU Score

|model \ domain|IT|Medical|Koran|Law|General Domain (Avg)|
|:--:|:--:|:--:|:--:|:--:|:--:|
|Base NMT|38.35|40.06|16.26|45.48|37.16|
|vanilla kNN-MT|45.88|54.13|20.51|61.09|11.98|
|adaptive kNN-MT|47.41|56.15|20.20|63.01|32.70|
|kernel smoothed kNN-MT|47.81|56.67|20.16|63.27|32.60|

> Note that due to the unavoidable randomness in constructing the faiss index, there will be slight differences between our results and the paper's, and our results will not be exactly the same as yours.

- hyper-parameter of vanilla kNN-MT

  we follow the configure of [adaptive kNN-MT](https://github.com/zhengxxn/adaptive-knn-mt)

  |domain|IT|Medical|Koran|Law|
  |:--:|:--:|:--:|:--:|:--:|
  |K|8|4|16|4|
  |Temperature|10|10|100|10|
  |Lambda|0.7|0.8|0.8|0.8|

- hyper-parameter of adaptive kNN-MT

  we choose the max-k which obtained highest BLEU for every domain in 
  [adaptive kNN-MT](https://github.com/zhengxxn/adaptive-knn-mt)

  <table>
    <tr>
      <th align="center">Domain</th>
      <td align="center">IT</td>
      <td align="center">Medical</td>
      <td align="center">Koran</td>
      <td align="center">Law</td>
    </tr>
    <tr>
      <th align="center">max-k</th>
      <td align="center">8</td>
      <td align="center">16</td>
      <td align="center">16</td>
      <td align="center">8</td>
    </tr>
  </table>

  although `temperature` is trainable, we follow the paper and fix `temperature` to 10.  

- hyper-parameter of kernel smoothed kNN-MT

  for all domains, we use fixed `k` 16.


## Contributor
Qianfeng Zhao: qianfeng@smail.nju.edu.cn
Wenhao Zhu: zhuwh@smail.nju.edu.cn
