
**以下内容是对文档的翻译, 如果需要快捷启动, 查看 [quick_start](https://github.com/zard1152/chatgpt-retrieval-plugin/tree/main/quick_start)**

# ChatGPT 检索插件
> **在此处加入 [ChatGPT 插件等待名单](https://openai.com/waitlist/plugins)!**
> 在此处找到一个检索插件的示例视频，该插件可以访问 2018 年至 2022 年的联合国年度报告 [视频链接](https://cdn.openai.com/chat-plugins/retrieval-gh-repo-readme/Retrieval-Final.mp4)。

## 简介
ChatGPT 检索插件仓库提供了一种灵活的解决方案，用于使用自然语言查询进行个人或组织文档的语义搜索和检索。该仓库分为几个目录：

| 目录                         | 描述                                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| [`datastore`](/datastore)    | 包含使用各种向量数据库提供商存储和查询文档嵌入的核心逻辑。                                                         |
| [`docs`](/docs)              | 包括设置和使用每个向量数据库提供商、Webhook 和删除未使用的依赖项的文档。                                            |
| [`examples`](/examples)       | 提供示例配置、认证方法和特定提供者的示例。                                   |
| [`models`](/models)           | 包含插件使用的数据模型，如文档和元数据模型。                                         |
| [`scripts`](/scripts)         | 提供用于处理和上传来自不同数据源的文档的脚本。                                         |
| [`server`](/server)           | 包含主要的 FastAPI 服务器实现。                                                                             |
| [`services`](/services)       | 包含用于任务如分块、元数据提取和 PII 检测的实用服务。                                 |
| [`tests`](/tests)             | 包含针对各种向量数据库提供者的集成测试。                                                          |
| [`.well-known`](/.well-known) | 存储插件清单文件和 OpenAPI 架构，这些定义了插件配置和 API 规范。           |


这份 README 提供了关于如何设置、开发和部署 ChatGPT 检索插件的详细信息。

## 目录
翻译问题, 索引可能跳转失败

- [快速入门](#quickstart)
- [关于](#about)
  - [插件](#plugins)
  - [检索插件](#retrieval-plugin)
  - [记忆功能](#memory-feature)
  - [安全性](#security)
  - [API 端点](#api-endpoints)
- [开发](#development)
  - [设置](#setup)
    - [通用环境变量](#general-environment-variables)
  - [选择向量数据库](#choosing-a-vector-database)
    - [Pinecone](#pinecone)
    - [Weaviate](#weaviate)
    - [Zilliz](#zilliz)
    - [Milvus](#milvus)
    - [Qdrant](#qdrant)
    - [Redis](#redis)
  - [在本地运行API](#running-the-api-locally)
  - [在ChatGPT中测试本地主机插件](#testing-a-localhost-plugin-in-chatgpt)
  - [个性化](#personalization)
  - [认证方法](#authentication-methods)
- [部署](#deployment)
- [安装开发者插件](#installing-a-developer-plugin)
- [Webhooks](#webhooks)
- [脚本](#scripts)
- [限制](#limitations)
- [贡献者](#contributors)
- [未来方向](#future-directions)


按照 README 中的 [在 ChatGPT 中测试本地插件](#testing-a-localhost-plugin-in-chatgpt) 部分的说明进行操作。
有关设置、开发和部署 ChatGPT 检索插件的更详细信息，请参阅下面的完整开发部分。


## 快速入门
按照以下步骤快速设置并运行 ChatGPT 检索插件：

1. 如果尚未安装，安装 Python 3.10。
2. 克隆仓库：`git clone https://github.com/openai/chatgpt-retrieval-plugin.git`
3. 导航到克隆的仓库目录：`cd /path/to/chatgpt-retrieval-plugin`
4. 安装 poetry：`pip install poetry`
5. 使用 Python 3.10 创建一个新的虚拟环境：`poetry env use python3.10`
6. 激活虚拟环境：`poetry shell`
7. 安装应用依赖项：`poetry install`
8. 创建一个 [bearer token](#general-environment-variables)
9. 设置所需的环境变量：
```
export DATASTORE=<your_datastore>
export BEARER_TOKEN=<your_bearer_token>
export OPENAI_API_KEY=<your_openai_api_key>

# Add the environment variables for your chosen vector DB.
# Some of these are optional; read the provider's setup docs in /docs/providers for more information.

# Pinecone
export PINECONE_API_KEY=<your_pinecone_api_key>
export PINECONE_ENVIRONMENT=<your_pinecone_environment>
export PINECONE_INDEX=<your_pinecone_index>

# Weaviate
export WEAVIATE_HOST=<your_weaviate_instance_url>
export WEAVIATE_PORT=<your_weaviate_port_443_for_WCS>
export WEAVIATE_CLASS=<your_optional_weaviate_class>
export WEAVIATE_USERNAME=<your_weaviate_WCS_username>
export WEAVIATE_PASSWORD=<your_weaviate_WCS_password>
export WEAVIATE_SCOPES=<your_optional_weaviate_scopes>
export WEAVIATE_BATCH_SIZE=<optional_weaviate_batch_size>
export WEAVIATE_BATCH_DYNAMIC=<optional_weaviate_batch_dynamic>
export WEAVIATE_BATCH_TIMEOUT_RETRIES=<optional_weaviate_batch_timeout_retries>
export WEAVIATE_BATCH_NUM_WORKERS=<optional_weaviate_batch_num_workers>

# Zilliz
export ZILLIZ_COLLECTION=<your_zilliz_collection>
export ZILLIZ_URI=<your_zilliz_uri>
export ZILLIZ_USER=<your_zilliz_username>
export ZILLIZ_PASSWORD=<your_zilliz_password>

# Milvus
export MILVUS_COLLECTION=<your_milvus_collection>
export MILVUS_HOST=<your_milvus_host>
export MILVUS_PORT=<your_milvus_port>
export MILVUS_USER=<your_milvus_username>
export MILVUS_PASSWORD=<your_milvus_password>

# Qdrant
export QDRANT_URL=<your_qdrant_url>
export QDRANT_PORT=<your_qdrant_port>
export QDRANT_GRPC_PORT=<your_qdrant_grpc_port>
export QDRANT_API_KEY=<your_qdrant_api_key>
export QDRANT_COLLECTION=<your_qdrant_collection>

# Redis
export REDIS_HOST=<your_redis_host>
export REDIS_PORT=<your_redis_port>
export REDIS_PASSWORD=<your_redis_password>
export REDIS_INDEX_NAME=<your_redis_index_name>
export REDIS_DOC_PREFIX=<your_redis_doc_prefix>
export REDIS_DISTANCE_METRIC=<your_redis_distance_metric>
export REDIS_INDEX_TYPE=<your_redis_index_typ
```

10.运行本地 API：`poetry run start`

11.访问 API 文档：`http://0.0.0.0:8000/docs` 并测试 API 端点（确保添加你的令牌）。

### Testing in ChatGPT

1.在 localhost 上运行 API：`poetry run dev` 

2.按照 README 中 [在 ChatGPT 中测试本地托管的插件](#testing-a-localhost-plugin-in-chatgpt) 部分的说明进行操作。

如需获得有关设置、开发和部署 ChatGPT 检索插件的更详细信息，请参阅下面的完整开发部分。

## 关于

### 插件

插件是专为像 ChatGPT 这样的语言模型设计的聊天扩展，使它们能够根据用户的请求访问最新信息、进行计算或与第三方服务进行交互。它们解锁了广泛的潜在用例，并增强了语言模型的能力。

开发者可以通过在他们的网站上公开一个 API 并提供描述该 API 的标准化清单文件来创建一个插件。ChatGPT 使用这些文件，并允许 AI 模型对开发者定义的 API 进行调用。

一个插件包括：

- 一个 API
- 一个 API 架构（OpenAPI JSON 或 YAML 格式）
- 一个定义插件相关元数据的清单（JSON 文件）

检索插件已经包含了所有这些组件。在[这里](https://openai.com/blog/chatgpt-plugins)阅读 Chat 插件的博客文章，并在[这里](https://platform.openai.com/docs/plugins/introduction)找到文档。

### 检索插件

这是一个用于 ChatGPT 的插件，它可以进行个人或组织文档的语义搜索和检索。它允许用户通过以自然语言提问或表达需求，从他们的数据源（如文件、笔记或电子邮件）中获取最相关的文档片段。企业可以使用这个插件通过 ChatGPT 将他们的内部文档提供给他们的员工。

该插件使用OpenAI的文本嵌入模型`text-embedding-ada-002`来生成文档块的嵌入，并使用后端的向量数据库进行存储和查询。作为一个开源的、自托管的解决方案，开发人员可以部署自己的检索插件并在ChatGPT上注册。检索插件支持多个向量数据库提供者，允许开发人员从列表中选择自己喜欢的向量数据库。

FastAPI服务器公开插件的端点以进行文档的插入、查询和删除。
用户可以使用按来源、日期、作者或其他标准的元数据过滤器来细化其搜索结果。
该插件可以托管在任何支持Docker容器的云平台上，如Fly.
io、Heroku或Azure Container Apps。为了保持向量数据库与最新文档同步，插件可以持续处理并存储来自各种数据源的文档，使用传入的 Webhooks 对 upsert 和 delete 端点进行更新。像 [Zapier](https://zapier.com) 或 [Make](https://www.make.com) 等工具可以根据事件或计划配置 Webhooks。


### 记忆功能
检索插件的一个显著特性是其为 ChatGPT 提供记忆的能力。通过利用插件的 upsert 端点，ChatGPT 可以将对话中的片段保存到向量数据库中以供后续参考（只有在用户提示时才这样做）。这个功能通过允许 ChatGPT 记住并检索来自先前对话的信息，为更具上下文感知的聊天体验做出了贡献。在[这里](/examples/memory)了解如何配置具有记忆功能的检索插件。


### 安全性
检索插件允许 ChatGPT 搜索向量数据库的内容，然后将最佳结果添加到 ChatGPT 会话中。这意味着它没有任何外部效应，主要的风险考虑是数据授权和隐私。开发者只应将他们有授权的内容添加到他们的检索插件中，并且他们愿意让这些内容出现在用户的 ChatGPT 会话中。


 ### API Endpoints 
检索插件使用FastAPI构建，这是一种用Python构建API的Web框架。FastAPI允许轻松开发、验证和文档化API终点。在此处找到FastAPI文档[here](https://fastapi.tiangolo.com/)。使用FastAPI的一个好处是自动生成交互式API文档与Swagger UI。当API在本地运行时，可以使用Swagger UI在`<local_host_url i.e. http://0.0.0.0:8000>/docs`与API终点进行交互，测试其功能，并查看预期的请求和响应模型。

插件为向量数据库提供以下端点，可进行文档插入、查询和删除。所有请求和响应均为JSON格式，并需要一个有效的令牌作为授权头。
 - `/upsert`：此端点允许上传一个或多个文档，并将它们的文本和元数据存储在向量数据库中。文档被分成大约200个标记的块，每个块有一个唯一的ID。端点在请求体中期望文档列表，每个文档都有一个`text`字段和可选的`id`和`metadata`字段。`metadata`字段可以包含以下可选子字段：`source`，`source_id`，`url`，`created_at`和`author`。端点返回已插入文档的ID列表（如果未提供，则生成一个ID）。
 - `/upsert-file`：此端点允许上传单个文件（PDF，TXT，DOCX，PPTX或MD），并将其文本和元数据存储在向量数据库中。该文件已转换为纯文本，并分成大约200个标记的块，每个块都有一个唯一的ID。该端点返回一个包含插入文件生成的ID的列表。
- `/query`：这个端点允许使用一个或多个自然语言查询和可选的元数据过滤器查询向量数据库。端点期望在请求正文中使用查询列表，每个查询都有一个`query`和可选的`filter`和`top_k`字段。`filter`字段应包含以下子字段的子集：`source`，`source_id`，`document_id`，`url`，`created_at`和`author`。`top_k`字段指定要返回给定查询的结果数，默认值为3。该端点返回一个对象列表，每个对象包含给定查询的最相关文档块的列表，以及它们的文本、元数据和相似度分数。
- `/delete`：这个端点允许使用它们的ID、元数据过滤器或delete_all标志从向量数据库中删除一个或多个文档。该终端点在请求正文中需要至少一个以下参数：`ids`、`filter`或`delete_all`。 `ids`参数应该是要删除的文档ID列表；这些IDS文档的所有文档块都将被删除。`filter`参数应该包含以下子字段的子集：`source`、`source_id`、`document_id`、`url`、`created_at`和`author`。`delete_all`参数应该是一个布尔值，表示是否从向量数据库中删除所有文档。终端点返回一个布尔值，表示删除是否成功。


请求和响应模型的详细规格和示例可以通过在本地运行应用程序并导航到http://0.0.0.0:8000/openapi.json或在OpenAPI架构 [here]（/.well-known/openapi.yaml）中找到。请注意，OpenAPI架构仅包含`/query`终端点，因为这是ChatGPT需要访问的唯一功能。这样，ChatGPT 可以仅基于自然语言查询或需求来使用插件检索相关文档。但是，如果开发人员还想让 ChatGPT 能够记住一些东西以供以后使用，他们可以使用 `/upsert` 端点将对话片段保存到向量数据库中。可以在 [这里](/examples/memory) 找到一个提供 ChatGPT 访问 `/upsert` 端点的清单和 OpenAPI 模式的示例。

要包含自定义元数据字段，请编辑 `DocumentMetadata` 和 `DocumentMetadataFilter` 数据模型 [here](/models/models.py)，并更新 OpenAPI 模式 [here](/.well-known/openapi.yaml)。你可以通过在本地运行应用程序、复制 http://0.0.0.0:8000/sub/openapi.json 中找到的 JSON，并使用 [Swagger Editor](https://editor.swagger.io/) 将其转换为 YAML 格式来轻松更新它。或者，您可以将 `openapi.yaml` 文件替换为 `openapi.json` 文件。




## 开发

### 设置

此应用使用Python 3.10和[poetry](https://python-poetry.org/)来管理依赖项。

如果您的计算机尚未安装Python 3.10，请先安装。您可以从官方[Python网站](https://www.python.org/downloads/)下载，或者使用像`brew`或`apt`这样的软件包管理器，具体取决于您的系统。

从GitHub克隆存储库：

```
git clone https://github.com/openai/chatgpt-retrieval-plugin.git
```

Navigate to the cloned repository directory:

```
cd /path/to/chatgpt-retrieval-plugin
```

Install poetry:

```
pip install poetry
```

Create a new virtual environment that uses Python 3.10:

```
poetry env use python3.10
poetry shell
```

Install app dependencies using poetry:

```
poetry install
```

**注意：** 如果在`pyproject.toml`中添加依赖项，请确保运行`poetry lock`和`poetry install`。


#### 一般环境变量

API需要以下环境变量才能正常工作： 

| 名称             | 必填 | 描述                                                                                                                                                                                |
| ---------------- | ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DATASTORE`      | 是   | 这指定了您想要使用的向量数据库提供程序来存储和查询嵌入。您可以从`pinecone`、`weaviate`、`zilliz`、`milvus`、`qdrant`或`redis`中进行选择。        |
| `BEARER_TOKEN`   | 是   | 这是一个秘密令牌，您需要使用它来对API进行身份验证。您可以使用任何工具或方法来生成一个，例如[jwt.io](https://jwt.io/)。  |
| `OPENAI_API_KEY` | 是   | 这是您的OpenAI API密钥，您需要使用它来生成使用`text-embedding-ada-002`模型的嵌入。您可以通过在[OpenAI](https://openai.com/)上创建帐户来获取API密钥。 |


### 选择向量数据库

该插件支持多个向量数据库提供程序，每个提供程序都具有不同的功能、性能和定价。根据您选择的提供程序，您将需要使用不同的Dockerfile并设置不同的环境变量。以下部分提供了对每个向量数据库提供程序的简要介绍。

有关设置和使用每个向量数据库提供程序的更详细说明，请参阅`/docs/providers/<datastore_name>/setup.md`文件中的相应文档（[此处的文件夹](/docs/providers)）。

#### Pinecone

[Pinecone](https://www.pinecone.io)是一个管理的向量数据库，旨在实现快速、可扩展和快速部署到生产环境。它支持混合搜索，并且目前是唯一本地支持SPLADE稀疏向量的数据存储。有关详细的设置说明，请参阅[`/docs/providers/pinecone/setup.md`](/docs/providers/pinecone/setup.md)。

#### Weaviate

[Weaviate](https://weaviate.io/)是一个开源的向量搜索引擎，可无缝地扩展到数十亿个数据对象。它支持混合搜索，适用于需要高效关键字搜索的用户。Weaviate可以自主托管或托管，提供部署灵活性。有关详细的设置说明，请参阅[`/docs/providers/weaviate/setup.md`](/docs/providers/weaviate/setup.md)。

#### Zilliz

[Zilliz](https://zilliz.com)是一个管理的云原生向量数据库，旨在处理十亿级数据。它提供多种功能，包括多个索引算法、距离度量、标量过滤、时间旅行搜索、带快照回滚、完整的RBAC、99.9%的正常运行时间、分离存储和计算以及多语言SDK。有关详细的设置说明，请参阅[`/docs/providers/zilliz/setup.md`](/docs/providers/zilliz/setup.md)。 

#### Milvus

[Milvus](https://milvus.io/)是一个开源的云原生向量数据库，可扩展到数十亿个向量。它是Zilliz的开源版本，并共享许多其功能，例如各种索引算法、距离度量、标量过滤、时间旅行搜索、带快照回滚、多语言SDK、存储和计算分离以及云可扩展性。有关详细的设置说明，请参阅[`/docs/providers/milvus/setup.md`](/docs/providers/milvus/setup.md)。

#### Qdrant

[Qdrant](https://qdrant.tech/)是一个能够存储文档和向量嵌入的向量数据库。它提供了自主托管和托管的[Qdrant Cloud](https://cloud.qdrant.io/)部署选项，为具有不同需求的用户提供了灵活性。有关详细的设置说明，请参阅[`/docs/providers/qdrant/setup.md`](/docs/providers/qdrant/setup.md)。

#### Redis

[Redis](https://redis.com/solutions/use-cases/vector-database/)是一个实时数据平台，适用于各种用例，包括日常应用程序和AI/ML工作负载。通过使用[Redis Stack docker容器](/examples/docker/redis/docker-compose.yml)创建Redis数据库，它可以用作低延迟向量引擎。如果需要托管/托管的解决方案，可以使用[Redis Cloud](https://app.redislabs.com/#/)。有关详细的设置说明，请参阅[`/docs/providers/redis/setup.md`](/docs/providers/redis/setup.md)。

#### LlamaIndex

[LlamaIndex](https://github.com/jerryjliu/llama_index)是一个中心接口，可将您的LLM与外部数据连接起来。它为ChatGPT提供了一套在内存中对您的非结构化和结构化数据进行索引的工具。与标准的向量数据库不同，LlamaIndex支持广泛的索引策略（例如树形、关键词表、知识图），针对不同的用例进行了优化。它轻巧、易于使用，不需要额外的部署。您只需要指定一些环境变量（可选地指向现有的已保存索引json文件）即可。请注意，查询中的元数据过滤器尚未得到支持。有关详细的设置说明，请参阅[`/docs/providers/llama/setup.md`](/docs/providers/llama/setup.md)。


### 在本地运行API

要在本地运行API，您首先需要使用`export`命令设置必需的环境变量： 

```
export DATASTORE=<your_datastore>
export BEARER_TOKEN=<your_bearer_token>
export OPENAI_API_KEY=<your_openai_api_key>
<Add the environment variables for your chosen vector DB here>
```

Start the API with:

```
poetry run start
```

将`docs`附加到终端中显示的URL中，并在浏览器中打开它以访问API文档并尝试端点（即http://0.0.0.0:8000/docs）。确保输入您的bearer令牌并测试API端点。

**注意：**如果您将新依赖项添加到pyproject.toml文件中，则需要运行`poetry lock`和`poetry install`来更新锁文件并安装新依赖项。

### 在ChatGPT中测试本地主机插件

要在ChatGPT中测试本地主机插件，请使用提供的[`local-server/main.py`](/local-server/main.py)文件，该文件专门配置为具有CORS设置、无身份验证和清单、OpenAPI模式和徽标路由的本地主机测试。

按照以下步骤测试您的本地主机插件：

1.使用`poetry run dev`命令运行本地主机服务器。这将在默认地址（例如`localhost:3333`）启动服务器。

2.访问[ChatGPT](https://chat.openai.com/)，从模型选择器中选择“插件”，单击插件选择器，然后单击列表底部的“插件商店”。

3.选择“开发您自己的插件”，并在提示时输入您的本地主机URL（例如`localhost:3333`）。

4.您的本地主机插件现已启用，用于您的ChatGPT会话。

有关更多信息，请参阅[OpenAI文档](https://platform.openai.com/docs/plugins/getting-started/openapi-definition)。

### 个性化

您可以通过执行以下操作为自己的用例个性化检索插件：

- **更换徽标**：将[logo.png](/.well-known/logo.png)中的图像替换为您自己的徽标。

- **编辑数据模型**：编辑[models.py](/models/models.py)中的`DocumentMetadata`和`DocumentMetadataFilter`数据模型，以添加自定义元数据字段。相应更新[openapi.yaml](/.well-known/openapi.yaml)中的OpenAPI模式。为了更轻松地更新OpenAPI模式，您可以在本地运行应用程序，然后导航到`http://0.0.0.0:8000/sub/openapi.json`并复制网页的内容。然后转到[Swagger Editor](https://editor.swagger.io/)并粘贴JSON以将其转换为YAML格式。您还可以将[openapi.yaml](/.well-known/openapi.yaml)文件替换为[.well-known](/.well-known)文件夹中的openapi.json文件。

- **更改插件名称、描述和使用说明**：更新模型的插件名称、用户面向的描述和使用说明。您可以在[main.py](/server/main.py)文件中编辑描述，也可以更新[openapi.yaml](/.well-known/openapi.yaml)文件。按照上一步中的相同说明更新OpenAPI模式。


- **启用 ChatGPT 以保存对话中的信息**: 请参照这个说明 [memory example folder](/examples/memory).

### 身份验证方法

您可以从以下四个选项中选择身份验证插件请求的方法：

1. **无验证**：任何人都可以添加您的插件并在没有任何凭据的情况下使用其 API。如果您只公开不敏感或已公开的文档，则此选项很合适。它不提供您数据的安全性。如果使用此方法，请将 [main.py](/examples/authentication-methods/no-auth/main.py) 的内容复制到 [实际的 main.py 文件](/server/main.py) 中。示例清单[在此处](/examples/authentication-methods/no-auth/ai-plugin.json)。

2. **HTTP Bearer**：您可以使用一个密钥令牌作为标头来授权对插件的请求。有两种变体的选项：

  - **用户级别**（此实现的默认级别）：每个将您的插件添加到ChatGPT的用户在添加插件时必须提供令牌。您可以使用任何您喜欢的工具或方法（例如[jwt.io](https://jwt.io/)）生成和分发这些令牌。此方法提供更好的安全性，因为每个用户都必须输入共享访问令牌。如果您需要为每个用户提供唯一的访问令牌，则需要在[main.py](/server/main.py)文件中自行实现。示例清单[在此处](/examples/authentication-methods/user-http/ai-plugin.json)。

  - **服务级别**：任何人都可以添加您的插件并使用其API，而无需凭据，但您必须在注册插件时添加访问令牌。安装插件时，您需要添加您的访问令牌，然后将从ChatGPT接收一个令牌，您必须将其包含在托管的清单文件中。ChatGPT将使用您的令牌代表所有添加它的用户授权访问您的插件。此方法对用户更方便，但安全性可能较低，因为所有用户共享同一令牌，并且不需要添加令牌来安装插件。示例清单[在此处](/examples/authentication-methods/service-http/ai-plugin.json)。


3. **OAuth**：用户必须通过OAuth流程才能添加您的插件。您可以使用OAuth提供程序对添加您的插件的用户进行身份验证，并授予他们访问您的API的权限。此方法提供了最高级别的安全性和控制，因为用户通过受信任的第三方提供程序进行身份验证。但是，您需要在[main.py](/server/main.py)文件中自己实现OAuth流程，并在清单文件中提供必要的参数。示例清单[在此处](/examples/authentication-methods/oauth/ai-plugin.json)。

在选择最适合您的用例和安全要求的身份验证方法之前，请考虑每种身份验证方法的优缺点。如果您选择使用不同于默认值（用户级别HTTP）的方法，请确保更新清单文件[在此处](/.well-known/ai-plugin.json)。







## 部署
您可以将应用程序部署到不同的云提供商，具体取决于您的偏好和要求。但是，无论您选择哪个提供商，您都需要更新应用程序中的两个文件：[openapi.yaml]（/.well-known/openapi.yaml）和[ai-plugin.json]（/.well-known/ai-plugin.json）。如上所述，这些文件分别定义了 API 规范和您的应用程序的 AI 插件配置。您需要在两个文件中更改 url 字段，以匹配您部署的应用程序的地址。在部署应用程序之前，您可能需要从 [pyproject.toml]（/pyproject.toml）文件中删除未使用的依赖项，以减小应用程序的大小并提高其性能。根据您选择的向量数据库提供商，可以删除不需要针对您特定提供商的软件包。

请参考[`/docs/deployment/removing-unused-dependencies.md`]文件的各自文档，了解每个提供程序的删除未使用依赖关系的信息。一旦您部署了您的应用程序，考虑使用其中一个[这些脚本](/scripts)或通过调用`/upsert`端点上传初始批文档。以下是各种平台的详细部署说明：
- [部署到Fly.io](/docs/deployment/flyio.md)
- [部署到Heroku](/docs/deployment/heroku.md)
- [其他部署选项](/docs/deployment/other-options.md)（Azure容器应用程序、Google Cloud Run、AWS Elastic Container Service等）。

创建应用程序后，请确保更改插件清单文件中的插件URL[在这里]（/.well-known/ai-plugin.json），并在您的OpenAPI模式文件[在此处]（/.well-known/openapi.yaml）中重新部署。


## 安装开发插件 
要安装开发插件，请按照以下步骤进行：
- 首先，在您的首选托管平台（例如 Fly.
io、Heroku 等）上部署开发插件，并在清单文件和 OpenAPI 模式中更新插件 URL。
- 转到 [ChatGPT](https://chat.openai.com/)，从模型选择器中选择 “插件”。
- 在插件选择器中，向下滚动到底部，然后单击 “插件商店”。
- 转到 “开发自己的插件”，并按照提供的说明进行操作。
您需要输入插件部署的域。
- 根据您为插件选择的身份验证类型遵循说明（例如，如果您的插件使用 ervice Level HTTP，则必须粘贴您的访问令牌，然后将插件流程中收到的新访问令牌复制并粘贴到您的 [ai-plugin.json](/.well-known/ai-plugin.json) 文件中，并重新部署应用程序）。
- 接下来，您必须添加您的插件。再次转到 “插件商店”，然后单击 “安装未验证的插件”。
- 请按照提供的说明进行操作，这将要求您输入部署插件的域名。
- 根据您为插件选择的身份验证类型，按照说明进行操作（例如，如果您的插件使用用户级HTTP，您将需要粘贴您的Bearer令牌）。


完成这些步骤后，您的开发者插件应该已经安装好并可以在ChatGPT中使用。


## Webhooks 
为了保持向量数据库中存储的文档最新，考虑使用像[Zapier](https://zapier.com)或[Make](https://www.make.com)这样的工具，根据事件或时间表配置入站Webhooks到您的插件API。例如，这可以允许您在更新笔记或接收电子邮件时同步新信息。您还可以使用[Zapier转移](https://zapier.com/blog/zapier-transfer-guide/)来批量处理一组现有文档并将它们上传到向量数据库。

如果您需要从这些工具中传递自定义字段到您的插件中，您可能需要创建一个额外的检索插件API端点，调用数据存储的插入/更新功能，例如`upsert-email`。
此自定义端点可以被设计为从Webhook中接受特定的字段并相应地处理它们。
要设置一个传入的Webhook，请按照以下一般步骤：-选择一个Webhook工具，如Zapier或Make，并创建一个账户。
- 设置一个新的Webhook或将它转移到工具中，并根据事件或计划配置它以触发。
- 指定Webhook的目标URL，该URL应该是您检索插件的API端点（例如`https://your-plugin-url.com/upsert`）。
- 根据检索插件的API要求配置Webhook有效负载以包括必要的数据字段并将其格式化。
- 测试Webhook以确保它正常工作并按预期将数据发送到您的检索插件。

设置 Webhook 后，您可能需要运行回溯以确保向量数据库中包括任何先前漏掉的数据。请记住，如果要使用传入的 Webhook 连续同步数据，应在设置后考虑运行补充以避免遗漏任何数据。除使用 Zapier 和 Make 等工具外，您还可以构建自己的自定义集成来将数据与检索插件同步。这使您可以更好地控制数据流并根据自己的特定需求和要求来定制集成。



## 脚本
`scripts` 文件夹包含用于批量插入或处理来自不同数据源（如 zip 文件、JSON 文件或 JSONL 文件）的文本文档的脚本。这些脚本使用插件的 upsert 工具函数将文档及其元数据上传到向量数据库，将它们转换为纯文本并将其拆分成块。每个脚本文件夹都有一个 README 文件，说明如何使用它以及需要哪些参数。您还可以使用语言模型可选地筛选包含个人身份信息（PII）的文档，并在检测到时跳过它们，使用 [`services.pii_detection`](/services/pii_detection.py) 模块即可。这样做有助于避免无意上传敏感或私人文档到向量数据库中。此外，您还可以使用语言模型可选地从文档文本中提取元数据，使用 [`services.extract_metadata`](/services/extract_metadata.py) 模块即可。如果您想丰富文档的元数据，则此工具非常有用。**注意：**如果使用传入的 WebHooks 连续同步数据，请在设置后运行回填来避免丢失任何数据。脚本为：
- [`process_json`](scripts/process_json/)：此脚本处理 JSON 格式的文件转储中的文档，并将其存储到向量数据库中，同时添加一些元数据。JSON 文件的格式应该是一个包含多个 JSON 对象的列表，其中每个对象代表一个文档。JSON对象应该有一个`text`字段，还可以选填其他字段来填充元数据。您可以提供自定义元数据作为JSON字符串和标志来筛选PII并提取元数据。

- [`process_jsonl`](scripts/process_jsonl/):此脚本处理JSONL格式的文档文件转储，并存储它们具有一些元数据的向量数据库中。 JSONL文件的格式应该是一个以换行符分隔的JSON文件，其中每行都是一个有效的JSON对象，表示一个文档。 JSON对象应该有一个`text`字段，还可以选填其他字段来填充元数据。您可以提供自定义元数据作为JSON字符串和标志来筛选PII并提取元数据。


- [`process_zip`](scripts/process_zip/):此脚本处理zip文件中的文档文件转储，并存储它们具有一些元数据的向量数据库中。 zip文件的格式应该是docx，pdf，txt，md，pptx或csv文件的扁平zip文件夹。您可以提供自定义元数据作为JSON字符串和标志，以屏蔽PII并提取元数据。

## 限制
虽然ChatGPT检索插件旨在提供一种灵活的语义搜索和检索解决方案，但它确实有一些限制： 
- **关键字搜索限制**：由“text-embedding-ada-002”模型生成的嵌入可能并不总是能够有效地捕获精确的关键字匹配。因此，插件可能无法返回对特定关键字的查询非常依赖的最相关结果。一些矢量数据库，如Pinecone和Weaviate，使用混合搜索可能表现更好，适用于关键字搜索。
- **敏感数据处理**：该插件不会自动检测或过滤敏感数据。
开发人员有责任确保他们有必要的授权来包含检索插件中的内容，并且内容符合数据隐私要求。
- **可伸缩性**：插件的性能取决于所选择的向量数据库提供商和数据集的大小。有些提供商可能比其他提供商提供更好的可伸缩性和性能。
- **语言支持**：插件目前使用的是 OpenAI 的 `text-embedding-ada-002 ` 模型，该模型是针对英语进行优化的。然而，它仍然足够强大，能够为各种语言生成出色的结果。
- **元数据提取**：可选的元数据提取功能依赖于一种语言模型，用于从文本中提取信息。这个过程可能不总是准确的，提取的元数据的质量可能取决于文档内容和结构。
- **PII 检测**：可选的 PII 检测功能不是百分百可靠的，可能无法捕捉到所有个人可识别信息的实例。请谨慎使用此功能，并验证其对您特定用例的有效性。



## 未来发展方向

ChatGPT检索插件提供了灵活的语义搜索和检索解决方案，但仍有进一步的发展潜力。
我们鼓励用户通过提交拉取请求来为项目做出贡献以实现新功能或增强性能。
值得注意的贡献将获得OpenAI积分。
未来发展方向的一些想法包括： 

- **更多向量数据库提供商**：如果你有兴趣与ChatGPT检索插件集成另一个向量数据库提供商，请随时提交实现。
 - **附加脚本**：扩展可用于处理和上传来自各种数据源的文档的脚本范围将使插件更加多才多艺。
 - **用户界面**：开发用于管理文档和与插件交互的用户界面可以改善用户体验。
 - **混合搜索/ TF-IDF选项**：增强[datastore的upsert函数](/datastore/datastore.md#upsert)，以允许混合搜索使用语义向量和TF-IDF分数，可以提供更准确的检索结果。
- **多语言支持**：添加对多种语言的支持将允许插件在更广泛的上下文中使用，并提高工具的包容性。我们期待着看到ChatGPT检索插件将如何继续发展并满足其用户的需求！使用混合搜索或TF-IDF索引选项可能会提高基于关键字的查询插件的性能。
 - **高级分块策略和嵌入计算**：实现更复杂的分块策略和嵌入计算，如嵌入文档标题和摘要，进行文档块和摘要的加权平均，或计算文档的平均嵌入，可能会导致更好的搜索结果。
 - **自定义元数据**：允许用户向文档块添加自定义元数据，如标题或其他相关信息，可能会在某些使用情况下提高检索结果。
 - **附加可选服务**：集成更多可选服务，如文档摘要或在嵌入它们之前对文档进行预处理，可以增强插件的功能和检索结果的质量。这些服务可以使用语言模型直接集成到插件中，而不仅仅在脚本中可用。


我们欢迎社区的贡献帮助改进 ChatGPT 检索插件，扩展其功能。
如果您有想法或功能要贡献，请提交拉取请求到存储库。




## 贡献者
我们要向以下贡献者表达我们的感激，感谢他们的代码/文档贡献以及支持将各种向量数据库提供商与 ChatGPT 检索插件集成:

- [Pinecone](https://www.pinecone.io/)
  - [acatav](https://github.com/acatav)
  - [gkogan](https://github.com/gkogan)
  - [jamescalam](https://github.com/jamescalam)
- [Weaviate](https://www.semi.technology/)
  - [byronvoorbach](https://github.com/byronvoorbach)
  - [hsm207](https://github.com/hsm207)
  - [sebawita](https://github.com/sebawita)
- [Zilliz](https://zilliz.com/)
  - [filip-halt](https://github.com/filip-halt)
- [Milvus](https://milvus.io/)
  - [filip-halt](https://github.com/filip-halt)
- [Qdrant](https://qdrant.tech/)
  - [kacperlukawski](https://github.com/kacperlukawski)
- [Redis](https://redis.io/)
  - [spartee](https://github.com/spartee)
  - [tylerhutcherson](https://github.com/tylerhutcherson)
