### 第一次启动
```
Clone the repository: git clone https://github.com/openai/chatgpt-retrieval-plugin.git

cd /path/to/chatgpt-retrieval-plugin
```

```
pip install poetry
```
```
poetry env use python3.10
poetry shell
```

```
poetry install
```

如果你选择的是 pinecone 的数据库

```
export DATASTORE=pinecone
export BEARER_TOKEN=<BEARER_TOKEN>
export OPENAI_API_KEY=<你的 openAI API key>
export PINECONE_API_KEY=<界面中那段隐藏的 value>
export PINECONE_ENVIRONMENT=<environment>
export PINECONE_INDEX=<index_name 符合命名规范就好>
```

```
poetry run start
```

### 重新启动

重新启动只要进入特定环境, 然后设置 environment, 再启动就可以了

```
poetry env use python3.10
poetry shell
```

如果你选择的是 pinecone 的数据库

```
export DATASTORE=pinecone
export BEARER_TOKEN=<BEARER_TOKEN>
export OPENAI_API_KEY=<你的 openAI API key>
export PINECONE_API_KEY=<界面中那段隐藏的 value>
export PINECONE_ENVIRONMENT=<environment>
export PINECONE_INDEX=<index_name 符合命名规范就好>
```

```
poetry run start
```

### 接入插件
plugins store --> Develop your own plugin --> 添加你的项目根 url, (不要带 .well-known parts) --> 界面中一直点击下一步 --> 填入之前环境变量中的 BEARER TOKEN, 之后就可以开始使用了


### 插入 langchain docs 的数据到向量数据库
**参考[这里](https://github.com/zard1152/examples/blob/master/generation/chatgpt/plugins/langchain-docs-plugin.ipynb)**

### 插入整个 API 项目的数据到向量数据库
**参考[这里]()**


### 一起理解整个源码
**参考[这里](https://github.com/zard1152/chatgpt-retrieval-plugin/tree/main/quick_start/read_code.md)**



### 读 readme

限制:
其它比较好理解
Q: 1.什么是混合搜索 2.PII检测是什么
A: 
1.混合搜索：是一种结合了基于关键词的搜索和基于语义的搜索的方法。基于关键词的搜索主要关注精确匹配用户查询中的关键词，而基于语义的搜索则侧重于理解查询的语义，并找到与查询意图相关的内容，而不仅仅是关键词匹配。
    - 什么是基于语义搜索, 原理是什么
        - 上下文关联：语义搜索还考虑到与查询相关的上下文信息，如用户的地理位置、搜索历史、时间等，以便提供更个性化的结果。
        - 语义关联：搜索引擎会根据词汇间的关系，例如同义词、反义词、上下位关系等，来扩展搜索范围并提高搜索结果的相关性。
        - 基于向量的表示：在语义搜索中，文本通常通过将其映射到高维空间中的向量来表示。这些向量可以捕捉文本的语义信息，使得具有相似含义的文本具有相似的向量表示。这样，搜索引擎可以通过计算向量之间的相似度来衡量文档与查询的相关性。


2.PPI检测：（Personally Identifiable Information，个人身份信息）。PII 检测是一种用于识别和保护个人身份信息的技术。应该是指这项技术无法全面保护放在数据库中的隐私信息.

---

