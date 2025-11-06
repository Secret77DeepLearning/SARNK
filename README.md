

#########


算法说明

一、算法概述
用到的降噪算法：改进的SAR-BM3D自适应降噪
用到的模型：yolo11l-obb、yolo11m-obb
算法概述：产生不同级别的降噪图像分别训练针对不同类别的四个模型，再用四个模型进行预测，将预测结果进行NMS合并，得到最终结果

二、模型训练用到的数据集
DOTA-v1.0、大赛官方数据集

三、数据集获取方式及其用途
https://captain-whu.github.io/DOTA/
DOTA数据集用于产生预训练的yolo模型，该模型是ulralytics包自带的预训练模型

四、预计模型训练和测试时间
以大赛官方服务器性能进行估计
模型训练时间预计不少于900小时
测试时间预计1小时

五、项目的主要依赖项
opencv、ultralytics、torch、torchvision


#########


模型预测验证

一、验证环境配置
打开SAR_Summit_Version_1105\predict文件夹
依照此文件夹中的requirements.txt配置训练环境
二、验证
打开SAR_Summit_Version_1105\predict\Data文件夹，将待测试图片放到其中的images文件夹下
运行despeckleTestMain10/35两个.py文件得到两组不同程度的降噪结果
回到SAR_Summit_Version_1105\predict文件夹
打开命令行运行如下四条命令
yolo obb predict model=train1/best.pt source=Data/testDenoised35 device=0 conf=0.3 iou=0.3 save_txt=True save_conf=True project=SAR_predict name=exp1
yolo obb predict model=train2/best.pt source=Data/testDenoised10 device=0 conf=0.2 iou=0.3 save_txt=True save_conf=True project=SAR_predict name=exp2
yolo obb predict model=train3/best.pt source=Data/testDenoised10 device=0 conf=0.3 iou=0.3 save_txt=True save_conf=True project=SAR_predict name=exp3
yolo obb predict model=train4/best.pt source=Data/testDenoised10 device=0 conf=0.2 iou=0.3 save_txt=True save_conf=True project=SAR_predict name=exp4

得到四个模型的检测结果
然后运行txtMerge.py进行初步合并
继而运行IOU.py进行NMS算法合并
最终得到IOU文件夹下的检测结果
三、生成pkl
打开SAR_Summit_Version_1105\predict文件夹
依照文件夹中的requirementsPKL.txt配置环境
在环境中运行pklCreate.py得到pkl文件


#########


模型训练

一、训练环境配置
打开SAR_Summit_Version_1105\trainData文件夹
依照文件夹中的requirements.txt配置训练环境
注意训练环境和预测环境不同
二、数据预处理
1、将所有训练可用的图片拷贝到trainData/trainImages文件中,将标签拷贝到trainData/annfiles文件夹中
2、打开刚才配置好的环境，在环境中运行despeckleTestMain10、despeckleTestMain35得到2种不同降噪级别的训练数据
3、运行convertLabels，得到yoloLabels中yolo格式的标签
4、运行splitData10/35，在trainData/dataset文件夹下，得到2个训练集NKData10/35
三、模型训练
1、运行trainData文件夹下的train1/2/3/4.py得到我们所需要使用的四个模型，保存在SAR文件夹下，可以验证所得的best.pt即为我们在预测中使用的四个best.pt