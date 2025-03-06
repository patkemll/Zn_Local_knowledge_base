# 中文个性化知识库搭建
基于DeepSeek-R1、bge-m3、bge-reranker-v2-m3、dify技术框架实现中文本地知识库，依托项目搭建，搭建中文大模型知识库LLM支持多模态、侧端

写在前面：
本项目依托企业实际知识库RAG功能、另外很多机遇Dify技术框架可以实现很多复杂功能：例如个性化语音输出、个性化语言复现

本项目环境依托Ubuntu22.04 X86
需要拥有部分git知识、数据库知识、LLm基础知识体系

环境依赖
本项目RAG数据库来自于：中文环境
DeepSeek-R1-671B
Bge-M3
Bge-reranker-v2-m3

DeepSeek使用modelscope的模型参数进行下载，Hugging Face国内会断
#DeepSeek-R1-671B版（有其他需要自行下载即可，记住模型名称即可）
git clone https://www.modelscope.cn/deepseek-ai/DeepSeek-R1.git
