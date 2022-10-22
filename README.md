원본 github : https://github.com/kozistr/movie-rate-prediction

# Movie Rate Prediction
영화 평점 예측 with Tensorflow

## Personal Environments
* OS  : MacOS Monterey 12.6 / MacBook Pro 2019
* CPU : 2.6 GHz 6코어 Intel Core i7
* GPU : AMD Radeon Pro 5300M 4GB
* RAM : 16GB ~
* Library : TF 1.x with CUDA 9.0~ + cuDNN 7.0~

## Prerequisites
* Python
* MySQL DB
* tensorflow 1.x
* numpy
* gensim and konlpy and soynlp
* mecab-ko
* pymysql
* h5py
* tqdm
* bs4
* (Optional) java 1.7+
* (Optional) PyKoSpacing
* (Optional) MultiTSNE (for visualization)
* (Optional) matplotlib (for visualization)

## DataSet

| DataSet  |  Language  | Sentences | Words | Size |
|:---:|:---:|:---:|:---:|:---:|
| [NAVER Movie Review](http://movie.naver.com) | *Korean* | ```8.86M``` | ```391K``` |  ```About 1GB``` | 

### Movie Review Data Distribution

![dist](./image/movie-rate-distribution.png)

## Usage
### 1.1 Installing Dependencies
    # Necessary
    $ brew install mysql
    $ sudo python3 -m pip install -r requirements.txt
    # Optional
    $ sudo python3 -m pip install -r opt_requirements.txt
### 1.2 Configuration
    # ```config.py```, 개인 환경에 맞게 스크립트의 파라미터 reset이 필요합니다.
### 2. Parsing the DataSet
    $ python3 movie-parse.py
### 3. Making DataSet DB
    $ python3 db.py
### 4. Making w2v/d2v embeddings (**skip** if u only wanna use Char2Vec)
    $ python3 preprocessing.py

    usage: preprocessing.py [-h] [--load_from {db,csv}] [--vector {d2v,w2v}]
                            [--is_analyzed IS_ANALYZED]
    
    Pre-Processing NAVER Movie Review Comment
    
    optional arguments:
      -h, --help            show this help message and exit
      --load_from {db,csv}  load DataSet from db or csv
      --vector {d2v,w2v}    d2v or w2v
      --is_analyzed IS_ANALYZED
                            already analyzed data

### 5. Training a Model
    $ python3 main.py --refine_data [True or False]

    usage: main.py [-h] [--checkpoint CHECKPOINT] [--refine_data REFINE_DATA]
    
    train/test movie review classification model
    
    optional arguments:
      -h, --help            show this help message and exit
      --checkpoint CHECKPOINT
                            pre-trained model
      --refine_data REFINE_DATA
                            solving data imbalance problem

## Repo Tree
```
│
├── comments          (NAVER Movie Review DataSets)
│    ├── 10000.sql
│    ├── ...
│    └── 200000.sql
├── w2v               (Word2Vec)
│    ├── ko_w2v.model (Word2Vec trained gensim model)
│    └── ...
├── d2v               (Doc2Vec)
│    ├── ko_d2v.model (Dov2Vec trained gensim model)
│    └── ...
├── model             (Movie Review Rate ML Models)
│    ├── textcnn.py
│    └── textrnn.py
├── image             (explaination images)
│    └── *.png
├── ml_model          (tf pre-trained model saved in here)
│    ├── checkpoint
│    ├── ...
│    └── charcnn-best_loss.ckpt
├── config.py         (Configuration)
├── tfutil.py         (handy tfutils)
├── dataloader.py     (Doc/Word2Vec model loader)
├── movie-parser.py   (NAVER Movie Review Parser)
├── db.py             (DataBase processing)
├── preprocessing.py  (Korean normalize/tokenize)
├── visualize.py      (for visualizing w2v)
└── main.py           (for easy use of train/test)
```

## Pre-Trained Models

Here's a **google drive link**. You can download pre-trained models from [here](https://drive.google.com/open?id=1yzVzYeybAgjEZ8KG7jwnxDt-eLRSusLi) !

* Embedding Models
    * Word2Vec model : [here](https://drive.google.com/open?id=1WmKtvPIGJ5eDs0TEdSVY51qtO0JdXEcl)

* M.L Models
    * TextCNN model : [here](https://drive.google.com/open?id=1z3LmaKCiW6Qg_OqOCBS-Do7qiw3m5iMS)
    * TextRNN model : [here](https://drive.google.com/open?id=1sVhfN-tpNhvZX_Ms3VXGz6qjsxqcNM8r)

## Models

* TextCNN

![img](./image/TextCNN-architecture.png)

credited by [Toxic Comment Classification kaggle 1st solution](https://medium.com/@zake7749/top-1-solution-to-toxic-comment-classification-challenge-ea28dbe75054)

* TextRNN

![img](./image/TextRNN-architecture.png)

credited by [Toxic Comment Classification kaggle 1st solution](https://medium.com/@zake7749/top-1-solution-to-toxic-comment-classification-challenge-ea28dbe75054)

## Results

DataSet is not good. So, the result also isn't pretty good as i expected :( <br/>
**Refining/Normalizing raw sentences are needed!**

* TextCNN (Char2Vec)

![img](./image/TextCNN-char2vec-loss.png)

> Result : train MSE 1.553, val MSE 3.341 <br/>
> Hyper-Parameter : rand, conv kernel size [10,9,7,5,3], conv filters 256, drop out 0.7, fc unit 1024, adam, embed size 384

* TextCNN (Word2Vec)

![img](./image/TextCNN-word2vec-loss.png)

> Result : train MSE 3.410 <br/>
> Hyper-Parameter : non-static, conv kernel size [2,3,4,5], conv filters 256, drop out 0.7, fc unit 1024, adadelta, embed size 300

* TextRNN (Word2Vec)

![img](./image/TextRNN-word2vec-loss.png)

> Result : train MSE 3.646 <br/>
> Hyper-Parameter : non-static, rnn cells 128, attention 128, drop out 0.7, fc unit 1024, adadelta, embed size 300

* TextRNN (Char2Vec)

SOON!

## Visualization

You can just simply type ```tensorboard --logdir=./ml_model/```

### Word2Vec Embeddings (t-SNE)

![img](./image/w2v-t_sne-visualization.png)

> Perplexity : 80 <br/>
> Learning rate : 10 <br/>
> Iteration : 310 <br/>

## To-Do
1. deal with word spacing problem

## ETC

**Any suggestions and PRs and issues are WELCONE :)**

## Author
HyeongChan Kim / [@kozistr](http://kozistr.tech)
