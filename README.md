# Pyraformer: Low-complexity Pyramidal Attention for Long-range Time Series Modeling and Forecasting
This is the Pytorch implementation of Pyraformer (Pyramidal Attention based Transformer) in the ICLR paper: [Pyraformer: Low-complexity Pyramidal Attention for Long-range Time Series Modeling and Forecasting](https://openreview.net/pdf?id=0EXmFzUn5I).

![The network architecture of Pyraformer.](./img/Figure_1.png)
<center><b>Figure 1.</b> The network architecture of Pyraformer.</center>

## Pyramidal Attention
As demonstrated in Figure 2, we leverage a pyramidal graph to describe the temporal dependencies of the observed time series in a multiresolution fashion. We can decompose the pyramidal graph into two parts: the inter-scale and the intra-scale connections. The inter-scale connections form a C-ary tree, in which each parent has C children. For example, if we associate the finest scale of the pyramidal graph with hourly observations of the original time series, the nodes at coarser scales can be regarded as the daily, weekly, and even monthly features of the time series. As a consequence, the pyramidal graph offers a multiresolution representation of the original time series. Furthermore, it is easier to capture long-range dependencies (e.g., monthly dependence) in the coarser scales by simply connecting the neighboring nodes via the intra-scale connections. In other words, the coarser scales are instrumental in describing long-range correlations in a manner that is graphically far more parsimonious than could be solely captured with a single, finest scale model.


![The Pyramidal Attention Mechanism.](./img/Figure_2.png#center)
<center><b>Figure 2.</b> The Pyramidal Attention Mechanism.</center>

## Requirements
* Python 3.7
* pytorch 1.8.0
* CUDA 11.1
* TVM 0.8.0 (optional)

Dependencies can be installed by:

    pip install -r requirements.txt

If you are using CUDA 11.1, you can use the compiled TVM runtime version in the our code to run PAM-TVM. Due to the short history length in the experiments, PAM-TVM does not provide a speed increase. If you want to compile our PAM-TVM kernel yourself, see [here](https://tvm.apache.org/docs/install/index.html) to compile TVM 0.8.0 first.

## Data preparetion
The four datasets (Electricity, Wind, ETT and App Flow) used in this paper can be downloaded from the following links:
* [Electricity](https://archive.ics.uci.edu/ml/datasets/ElectricityLoadDiagrams20112014)
* [Wind](https://www.kaggle.com/sohier/30-years-of-european-wind-generation)
* [ETT](https://github.com/zhouhaoyi/ETDataset)
* [App Flow](https://github.com/alipay/Pyraformer/blob/master/data/app_zone_rpc_hour_encrypted.zip)

The downloaded datasets can be put in the 'data' directory. For single step forecasting, we preprocess Electricity, Wind and App Flow using scripts preprocess_elect.py, preprocess_wind.py and preprocess_flow.py respectively. You can also download preprocessed data [here](https://drive.google.com/drive/folders/1-b9tR6Tgmx48smPMetzAhVSV7-95im3X?usp=sharing). and put them in the 'data' directory. The directory structure looks like:

    ${CODE_ROOT}
        ......
        |-- data
            |-- elect
                |-- test_data_elect.npy
                |-- train_data_elect.npy
                ......
            |-- flow
                ......
            |-- wind
                ......
            |-- ETT
                |-- ETTh1.csv
                |-- ETTh2.csv
                |-- ETTm1.csv
                |-- ETTm2.csv
            |-- LD2011_2014.txt
            |-- synthetic.npy

Where synthetic.npy is generated by running:

    python simulate_sin.py

## Training
To perform long-range forecasting, run:

    sh scripts/Pyraformer_LR_FC.sh

To perform single step forecasting, run:

    sh scripts/Pyraformer_SS.sh

The meaning of each command line argument is explained in long_range_main.py and single_step_main.py, respectively.

## Evaluate
Evaluation can be done by adding the -eval option to the command line.  We provide pretrained models [here](https://drive.google.com/drive/folders/15av5ZhHG8tbX8HuxZNNDGBybdnuxzA83?usp=sharing). The downloaded models should be put in the 'models' directory. The directory structure is as follows:

    ${CODE_ROOT}
        ......
        |-- models
            |-- LongRange
                |-- elect
                    |-- 168
                        |-- best_iter0.pth
                        |-- best_iter1.pth
                        |-- best_iter2.pth
                        |-- best_iter3.pth
                        |-- best_iter4.pth
                    |-- 336
                        ......
                    |-- 720
                        ......
                |-- ETTh1
                    ......
                |-- ETTm1
                    ......
            |-- SingleStep
                |-- elect
                    |-- best_model.pth
                |-- flow
                    |-- best_model.pth
                |-- wind
                    |-- best_model.pth

Below are evaluation examples:

    python long_range_main.py -data ETTh1 -input_size 168 -predict_step 168 -n_head 6 -eval
    
    python single_step_main.py -data_path data/elect/ -dataset elect -eval

## Citation

    @inproceedings{liu2022pyraformer,
    title={Pyraformer: Low-Complexity Pyramidal Attention for Long-Range Time Series Modeling and Forecasting},
    author={Liu, Shizhan and Yu, Hang and Liao, Cong and Li, Jianguo and Lin, Weiyao and Liu, Alex X and Dustdar, Schahram},
    booktitle={International Conference on Learning Representations},
    year={2022}
    }
