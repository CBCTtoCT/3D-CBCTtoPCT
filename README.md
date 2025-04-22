# 3D-CBCTtoPCT

*******************************************************************************
## 简要介绍

本代码主要做3D医疗影像转换/增强，可以选CycleGAN或pix2pix为主框架，生成器为Unet或resnet，判别器为PatchGAN。

*******************************************************************************
## 版本与库的需要

1. python版本：3.8.19
2. Cuda版本：12.6
3. 其他packages可使用 "pip install -r requirements.txt"， 也可按需单独安装

*******************************************************************************
## 代码文件解释

base_model.py: 模型初始化函数
cycle_gan_model.py：cycleGAN框架
networks3D.py： 生成器和判别器
NiftiDataset.py：Dataloader
base_options.py/train_options.py/test_options.py: 调参代码
train.py: 训练模型
test.py: 推理单个图像

*******************************************************************************
## 训练方法与步骤

1. 数据集准备

假设训练集存放在一个名为Train_folder的文件夹里，在文件夹分别创建两个子文件夹，分别名为：images和labels，分别存放图像与标签。

最终效果为：
                 
	|   ├── train              
	|   |   ├── images (CBCT)            
	|   |   |   ├── 0.nii              
	|   |   |   └── 1.nii                     
	|   |   └── labels (pct)          
	|   |   |   ├── 0.nii             
	|   |   |   └── 1.nii

2. 刚性配准

因为images和labels需要相同尺寸，所以需要刚性配准。使用simple itk官方文档里的代码，此代码已上传。

##tips：
里面#To Do标识了两种方法，可以都试一下，不同数据集用两种方法效果不太一样，每次配准效果也可能不太一样，可以多试几次。

3. 训练

训练指令样例：
python train.py --data_path /data/data/GMD/datasetsAtoB/train --name CBCTtoPCT --model cycle_gan --batch_size 8 --gpu_ids 4,5,6,7

后面跟的是各种参数，参数可以在指令里改，也可以直接去options.py里面改default。

比较重要的是--save_epoch_freq，这个是每n个epoch保存一个检查点

batch_size最开始建议先调为1，之后可以适量成倍增加，注意训练图像数量尽量是batch_size的倍数，否则可能报错。
--model 可以选cycle_gan 或 pix2pix
--netG 可以选unet或resnet，具体调节可以在networks3D.py里找，全都有

剩下的参数选项，代码里都有具体的介绍

训练后的模型储存在checkpoints文件下面的以--name参数命名的文件夹里
	
4. 推断

指令样例：

python test.py --image /data/data/GMD/datasetsAtoB/test/images/CBCT0360.nii --result /data/data/GMD/SCT.nii

image是需要推断的图像地址，result是结果存放地址

将latest_net_G_A.pth 移动到checkpoints文件下面的experiment_name文件里，将latest_net_G_A.pth改为latest_net_G.pth （这一步为了适配代码，如果觉得麻烦可以去代码里改），然后运行指令，即可推理。

5. Tips

pct和ect数据集后续可以删一些增强不明显的数据

*******************************************************************************

祝接下来的工作顺利！
