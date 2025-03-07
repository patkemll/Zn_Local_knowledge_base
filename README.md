# 中文个性化知识库搭建

基于 DeepSeek-R1、bge-m3、bge-reranker-v2-m3、Dify 技术框架实现中文本地知识库，依托项目搭建，支持多模态和侧端的中文大模型知识库。

## 写在前面

本项目依托企业实际知识库 RAG 功能，结合 Dify 技术框架实现个性化语音输出和语言复现等复杂功能。

## 环境依赖

本项目环境基于 Ubuntu 22.04 X86，需要具备一定的 Git、数据库和 LLM 基础知识。

### 依赖的模型和数据库

- DeepSeek-R1-671B
- Bge-M3
- Bge-reranker-v2-m3

DeepSeek 使用 ModelScope 的模型参数进行下载，Hugging Face 国内可能会断开。

## 安装步骤

### DeepSeek-R1-671B

```bash
git clone https://www.modelscope.cn/deepseek-ai/DeepSeek-R1.git
```

### 使用 docker-compose 部署 Dify

克隆 Dify 代码仓库至本地环境。

```bash
# 假设当前最新版本为 1.0.0
git clone https://github.com/langgenius/dify.git --branch 1.0.0
```

进入 Dify 源代码的 Docker 目录

```bash
cd dify/docker
```

复制环境配置文件

```bash
cp .env.example .env
```

启动 Docker 容器

根据你系统上的 Docker Compose 版本，选择合适的命令来启动容器。你可以通过 `$ docker compose version` 命令检查版本，详细说明请参考 Docker 官方文档：

如果版本是 Docker Compose V2，使用以下命令：

```bash
docker compose up -d
```

如果版本是 Docker Compose V1，使用以下命令：

```bash
docker-compose up -d
```

基础 URL:http://<your-ollama-endpoint-domain>:11434

此处需填写 Ollama 服务地址。如果填写公开 URL 后仍提示报错，请参考常见问题，修改环境变量并使得 Ollama 服务可被所有 IP 访问。

若 Dify 为 Docker 部署，建议填写局域网 IP 地址，例如：http://192.168.1.100:11434 或

 Docker 容器的内部 IP 地址，例如：http://host.docker.internal:11434。

若为本地源码部署，可填写 http://localhost:11434。



### 安装 xinference

Python 版本要求：3.10

由于 docker 源目前用不了，国内源也很慢，建议使用 Ubuntu 22.04 的镜像，pip 清华源，安装速度较快。

```bash
pip install "xinference[all]"
```
pip 时如果报错一般是pytnon版本不对，或者 缺少编译依赖

安装 llama-cpp-python 所需依赖

```bash
sudo apt update
sudo apt install -y build-essential cmake python3-dev
pip install --no-cache-dir llama-cpp-python
```
有GPU版本
```bash
CMAKE_ARGS="-DLLAMA_CUBLAS=ON" pip install --no-cache-dir llama-cpp-python
```

无GPU版本
```bash
CMAKE_ARGS="-DLLAMA_CUDA=OFF" pip install --no-cache-dir llama-cpp-python
```
```bash
pip install --no-cache-dir --no-build-isolation llama-cpp-python
```
再次重新安装
```
pip install "xinference[all]"
```



libgomp.so.1无法找到问题：
find /usr/lib /usr/lib64 /lib /lib64 -name "libgomp.so.1"
export LD_LIBRARY_PATH=/usr/lib/gcc/x86_64-linux-gnu/11:$LD_LIBRARY_PATH
使用 screen 持久化运行

```bash
#!/bin/bash

# 修改环境变量 设置模型从 ModelScope 下载
export XINFERENCE_MODEL_SRC=modelscope

# 启动服务
xinference-local --host 0.0.0.0 --port 9997
```

### 启动 bge-m3

这个不需要持久化

```bash
xinference launch --model-name bge-m3 --model-type embedding
```

调用代码

```python
import openai

client = openai.Client(
  api_key="cannot be empty",  # 可以在上面命令设置
  base_url="http://<XINFERENCE_HOST>:<XINFERENCE_PORT>/v1"
)
client.embeddings.create(
  model="bge-m3",  # 这里一般使用 model name 就行
  input=["What is the capital of China?"]
)
```

### 启动 bge-reranker-v2-m3

这个也不需要持久化

```bash
xinference launch --model-name bge-reranker-v2-m3 --model-type rerank
