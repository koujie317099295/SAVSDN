# XJTU SAVSDN-SAES-SAVSDN_detection_result
## 什么是SAVSDN？
场景感知视频火花检测网络（SAVSDN）可以检测可见光视频中的高速飞溅火花，它由基于信息融合的级联视频编解码器-YOLOv5结构组成。因为环境中存在着大量具有和火花完全一致的亮线段特征的干扰。 现有的图像目标检测器和视频目标检测器对于火花检测存在大量的过检测。 SAVSDN中的视频编解码器可以与任何图像目标检测器级联，编解码器可以通过改进的序列到序列模型感知场景和火花。 该视频火花检测技术已发表在期刊文章中，您可以从 https://doi.org/10.3390/s21134453 获得更多详细信息。
### 智能视频火花检测的三个主要难点：
![1625052610](https://user-images.githubusercontent.com/83768527/123953358-b69ff900-d9d9-11eb-8be8-a5766e9a96e3.png)

图中显示了智能火花检测面临的三大难点。 首先，单个火花的特征是一条亮线段，这与图像中的灯、金属电缆和反射光的特征完全一致。 其次，异常火花数据的缺乏阻碍了监督学习模型的训练。 第三，使用10多个摄像头来监控航空发动机的状态，这就要求火花检测算法是实时并行的。

值得注意的是，由于航空发动机试验舱内存在的高速气流会导致摄像头的晃动，因此视频画面往往伴随着抖动，导致场景中所有的线段也在复杂的时序运动。



### SAVSDN的网络结构：
![1625052649(1)](https://user-images.githubusercontent.com/83768527/123953362-b7d12600-d9d9-11eb-901f-8ae4b61c1ce3.png)

事实上，我们提出了一种基于深度学习的航空发动机测试实时异常火花视频检测方法。每次，输入都是连续九帧的序列，输出是最后三帧中所有异常火花的位置。特别是前七帧被SAVSDN用来感知场景中的亮线段干扰，让网络很容易区分火花和干扰。首先输入九帧图像序列进行预处理，包括图像尺寸归一化和图像灰度。然后将输入按时间顺序分为4组图像子序列，每组图像子序列包含3张连续的图像。接下来，使用四个权重共享 ConvLSTM 分别从四个图像子序列中提取运动特征图像。此外，将通过整合前三组图像子序列对应的特征图像来创建新序列。此外，新序列将被发送到另一个 ConvLSTM 以提取场景感知特征图像，其中包含长时间存在的亮线段干扰。此外，在获得场景感知特征图像后，再使用另一个ConvLSTM将场景感知特征图像和最后一个图像子序列对应的运动特征图像进行融合，然后解码得到时空深度特征图像。时空深度特征图像包含能够区分火花和亮线段干扰的时空特征，这些时空特征将被发送到后续的图像对象检测器以再次检测火花特征。如果图像物体检测器检测到火花，人机交互终端会报警并持续监控，否则继续监控。



## 什么是SAES？
据我们所知，模拟航空发动机火花（SAES）数据集是第一个发布的航空发动机异常火花检测数据集。 该数据集总共包含 26,382 张图像和相关的火花标签。 这些图像来自一段 17 分 35 秒的视频，由海康威视 DS-2CD3T45FP1-LS 摄像机以 25 fps 的帧速率拍摄。 所有图像均取自我们构建的模拟航空发动机舱。 内室由一组复杂的干扰组成，包括复杂的光照变化、五颜六色的摇晃电缆、航空发动机振动、闪烁的火焰、不稳定的视频界面和闪亮的金属表面。
### 我们建造的模拟航空发动机试验舱:

![image](https://user-images.githubusercontent.com/55908288/120090714-256a0800-c137-11eb-93e3-2e5e2386db3a.png)

![image](https://user-images.githubusercontent.com/55908288/120090721-29962580-c137-11eb-8d09-831120f07a21.png)


### SAES数据集的部分图像实例:

![image](https://user-images.githubusercontent.com/55908288/120090586-5a298f80-c136-11eb-9056-83f2e2c597a3.png)

![image](https://user-images.githubusercontent.com/55908288/120090589-5f86da00-c136-11eb-8b01-eedaa969d269.png)

![image](https://user-images.githubusercontent.com/55908288/120090593-631a6100-c136-11eb-9a6b-900a10713d99.png)


## SAES数据集和SAVSDN的检测效果演示视频下载链接:
```
https://pan.baidu.com/s/1A7ocxF9LS8GrKGlJFQYpgA   (提取码:o0rc)
```
## 如何执行火花检测代码?
我们提出来的视频编解码器源码位于 "models/autoCodeNet.py" ，并且一个训练好的可以直接进行火星检测的模型文件位于 "weights/sparkDetectYolov5s/epoch17.pt"。
1. 安装必要的环境:
```
pip install -r requirements.txt
```
2. 将需要检测的视频存放至 "video/" 。
3. 开始火星检测：
```
python detect.py
```
4. 检测结果将会被存放至 "runs/detect/exp/" 。
## 模型性能测试
我们在建造的模拟航空发动机试验舱和一些真实场景中测试了SAVSDN的火花检测性能。

![image](https://user-images.githubusercontent.com/55908288/120088969-a40b7900-c128-11eb-9265-f8be2c43f511.png) ![image](https://user-images.githubusercontent.com/55908288/120088975-b4235880-c128-11eb-8523-68c936d03faa.png)

![image](https://user-images.githubusercontent.com/55908288/120088977-c00f1a80-c128-11eb-8e48-47279c8a642f.png) ![image](https://user-images.githubusercontent.com/55908288/120088979-c43b3800-c128-11eb-8402-22f2aba17e2a.png)


SAVSDN的检测结果几乎没有过检测！

![sparks](https://user-images.githubusercontent.com/55908288/120089937-5a735c00-c131-11eb-81c3-bf0d9bda7df9.gif)

## 该火星检测技术的一些扩展应用
SAVSDN在一些其他的应用场景下，也可以取得很好的检测精度。

### 其他的火星:

![other_sparks_1 00_00_00-00_00_08](https://user-images.githubusercontent.com/55908288/120110521-6a765480-c1a0-11eb-8cf4-2324d974d29b.gif)

![other_sparks_2 00_00_00-00_00_10](https://user-images.githubusercontent.com/55908288/120110530-706c3580-c1a0-11eb-9a42-23ebedb5bd71.gif)


### 羽毛球检测:

![04_badminton 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094158-6111cb80-c151-11eb-95cd-98503b5c8c3b.gif)

![badminton2 00_00_02-00_00_19](https://user-images.githubusercontent.com/55908288/120110543-77934380-c1a0-11eb-809c-0515827e8185.gif)

![badminton3 00_00_02-00_00_12](https://user-images.githubusercontent.com/55908288/120110553-7cf08e00-c1a0-11eb-89b9-3ba0b78cc153.gif)


### 高空坠物检测:

![07_high-altitude parabolics 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094295-0fb60c00-c152-11eb-9964-b961017c982e.gif)


### 高速飞行的子弹检测:

![03_tracer bullets 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094306-1e042800-c152-11eb-9ac0-e8a53cd3aef9.gif)


## 参考
YOLOv5 (https://github.com/ultralytics/yolov5) 是SAVSDN中被级联于视频编解码器后的图像目标检测器。

## 致谢
该研究受到中国航发四川燃气涡轮研究院的委托。

## 细节
该视频火花检测技术已发表在期刊文章中，您可以从 https://doi.org/10.3390/s21134453 获得更多详细信息。

如果您有任何问题，请发送电子邮件至 kj317099295@stu.xjtu.edu.cn 与我们联系。

## What is SAVSDN?
The Scene-Aware Video Spark Detection Network (SAVSDN) can detect high-speed flying sparks in visible light video, which consists of an information fusion-based cascading video codec-YOLOv5 structure. Because there are a lot of disturbances in the environment that have the characteristics of bright line segments that are exactly the same as sparks. Existing image object detectors and video object detectors have a large amount of over-detection for spark detection. The video codec in SAVSDN can be cascaded with any image object detector, and the codec can sense scenes and sparks through an improved sequence-to-sequence model. This video spark detection technology has been published in a journal article, and you can get more details from https://doi.org/10.3390/s21134453 .
### The three main difficulties in video spark intelligent detection:
![1625052610](https://user-images.githubusercontent.com/83768527/123953358-b69ff900-d9d9-11eb-8be8-a5766e9a96e3.png)

The figure shows the three difficulties faced by intelligent spark detection. First, the feature of a single spark is a bright line segment, which is completely consistent with the features of the lights, metal cables, and reflected light in the image. Second,a lack of abnormal spark data hinders the training of supervised learning models. Third, more than 10 cameras are used to monitor the status of aero engines, which requires the spark detection algorithm to be real-time and parallel.

It is worth noting that because the high-speed airflow in the aero engine test cabin will cause the camera to shake, the video images are often accompanied by jitter, which causes all the line segments in the scene to also move in complex time series.



### The network structure of SAVSDN：
![1625052649(1)](https://user-images.githubusercontent.com/83768527/123953362-b7d12600-d9d9-11eb-901f-8ae4b61c1ce3.png)

In fact, we proposed a real-time anomalous spark video detection method based on deep learning for aero engine testing. Each time, the input was a sequence of nine consecutive frames, and the output was the position of all the anomalous sparks in the last three frames. In particular, the first seven frames were used by SAVSDN to perceive the bright line segments interference in the scene, so that the network could easily distinguish the sparks and the interference. First of all, nine frames of image sequences were inputted for preprocessing, including image size normalization and image grayscale. Then, the input was divided into 4 groups of image sub-sequences in chronological order, and each group of image sub-sequence contained 3 consecutive images. Next, four weight-sharing ConvLSTM were used to extract motion feature images from the four image sub-sequences, respectively. Moreover, a new sequence will be created by integrating the feature images corresponding to the first three groups of image sub-sequences. In addition, the new sequence will be sent to another ConvLSTM to extract scene perception feature images, which contains long time existing interference of bright line segments. Moreover, after obtaining scene perception feature images, another ConvLSTM was used to integrate the scene perception feature images and the motion feature images corresponding to the last image sub-sequence, and was then decoded to obtain a spatio-temporal deep feature image. The spatio-temporal deep feature image contained the spatio-temporal features that could distinguish sparks from the interference of bright line segments, which will be sent to the subsequent image object detector to detect spark features again. If the image object detector detects a spark, the human-computer interaction terminal will alarm and continuously monitor, otherwise continue to monitor.



## What is SAES?
As far as we know, the Simulated Aero Engine Spark (SAES) data set is the first published data set for aero engine anomalous spark detection. The data set contains 26,382 images and correlated spark tags in total. The images come from a video of 17 minutes and 35 seconds in length, captured by a Hikvision DS-2CD3T45FP1-LS camera with a frame rate of 25 fps. All the images were taken from a simulation aero engine chamber, which we constructed. The inner chamber comprised a set of complex interferences, including complex illumination changes, colorful shaking cables, aero engine vibration, flickering flames, unsteady video interface, and shining metal surfaces.
### The simulated aero engine chamber:

![image](https://user-images.githubusercontent.com/55908288/120090714-256a0800-c137-11eb-93e3-2e5e2386db3a.png)

![image](https://user-images.githubusercontent.com/55908288/120090721-29962580-c137-11eb-8d09-831120f07a21.png)


### The SAES dataset samples:

![image](https://user-images.githubusercontent.com/55908288/120090586-5a298f80-c136-11eb-9056-83f2e2c597a3.png)

![image](https://user-images.githubusercontent.com/55908288/120090589-5f86da00-c136-11eb-8b01-eedaa969d269.png)

![image](https://user-images.githubusercontent.com/55908288/120090593-631a6100-c136-11eb-9a6b-900a10713d99.png)


## The link to SAES data set and SAVSDN's detection results:
```
https://pan.baidu.com/s/1A7ocxF9LS8GrKGlJFQYpgA   (Extraction code:o0rc)
```
## How to run the source code for spark detection?
The source code of the video codec we proposed is "models/autoCodeNet.py" and a trained model file that can directly detect sparks is "weights/sparkDetectYolov5s/epoch17.pt".
1. Install the necessary environment:
```
pip install -r requirements.txt
```
2. Save the video to be detected to the "video/" directory.
3. Start spark detection:
```
python detect.py
```
4. The test results will be saved to the "runs/detect/exp/" directory.
## Test
SAVSDN is tested in a simulated environment and some real scenes.

![image](https://user-images.githubusercontent.com/55908288/120088969-a40b7900-c128-11eb-9265-f8be2c43f511.png) ![image](https://user-images.githubusercontent.com/55908288/120088975-b4235880-c128-11eb-8523-68c936d03faa.png)

![image](https://user-images.githubusercontent.com/55908288/120088977-c00f1a80-c128-11eb-8e48-47279c8a642f.png) ![image](https://user-images.githubusercontent.com/55908288/120088979-c43b3800-c128-11eb-8402-22f2aba17e2a.png)


When we apply it to the simulated aero engine chamber, the result is good enough and almost exists no mistake.

![sparks](https://user-images.githubusercontent.com/55908288/120089937-5a735c00-c131-11eb-81c3-bf0d9bda7df9.gif)

## Development
SAVSDN can also be applied to other fields with perfect performance.

### Other sparks:

![other_sparks_1 00_00_00-00_00_08](https://user-images.githubusercontent.com/55908288/120110521-6a765480-c1a0-11eb-8cf4-2324d974d29b.gif)

![other_sparks_2 00_00_00-00_00_10](https://user-images.githubusercontent.com/55908288/120110530-706c3580-c1a0-11eb-9a42-23ebedb5bd71.gif)


### Badminton:

![04_badminton 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094158-6111cb80-c151-11eb-95cd-98503b5c8c3b.gif)

![badminton2 00_00_02-00_00_19](https://user-images.githubusercontent.com/55908288/120110543-77934380-c1a0-11eb-809c-0515827e8185.gif)

![badminton3 00_00_02-00_00_12](https://user-images.githubusercontent.com/55908288/120110553-7cf08e00-c1a0-11eb-89b9-3ba0b78cc153.gif)


### Rubbish thrown from upstairs:

![07_high-altitude parabolics 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094295-0fb60c00-c152-11eb-9964-b961017c982e.gif)


### Shooting:

![03_tracer bullets 00_00_00-00_00_30](https://user-images.githubusercontent.com/83768527/120094306-1e042800-c152-11eb-9ac0-e8a53cd3aef9.gif)


## References
YOLOv5 (https://github.com/ultralytics/yolov5) is the image object detector in SAVSDN of this repository.

## Acknowledgements
This research is commissioned by AECC (Aero Engine Corporation of China) Sichuan Gas Turbine Establishment.

## Details
This video spark detection technology has been published in a journal article, and you can get more details from https://doi.org/10.3390/s21134453 .

If you have any question, please contact us by emailing to kj317099295@stu.xjtu.edu.cn.


