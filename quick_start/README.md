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


---

