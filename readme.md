##  LiveNet

## 项目目录结构如下： 

```
LiveNet
├── align
├── cfgs
├── checkpoints
│   └── mobilenetv2_bs32
├── dataset
│   ├── nuaa
│   │   ├── fake
│   │   └── real
│   ├── nuaa_64
│   │   ├── fake
│   │   └── real
│   ├── test
│   │   ├── fake
│   │   └── real
│   ├── train
│   │   ├── fake
│   │   └── real
│   └── val
│       ├── fake
│       └── real
├── logs
├── models
└── utils
```

- align: 存放MTCNN模型文件及MTCNN运行主程序。
- cfgs: 存放MobileNet模型参数配置文件。
- checkpoints：存放MobileNet训练好的模型文件。
- dataset：存放数据集相关文件。
- log：存放模型训练过程日志。
- models：存放MobileNet网络文件。
- utils：存放相关工具。

## 基础环境安装

GPU 服务器：CUDA=9.0，torch 安装时跟 CUDA 版本有关，可根据自已的 CUDA 版本号，安装相应的 torch 。基础环境安装如下：

```
conda create -n livenet python=3.5 jupyter ipython
source activate livenet
pip install scipy==1.1.0
pip install Pillow
pip install tensorflow-gpu==1.8.0
pip install torch torchvision
pip install sklearn
pip install opencv-python
pip install pyyaml
pip install numpy==1.16.2
```

## 收集数据

 在 dataset 下创建 nuaa 目录，在 nuaa 下创建 reak、fake 目录，用于存放真实有攻击的图片： 

```
mkdir -p dataset/nuaa/real  dataset/nuaa/fake
cd dataset
cp raw/ClientRaw/*/*.jpg nuaa/real/
cp raw/ImposterRaw/*/*.jpg nuaa/fake/
```

## 应用人脸检测

 运行以下命令进行人脸检测处理： 

```
mkdir dataset/nuaa_64
cd align
python align_dataset_mtcnn.py ../dataset/nuaa ../dataset/nuaa_64 --image_size 64 --margin 8
```

## 分割数据集

 将 nuaa_64 下的图片按 8 : 1 : 1 切分成训练集、验证集、测试集，训练集、验证集、测试集分别保存到 train、val、test 目录下。 

```
cd dataset
mkdir -p train/real train/fake val/real val/fake test/real test/fake
python split_data.py
```

##  开始训练模型

 在 cfgs 下创建一个模型配置文件 mobilenetv2.yaml ，主要是相关模型参数的配置： 

```
common:
    arch: moilenetv2
    workers: 1
    batch_size: 32
    epochs: 50
    policy: "step"
    base_lr: 0.01
    momentum: 0.9
    weight_decay: 0.0001
    print_freq: 10
    save_path: checkpoints/mobilenetv2_bs32
```

 运行以下命令进行训练： 

```
mkdir logs
python main.py --config "cfgs/mobilenetv2.yaml" dataset
```

实时测试：

```
Python app.py
```

