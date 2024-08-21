## 使用文档
### 下载大模型部署SDK
```shell
#部署SDK:
wget https://bj.bcebos.com/ppdeploy/ppdeploy1.2/PPDeploy1.2_LLM_Ver1.0.0_python.zip
#超小参数量大模型：
wget https://bj.bcebos.com/ppdeploy/ppdeploy1.2/LLM_Model/small_model.gguf
#稍大参数量大模型
wget https://bj.bcebos.com/ppdeploy/ppdeploy1.2/LLM_Model/large_model.gguf
```

### 设备连接
该大模型项目需要连接显示器和鼠标、键盘，同时为了语音输入和输出，需要连接麦克风和扬声器，如果没有专业设备，使用带麦克风的有线耳机即可，将耳机插在如下图所示位置：
![image](./res/microphone.jpg)


### 环境准备
- 执行如下指令安装相关软件，并设置对应环境变量：
```shell
bash prepare_env.sh
```

- 设置交换分区   
由于板卡自身内存不足以支撑进行大模型推理，需要使用swap交换分区，设置方式如下：
```shell
sudo fallocate -l 2G /swapfile
#如果报错：fallocate: fallocate failed: Text file busy，则执行：
sudo swapoff -a
sudo rm /swapfile
sudo fallocate -l 2G /swapfile
#设置权限
sudo chmod 600 /swapfile
#使用 mkswap 实用程序在文件上设置 Linux SWAP 区域
sudo mkswap /swapfile
#激活 swap 文件：
sudo swapon /swapfile
#要让创建好的 swap 分区永久生效，可以将 swapfile 路径内容写入到 /etc/fstab 文件当中 ：
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

```
设置完成，使用以下执行查看是否成功：
```shell
free -h
```
设置成功后，swap的total应为2G，如下图所示：
![image](./res/swap.jpg)



### 进行大模型推理
执行以下指令进行大模型推理
```shell
bash run.sh context_pdf.pdf small_model.gguf
```
- context_pdf.pdf即为知识库文件，大模型会首先搜索知识库查找相关的知识进行回答，可根据需要替换成其他知识库文件。 支持pdf、word、txt格式的文件。 
- small_model.gguf即为下载的大模型文件，可自行选择超小参数量或稍大参数量模型。

程序启动时会首先进行模型加载，加载完成会提示“你好，欢迎使用大模型服务，请按下空格键开始说话”，此时按下空格键开始提问，提问完成松开空格键，程序会进行语音转文字处理、搜索比对知识库、大模型生成答案、对答案语音合成和语音输出。答案输出完成后，语音提示“你还有其他想问的吗，请按下空格键继续说话”，**这句提示完成后**，可按下空格键继续交互。

注意：每一轮交互完成后，请在语音提示“你还有其他想问的吗，请按下空格键继续说话”完成后，再按下空格键进行语音输入。