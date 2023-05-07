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

#### 限制:
其它比较好理解
Q: 1.什么是混合搜索 2.PII检测是什么
A: 
1.混合搜索：是一种结合了基于关键词的搜索和基于语义的搜索的方法。基于关键词的搜索主要关注精确匹配用户查询中的关键词，而基于语义的搜索则侧重于理解查询的语义，并找到与查询意图相关的内容，而不仅仅是关键词匹配。
    - 什么是基于语义搜索, 原理是什么
        - 上下文关联：语义搜索还考虑到与查询相关的上下文信息，如用户的地理位置、搜索历史、时间等，以便提供更个性化的结果。
        - 语义关联：搜索引擎会根据词汇间的关系，例如同义词、反义词、上下位关系等，来扩展搜索范围并提高搜索结果的相关性。
        - 基于向量的表示：在语义搜索中，文本通常通过将其映射到高维空间中的向量来表示。这些向量可以捕捉文本的语义信息，使得具有相似含义的文本具有相似的向量表示。这样，搜索引擎可以通过计算向量之间的相似度来衡量文档与查询的相关性。


2.PPI检测：（Personally Identifiable Information，个人身份信息）。PII 检测是一种用于识别和保护个人身份信息的技术。应该是指这项技术无法全面过滤隐私信息.



#### Future
什么是 TF-IDE
是一种用于信息检索和文本挖掘的统计方法。它可以衡量一个词在特定文档中的重要性。TF-IDF由两部分组成:
    - 词频（TF）：衡量一个词在文档中出现的频率。
    - 逆文档频率（IDF）：衡量一个词在整个文档集合中的罕见程度。
拆解关键字, 对高频出现的降权, 对低频出现的加权.

**高级分块策略和嵌入计算：**这部分主要关注在将文档表示为向量（嵌入）之前，如何更好地将文档拆分成块以及如何计算这些块的嵌入。这可能包括将文档标题、摘要等信息嵌入到向量中，或对文档块和摘要进行加权平均。这些方法旨在提高文档向量的质量，从而改善检索结果。

**附加可选服务：**这部分关注提供额外的功能，如对文档进行摘要或在嵌入之前对文档进行预处理。这些服务通常使用语言模型实现，并直接集成到插件中。摘要服务将长文档简化为核心观点，从而提高检索结果的质量。预处理服务在将文档嵌入到向量空间之前对其进行清洗和格式化，例如消除停用词、转换为小写字母等，以改善文档向量的质量和搜索性能。



---

**很多靠 GPT 完成的部分, 以及个人理解部分, 如果有不对的地方欢迎指出!!**