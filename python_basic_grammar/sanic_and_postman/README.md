# Sanic and Postman

Sanic 是一个用于构建异步（asynchronous）Web应用的Python框架，它基于 Python 3.5+ 引入的 `async/await` 语法，旨在提供高性能的 Web 服务器和路由处理功能。它的设计灵感来自于 Flask，但强调了异步处理，适用于高并发的应用。<br>

- [Sanic and Postman](#sanic-and-postman)
  - [Sanic的安装](#sanic的安装)
  - [简单sanic应用示例：](#简单sanic应用示例)
  - [使用Postman测试Sanic启动的服务：](#使用postman测试sanic启动的服务)
    - [Postman 简介：](#postman-简介)
    - [Postman 测试 Sanic 服务：](#postman-测试-sanic-服务)
  - [GET和POST方法：](#get和post方法)
    - [GET 方法进阶：](#get-方法进阶)
    - [POST 方法：](#post-方法)
    - [POST 方法路由的测试：](#post-方法路由的测试)
    - [Postman 中 Body 选项的含义与选择：](#postman-中-body-选项的含义与选择)
  - [request 对象：](#request-对象)
    - [request的属性:](#request的属性)
    - [sanic中的request什么时候用:](#sanic中的request什么时候用)
    - [`request.json` 用法示例:](#requestjson-用法示例)
  - [response 对象：](#response-对象)
  - [高级功能:](#高级功能)
    - [基于类的视图(Class Based Views)：](#基于类的视图class-based-views)
      - [为什么使用它们(Why use them)？](#为什么使用它们why-use-them)
      - [问题所在(The problem)：](#问题所在the-problem)
      - [解决方案(The solution)：](#解决方案the-solution)
    - [Streaming:](#streaming)
      - [Response streaming:](#response-streaming)
  - [使用 Postman 动态更新Sanic服务中类属性：](#使用-postman-动态更新sanic服务中类属性)
    - [在主文件中定义路由：](#在主文件中定义路由)
    - [附属文件中定义用户输出的处理流程：](#附属文件中定义用户输出的处理流程)
    - [工具型函数中定义文本处理、变量更新细节：](#工具型函数中定义文本处理变量更新细节)
    - [运行方式：](#运行方式)
  - [使用Postman+global动态更新Sanic服务中的数据：](#使用postmanglobal动态更新sanic服务中的数据)
    - [任务目标：](#任务目标)
    - [主文件：](#主文件)
    - [工具文件：](#工具文件)
    - [数据存入redis的文件：](#数据存入redis的文件)
    - [清空Redis数据(可选)：](#清空redis数据可选)
    - [文件运行顺序：](#文件运行顺序)
    - [代码解释：](#代码解释)
  - [启动Sanic服务时只加载一次模型--避免每次调用接口时加载模型的耗时:](#启动sanic服务时只加载一次模型--避免每次调用接口时加载模型的耗时)
    - [模拟场景:](#模拟场景)
    - [解决方案-sanic应用的上下文:](#解决方案-sanic应用的上下文)
    - [解决方案-全局变量:](#解决方案-全局变量)
    - [应用上下文管理`app.ctx`写法和全局变量写法的区别:](#应用上下文管理appctx写法和全局变量写法的区别)
      - [应用上下文管理 (`app.ctx.classify_model = Classify_data()`):](#应用上下文管理-appctxclassify_model--classify_data)
      - [全局实例化 (`classify_model = Classify_data()`):](#全局实例化-classify_model--classify_data)
      - [选择依据:](#选择依据)
  - [Sanic Blueprint:](#sanic-blueprint)
    - [完整代码--单个蓝图：](#完整代码--单个蓝图)
    - [完整代码--多个蓝图：](#完整代码--多个蓝图)
  - [使用Sanic实现Server Sent Event(SSE):](#使用sanic实现server-sent-eventsse)
    - [`index.html`文件代码如下:](#indexhtml文件代码如下)
    - [`server.py`文件代码如下:](#serverpy文件代码如下)
    - [POST方式进行SSE传输:](#post方式进行sse传输)
    - [结合LLM API的SSE示例:](#结合llm-api的sse示例)
  - [利用Chrome浏览器以非域名的形式解除限制访问自己的服务:](#利用chrome浏览器以非域名的形式解除限制访问自己的服务)
    - [方案一: 为自己服务注册/绑定一个域名。](#方案一-为自己服务注册绑定一个域名)
    - [方案二: 更改Chrome试验模式配置。](#方案二-更改chrome试验模式配置)

## Sanic的安装

pip 安装sanic的指令如下：<br>

```bash
pip install sanic
```

## 简单sanic应用示例：

下面是一个简单的介绍，说明如何使用 Sanic 构建一个基本的 Web 应用：<br>

1. 创建一个 Sanic 应用:

创建一个 Python 文件，例如 `sanic_server.py`(文件名可以随意定义)，并导入 Sanic：<br>

```python
from sanic import Sanic
from sanic import response

app = Sanic(__name__)
```

2. 添加路由和视图函数:

在你的应用中，你可以定义不同的路由，并为每个路由定义视图函数。例如：<br>

> `async def index()`部分被称为视图函数。

```python
@app.route("/")
async def index(request):
    return response.text("Hello, Sanic!")

@app.route("/user/<name>")
async def greet_user(request, name):
    return response.text(f"Hello, {name}!")
```

这里，`@app.route` 装饰器用于指定路由，接受 HTTP 请求，并调用相应的处理函数。<br>

1. 启动 Sanic 应用:

在应用的最后，添加以下代码以运行 Sanic 服务器：<br>

```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

注意: 设定端口前一定要先看这个端口是否被占用，可以使用以下指令查看。<br>

```bash
lsof -i :8000
```

2. 运行应用:

使用以下命令运行你的应用：<br>

```bash
python sanic_server.py
```

现在，你的 Sanic 应用应该已经在本地运行，并监听在端口 8000 上。<br>

这只是 Sanic 的基本用法，它还提供了许多其他功能，如中间件支持、异步请求处理、WebSocket 支持等等，以满足不同类型的 Web 应用需求。你可以查阅官方文档以深入了解更多信息：https://sanicframework.org/<br>

## 使用Postman测试Sanic启动的服务：

### Postman 简介：

Postman 是一种流行的应用程序，用于测试和调试网络 API（应用程序编程接口）。它提供了一个用户友好的界面，允许开发人员轻松地创建、发送和接收 HTTP 请求，以测试 API 的各种端点和功能。Postman 还允许你组织请求、保存和分享测试集合，以及自动化 API 测试工作流程。它可用于多种用途，包括单元测试、端到端测试、性能测试和文档编制等。<br>

工作中经常使用 Postman 测试 Sanic 用于构建 Web 应用程序和 API。使用 Postman 进行单个测试，或者使用 Postman 的自动化功能来创建 API 文档和测试集合。这有助于确保 API 的质量和性能。<br>

Postman 主界面介绍如下：<br>

![Postman主界面](./postman主界面标识.png)

### Postman 测试 Sanic 服务：

🐳🐳🐳下面我们使用以下代码(`sanic_server.py`)介绍如何使用 Postman 测试我们所启动的 Sanic 服务效果如何。<br>

```python
# sanic_server.py
from sanic import Sanic
from sanic import response

app = Sanic(__name__)

@app.route("/")
async def index(request):
    """该路由为GET方法
    postman选择GET方法，在url填入http://8.140.203.xxx:8848/的网址，点击Send即可查看效果。(不需要额外选择参数。)
    """
    return response.text("Hello, Sanic!")

@app.route("/user/<name>")
async def greet_user(request, name):
    """该路由为GET方法
    这个路由包含了一个动态参数 `<name>`，这个参数可以在 URL 中接受用户提供的值。例如，当用户访问类似 http://8.140.203.xxx:8848/user/John 的URL时，会执行 `greet_user` 函数，并将 `name` 参数设置为 "John"，然后返回 "Hello, John!" 的文本响应。
    """
    return response.text(f"Hello, {name}!")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

要使用Postman测试上述Sanic应用程序，请按照以下步骤操作：<br>

1. 启动Sanic应用程序：
   
在命令行中运行你的Python脚本，以启动Sanic应用程序。这将使应用程序在本地运行并监听端口8000。<br>

```bash
python sanic_server.py
```

2. 打开Postman：
   
打开Postman应用程序或访问Postman网站。<br>

3. 创建一个新的请求：

在Postman主界面中，点击"New"（新建）按钮，然后选择HTTP方法。<br>

4. 配置请求：
   
- 在请求的"Request Type"（请求类型）下拉菜单中，选择HTTP方法，如GET、POST等。上述代码我只使用了GET方法的路由，所以请求类型设置为GET。

- 在"Enter request URL"（输入请求URL）字段中，输入Sanic应用程序的URL。默认情况下，它应该是`http://localhost:8000`，但如果你的应用程序在不同的主机或端口上运行，请相应地更改URL。

- 如果你要测试具有参数的路由，如`/user/<name>`，则需要在URL中包含相应的参数，例如`http://localhost:8000/user/John`。

- 在"Headers"（标头）部分，你可以添加任何必要的标头。**通常情况下，不需要添加任何特殊标头。**😀😀😀

- 如果你使用POST请求，你可以在"Body"（正文）部分设置请求正文。根据你的应用程序，可能需要在正文中包含JSON数据或表单数据。

5. 发送请求：

点击"Send"（发送）按钮以发送请求到Sanic应用程序。<br>

6. 查看响应：

Postman将显示从Sanic应用程序接收到的响应。你应该能够看到你的应用程序返回的数据或消息。<br>

通过按照以上步骤配置和发送请求，你可以使用Postman测试你的Sanic应用程序的不同路由和功能。确保你的Sanic应用程序正在运行，并且Postman配置正确以便与其通信。<br>


## GET和POST方法：

### GET 方法进阶：

笔者在上面的示例中定义的两个路由默认都是使用 GET 方法。GET 方法的使用较为简单，但在使用 GET 方法时依旧要注意一些内容：<br>

GET 方法不一定需要传递参数，因为它可以在URL中包含查询字符串（query string）来传递参数。查询字符串是URL中的一部分，通常跟在问号（?）之后，参数之间用“&”符号分隔。例如：<br>

```log
http://example.com/resource?param1=value1&param2=value2
```

在这个URL中，`param1` 和 `param2` 是两个GET请求的参数，它们的值分别是`value1`和`value2`。<br>

在Sanic中，你可以使用`request.args`来获取URL中的查询参数。例如：<br>

```python
from sanic import Sanic
from sanic import response

app = Sanic(__name__)

@app.route("/example")
async def example(request):
    param1 = request.args.get("param1")
    param2 = request.args.get("param2")
    
    return response.text(f"param1: {param1}, param2: {param2}")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

当你访问 `http://localhost:8000/example?param1=value1&param2=value2` 时，Sanic 将从查询字符串中提取参数值，并将它们传递给路由处理函数。然后，你可以在函数内部使用这些参数来执行逻辑操作。<br>

总结一下，GET 方法通常用于从服务器获取数据，参数可以通过查询字符串传递给服务器，然后服务器可以解析查询字符串来获取这些参数的值。 Sanic 的 `request.args` 可以帮助你在路由处理函数中方便地获取这些参数。<br>

### POST 方法：

如果你想要定义一个使用 POST 方法的路由，可以通过使用 `methods` 参数来指定请求方法，如下所示：<br>

```python
from sanic import Sanic
from sanic import response
import jieba

app = Sanic(__name__)

# 使用 GET 方法的路由
@app.route("/")
async def index(request):
    return response.text("Hello, Sanic!")

@app.route("/segment", methods=["POST"])
async def segment(request):
    """分词API"""
    text = request.json.get("text")
    if not text:
        return response.json({"error": "Missing 'text' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"分词结果为：": result})

@app.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"用户数据分词结果为：": result})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

🚀🚀🚀接下来，我讲解下上述代码中路由"/segment"和"/ans"的不同，以及应该如何用postman测试：<br>

1. **路由路径**：

- "/segment"路由用于处理POST请求，其路径为"/segment"。

- "/ans"路由也用于处理POST请求，其路径为"/ans"。

2. **请求参数的获取方式**：

- "/segment"路由使用`request.json.get("text")`来获取请求中的**JSON数据**，并期望数据包含一个名为"text"的字段。

- "/ans"路由使用`request.form.get("usr_input")`来获取**表单数据**，并期望数据包含一个名为"usr_input"的字段。

3. **响应**：

- "/segment"路由返回一个JSON响应，其中包含分词的结果。

- "/ans"路由也返回一个JSON响应，其中包含分词的结果。

### POST 方法路由的测试：

要使用Postman测试"/segment"和"/ans"这两个路由，可以按照以下步骤进行：<br>

1. 打开Postman应用程序。

2. 创建一个新的请求。

3. 在请求的URL栏中输入你的应用程序的地址和端口，例如：http://localhost:8848。

4. 选择HTTP方法为"POST"。

5. 对于"/segment"路由，切换到"Body"选项卡，选择"raw"，然后在文本框中输入JSON数据(**"raw"选项的最后一位带箭头位置选JSON**)，如下所示：

```json
{
    "text": "这是一段文本，需要进行分词。"
}
```

6. 点击发送按钮，你将获得分词结果的JSON响应。

7. 对于"/ans"路由，切换到"Body"选项卡，选择"x-www-form-urlencoded"，然后在"Key"中添加一个名为"usr_input"的字段，并在Value中输入文本，如下所示：

```log
usr_input: 这是一段用户数据，需要进行分词。
```

8. 点击发送按钮，你将获得用户数据分词结果的JSON响应。

这样，你可以使用Postman测试这两个不同的路由并查看它们的不同功能。一个路由期望JSON数据，而另一个期望表单数据。<br>

### Postman 中 Body 选项的含义与选择：
在Postman中，Body选项用于指定请求的消息体（payload）内容。以下是在Body选项中常见的几个选项及其含义：<br>

1. **none:** 这表示请求不包含消息体。选择此选项时，请求通常是GET或DELETE等不需要发送数据的HTTP方法。

2. **form-Data:** 当使用该选项时，请求的消息体将被格式化为multipart/form-data格式。这通常用于上传文件或通过表单提交数据。

3. **x-www-form-urlencoded:** 选择此选项将以URL编码形式发送请求的数据。🔥🔥🔥🔥🔥🔥这种格式通常用于HTML表单提交🔥🔥🔥🔥🔥🔥。

4. **raw:** 当选择此选项时，你可以手动编辑请求的消息体内容。你可以选择不同的格式，例如文本（纯文本）、JSON（应用/JSON）、XML（application/xml）等。

5. **binary:** 选择此选项以上传二进制文件。你可以选择将文件直接从磁盘上载，或者使用代码或其他方式生成二进制数据。

6. **graphQL:** 如果你正在使用GraphQL API，则可以选择此选项，并在请求的消息体中编写GraphQL查询。

7. **file:** 这个选项允许你将文件直接作为请求的消息体。你可以选择从磁盘上载文件，或者从已发送的请求中选择文件。

选择哪个选项取决于你要发送的数据类型和请求的要求。如果你要发送表单数据，你可以选择Form-Data或x-www-form-urlencoded。如果你要发送JSON数据，可以选择Raw并将Content-Type设置为application/json。如果你要上传文件，你可以选择Form-Data或Binary，具体取决于你的需求。<br>

## request 对象：

### request的属性:

**request**包含了客户端（浏览器）发过来的HTTP请求的各类数据。request不需要显示导入，Sanic内部含有。request 包含以下属性：<br>

| 属性   | 使用方式        | 意义                                        |
|--------|-----------------|---------------------------------------------|
| json   | request.json | 当客户端POST来的数据是json格式时，访问json数据 |
| args   | request.args | 查询字符串变量，即URL中问号?后面的部分        |
| files  | 字典            | 拥有name、body和type的文件对象的字典           |
| form   | 表单            | 以POST方式传递的form变量                     |
| body   | 字节串          | POST的原始数据                               |  


### sanic中的request什么时候用:

在 Sanic 这个异步 Web 框架中，`request` 对象是在处理传入的 HTTP 请求时使用的。当一个请求发送到你的 Sanic 服务器时，Sanic 会创建一个 `request` 对象，它包含了这个请求的所有信息，包括请求的 URL、HTTP 方法、头信息、查询参数、表单数据、JSON 数据等。<br>

简单来说，每当你的服务器收到一个 HTTP 请求（无论是 GET 请求、POST 请求、还是其他类型的请求），Sanic 都会为这个请求创建一个 `request` 对象，并将其传递给相应的处理函数。在这个处理函数中，你可以通过 `request` 对象访问请求的所有信息。<br>

下面是一个简单的例子，展示了如何在 Sanic 中使用 `request` 对象：<br>

```python
from sanic import Sanic
from sanic.response import json

app = Sanic("MyApp")

@app.route("/get_data", methods=["GET"])
async def get_data(request):
    # 使用 request.args 获取 URL 查询参数
    query_params = request.args

    # 你可以进一步处理这些参数
    # ...

    return json({"message": "收到 GET 请求", "query_params": query_params})

@app.route("/post_data", methods=["POST"])
async def post_data(request):
    # 使用 request.json 获取 JSON 格式的请求体数据
    json_data = request.json

    # 你可以进一步处理这些数据
    # ...

    return json({"message": "收到 POST 请求", "json_data": json_data})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

在这个例子中，我们定义了两个路由处理函数 `get_data` 和 `post_data`。它们分别用于处理 GET 和 POST 请求。在这些函数中，我们使用了 `request.args` 来获取 GET 请求的查询参数，使用了 `request.json` 来获取 POST 请求的 JSON 数据。这展示了 `request` 对象如何在不同情况下被使用。<br>

### `request.json` 用法示例:

```python
import os
from sanic import Sanic, response
from ocr_server import OCRClass
from fetch_pictures_aiohttp import download_image

app = Sanic("OCRService")

# 以全局变量方式实例化OCR对象
ocr_instance = OCRClass()

@app.post("/ocr_handler")
async def ocr_handler(request):
    """根据传入的image_url下载图片执行OCR,OCR执行完毕后,将结果返回同时写入mysql,之后删除下载的图片文件,避免空间占用。
    """
    # 异步获取请求数据
    request_data = await request.json   # 使用 `request_data = request.json` 也是可以的，但由于函数是异步函数，更推荐使用 `await`。
    """
    传入的数据格式如下:
    {
        "image_url": "https://xxxx.com/...png",  # 图片链接地址 必填参数 字符串类型
        "url_type": "oss"   # 图片地址类型 必填参数 字符串类型,取值为 "oss" 或 "7min_local"
    }
    """
    image_url = request_data.get("image_url")
    url_type = request_data.get("url_type")

    if image_url and url_type:
        # 根据图片链接下载图片到指定路径,具体路径可点击download_image跳转查看。
        # 下载会有网络波动、耗时问题,采用异步避免阻塞
        image_save_path = await download_image(image_url)
        if not image_save_path:
            return response.json({"error": "图片下载失败，请检查所输入图片链接是否有效。"})
        # 转换数据格式,格式必须类似 ['/OCR/2024-01-12/23c6b8f9-afe6-4eb8-b196-40235b0e89d5.jpg']，元素个数只能为1。
        path_images_list = [image_save_path]
        # 调用OCR处理方法
        ocr_result = ocr_instance.ocr_handler(path_images_list)
        # OCR执行结束后删除文件,避免空间占用
        os.remove(image_save_path)
        return response.json(ocr_result)
    else:
        # 提示用户 "image_url" 和 "url_type" 都为必填参数。
        return response.json({"error": "Invalid request, The 'image_url' and 'url_type' fields are both required parameters."}, status=400)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

`request.json` 的中的键值对可以使用`.get("key")`方法来获取，使用`.get("key")`方法来获取字典中的值有一个好处，那就是如果键不存在，它会返回`None`而不是抛出一个异常。这可以让你的代码更加健壮，特别是在处理外部传入的数据时。<br>


## response 对象：

`from sanic import response` 是 Sanic 框架提供的一个模块，它包含了一些用于构建 HTTP 响应的实用函数。这个模块使得返回不同类型的响应变得更加简单。其中，`response.json` 是其中一个非常常用的函数，它用于构建 JSON 格式的 HTTP 响应。<br>

以下是一些常见用法和示例：<br>

1. 返回 JSON 响应：

使用 `response.json` 函数，你可以将 Python 字典或对象转换为 JSON 格式的响应并返回给客户端。示例：<br>

```python
from sanic import Sanic
from sanic import response

app = Sanic(__name__)

@app.route("/json")
async def json_example(request):
    data = {"message": "Hello, Sanic!"}
    return response.json(data)
```

这将返回一个 JSON 响应，其内容为 `{"message": "Hello, Sanic!"}`。<br>

2. 返回文本响应：

使用 `response.text` 函数，你可以返回普通文本响应。示例：<br>

```python
@app.route("/text")
async def text_example(request):
    return response.text("This is a text response.")
```

这将返回一个包含指定文本的响应。<br>

3. 返回 HTML 响应：

使用 `response.html` 函数，你可以返回包含 HTML 内容的响应。示例：<br>

```python
@app.route("/html")
async def html_example(request):
    html_content = "<html><body><h1>Hello, Sanic!</h1></body></html>"
    return response.html(html_content)
```

这将返回一个包含指定 HTML 内容的响应。<br>

总之，`response` 模块提供了一些方便的函数，用于构建不同类型的 HTTP 响应，包括 JSON、文本、HTML 等。你可以根据你的需求使用这些函数来构建和返回适当类型的响应给客户端。<br>


## 高级功能:

### 基于类的视图(Class Based Views)：

#### 为什么使用它们(Why use them)？

#### 问题所在(The problem)：

设计API时的一个常见模式是在同一个端点上根据HTTP方法实现多种功能。<br>

尽管这两种选择都可行，但它们并不是好的设计实践，并且随着项目的增长，随着时间的推移可能难以维护。<br>

```python
@app.get("/foo")
async def foo_get(request):
    ...

@app.post("/foo")
async def foo_post(request):
    ...

@app.put("/foo")
async def foo_put(request):
    ...

@app.route("/bar", methods=["GET", "POST", "PATCH"])
async def bar(request):
    if request.method == "GET":
        ...

    elif request.method == "POST":
        ...

    elif request.method == "PATCH":
        ...
```

#### 解决方案(The solution)：

基于类的视图 (Class-based views) 实际上是实现对请求响应行为的类。它们提供了一种方式，可以在同一端点上 **对不同HTTP请求类型进行分门别类的处理** 。<br>

```python
from sanic.views import HTTPMethodView

class FooBar(HTTPMethodView):
    async def get(self, request):
        ...

    async def post(self, request):
        ...

    async def put(self, request):
        ...

app.add_route(FooBar.as_view(), "/foobar")
```


### Streaming:

#### Response streaming:

Sanic 允许你向客户端流式传输内容。<br>

```python
from sanic import Sanic
from sanic.response.types import BaseHTTPResponse   # response.send 中 send 方法出处；
from sanic.request import Request

app = Sanic("MyApp")

@app.route("/")
async def test(request: Request):
    response = await request.respond(content_type="text/csv")
    await response.send("foo,")
    await response.send("bar")

    # Optionally, you can explicitly end the stream by calling:
    await response.eof()


if __name__ == '__main__':
    app.run()
```

这在你想要将来自外部服务（如数据库）的内容流式 (stream content) 传输给客户端的情况下非常有用。<br>

例如，你可以使用 `asyncpg` 提供的异步游标(asynchronous cursor)将 **数据库记录** 流式传输给客户端。<br>

```python
@app.route("/")
async def index(request):
    response = await request.respond()
    conn = await asyncpg.connect(database='test')
    async with conn.transaction():
        async for record in conn.cursor('SELECT generate_series(0, 10)'):
            await response.send(record[0])
```

你可以通过调用 `await response.eof()` 来显式(explicitly)结束一个流。这是一个方便的方法(a convenience method)，用来替代 `await response.send("", True)`。

在处理程序确定没有更多内容需要发送回客户端后，应该调用它一次。<br>

🚨🚨🚨虽然在使用 Sanic 服务器时可以选择使用它，但如果你在 ASGI 模式下运行 Sanic，那么必须显式终止流。<br>

> 在 v21.6 版本中，调用 `eof` 变成了可选操作。


## 使用 Postman 动态更新Sanic服务中类属性：

工作中，你可能会遇到需要在程序启动后动态更新某些变量的情况，接下来我将给你具体的例子：<br>

### 在主文件中定义路由：

```python
# main.py
from sanic import Sanic
from sanic import response
from nlp_entry import create_pipelene
from code_utils import Dimension_analy

app = Sanic("my_app")

@app.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    processed_data = create_pipelene(text)
    return response.json(processed_data)

@app.route("/refresh")
async def refresh_metadata(request):
    Dimension_analy.modify_class_variable()
    return response.json({"message":"Dimension_analy的类属性更新成功"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

### 附属文件中定义用户输出的处理流程：

```python
# nlp_entry.py

from code_utils import Dimension_analy

def create_pipelene(usr_input):
    tmp_dimension = Dimension_analy()
    res_dim = tmp_dimension.handle(usr_input)
    return res_dim
```

### 工具型函数中定义文本处理、变量更新细节：

```python
# code_utils
class Dimension_analy:
    """进行维度分析
    """
    # 定义类属性
    dimension_data = 0

    def __init__(self):
        pass

    def handle(self, usr_input):
        """测试类属性是否变化，是否支持动态更新
        """
        res_dict = {"用户数据":usr_input,
                    "维度数据为:": self.dimension_data} # 调用类属性
        return res_dict

    @classmethod
    def modify_class_variable(cls):
        """每次调用更新一次类属性
        """
        cls.dimension_data += 1
```

### 运行方式：

1. 启动`main.py`文件；

2. Postman选择POST方法，url输入http://8.140.203.xxx:8848/ans；

3. "Body"的"x-www-form-urlencoded"选项中输入以下内容：

Key|Value|Description
---|---|---
usr_input | 黄金板块收益如何？ | Value中的内容可为任意字符串

4. 点击Send，你将获得如下JSON响应：

```json
{
    "用户数据": "黄金板块收益如何？",
    "维度数据为:": 0
}
```

由于没有更新"Dimension_analy"中"dimension_data"，所以此处显示的是原始值 "dimension_data = 0"。<br>

现在，我们尝试通过接口 "/refresh" 更新 "dimension_data" 测试下效果：<br>

5. Postman选择GET方法，url输入http://8.140.203.xxx:8848/refresh；(不需要输入其他参数)

6. 点击Send，你将获得如下JSON响应：

```json
{
    "message": "Dimension_analy的类属性更新成功"
}
```

7. 转回http://8.140.203.xxx:8848/ans接口界面，点击Send，你将获得如下JSON响应：

```json
{
    "用户数据": "黄金板块收益如何？",
    "维度数据为:": 1
}
```

数据动态更新了，在程序运行过程中更新了~~~~<br>

8. 我们再次运行http://8.140.203.xxx:8848/refresh接口，看下"/ans"接口的效果:

```json
{
    "用户数据": "黄金板块收益如何？",
    "维度数据为:": 2
}
```

数据再次更新，动态更新效果完成🪴🪴🪴<br>

🥴🥴🥴此时，直到你下次调用"/refresh"接口，程序的"Dimension_analy"中"dimension_data"数据将保持不变。<br>

## 使用Postman+global动态更新Sanic服务中的数据：

### 任务目标：

1. 确保程序正常启动；
2. 将数据存入 Redis 后，个人执行一次解析操作，获取对应数据；
3. 用户每次调用笔者的接口使用该数据，不必重新从 Redis 获取数据，直接从缓存中获取数据。

### 主文件：

```python
# main.py
from sanic import Sanic
from sanic import response
from code_utils import my_function
import redis
import pickle

app = Sanic("my_app")

# 连接到Redis
redis_conn = redis.Redis(host='localhost', port=6379)

# 全局变量用于存储metadata
metadata = None

@app.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if metadata is not None:
        processed_data = my_function(metadata)
        res_dict = {"用户数据":text,
                    "redis中数据": processed_data}
    else:
        res_dict = {"用户数据":text,
                    "redis中数据": metadata}
    return response.json(res_dict)

@app.route("/refresh")
async def refresh_metadata(request):
    if redis_conn.get('my_data') is not None:
        global metadata
        metadata = pickle.loads(redis_conn.get('my_data'))
        return response.json({"message": "Metadata 已刷新!"})
    else:
        return response.json({"message": "Metadata 还未写入redis，请先运行'/data_to_redis'接口。"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

### 工具文件：

```python
# code_utils.py
def my_function(data):
    for idx, item in enumerate(data,1):
        item["函数添加内容"] = f"内容{idx}"
    return data
```

### 数据存入redis的文件：

```python
# metadata_to_redis.py
import redis
import pickle

# 连接到Redis
redis_conn = redis.Redis(host='localhost', port=6379)

# 可改为其他数据，此处只是为了模拟数据写入。
data = [{'info_1':{'name': '麦克', 'age': 30, 'man': 'true'}}, 
        {'info_2':{'name': 'Juddy', 'age': 27, 'man': 'false'}}]

# 将数据存入Redis
redis_conn.set('my_data', pickle.dumps(data))
```

### 清空Redis数据(可选)：

如果你在代码测试过程中想要清空Redis，可以使用下列代码：<br>

```python
# empty_redis.py
import redis

# 连接到Redis
redis_conn = redis.Redis(host='localhost', port=6379)
# 清空redis
redis_conn.flushall()
```

### 文件运行顺序：

1. 运行`metadata_to_redis.py`文件；

2. 调用`/refresh`接口；

3. 用户此时就可向`ans`接口传参，获取数据；

### 代码解释：

该代码为用户提供两个接口："/ans"、"/refresh"，和一个数据写入Redis的文件。<br>

以下是每个接口/文件的作用：<br>

1. `metadata_to_redis.py`： 将数据存入Redis。

2. `/refresh`：从Redis中读取数据，并保存在全局变量`metadata`中。

3. `/ans`：返回包含用户提供数据和`metadata`中的数据的响应。在此接口中，它还调用`my_function`来处理`metadata`数据。


当运行`metadata_to_redis.py`后，数据被存入Redis。之后运行`/refresh`接口，数据从Redis中读取并存储到全局变量`metadata`中。此时，对于后续的`/ans`接口调用，程序直接从全局变量`metadata`中读取数据，而不是从Redis中。<br>

用户每次调用`ans`接口不会再有从Redis获取数据并pickle的时间，因为数据已经被存储在全局变量`metadata`中，接口直接从这个变量获取数据。<br>


## 启动Sanic服务时只加载一次模型--避免每次调用接口时加载模型的耗时:

### 模拟场景:

我使用sanic起了一个服务，`/classify`接口中调用了我自定义的类Classify_data中的predict方法，类Classify_data的`init`方法中加载了bert模型。我的代码应该怎么写才能避免每次调用`/classify`接口都要加载模型？<br>

### 解决方案-sanic应用的上下文:

为了避免每次调用`/classify`接口都要加载BERT模型，你可以在启动Sanic服务时就加载模型，并将模型保存在一个全局变量或者应用的上下文中。这样，每次调用接口时，只需要从上下文中获取已加载的模型即可。这种方法可以显著减少接口的响应时间，因为加载大型模型（如BERT）通常需要较长时间。<br>

以下是一个简化的例子，展示如何在Sanic应用中实现这个策略：<br>

```python
from sanic import Sanic
from sanic.response import json
from your_model_file import Classify_data  # 假设这是你的模型类

app = Sanic("ModelApp")

# 在启动应用时加载模型
@app.listener('before_server_start')
async def load_model(app, loop):
    app.ctx.classify_model = Classify_data()

# 使用已加载的模型进行预测
@app.route('/classify', methods=['POST'])
async def classify(request):
    # 获取请求数据
    data = request.json
    # 直接使用已加载的模型进行预测
    prediction = app.ctx.classify_model.predict(data)
    return json({"prediction": prediction})

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=8000)
```

在这个例子中，`Classify_data`的实例化（包括BERT模型的加载）发生在`before_server_start`事件处理器中。这意味着模型只会在Sanic服务启动时加载一次。之后，每次调用`/classify`接口时，通过`app.ctx.classify_model`即可访问到这个已加载的模型实例，而无需重复加载。<br>

请根据你的实际情况调整`your_model_file`和`Classify_data`的相关部分。这种方法适用于任何需要避免重复加载重量级资源（如模型文件、数据库连接等）的场景。<br>

### 解决方案-全局变量:

```python
from sanic import Sanic
from sanic.response import json
from your_model_file import Classify_data  # 假设这是你的模型类

app = Sanic("ModelApp")
# 实例化类Classify_data
classify_model = Classify_data()

# 使用已加载的模型进行预测
@app.route('/classify', methods=['POST'])
async def classify(request):
    # 获取请求数据
    data = request.json
    # 直接使用已加载的模型进行预测
    prediction = classify_model.predict(data)
    return json({"prediction": prediction})

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=8000)
```

在这个例子中，你在Sanic应用的全局作用域中创建了`Classify_data`的实例。这意味着，当你的Python脚本执行时（包括但不限于启动Sanic服务器时），`Classify_data`会被实例化一次，并且这个实例会被保存在`classify_model`变量中。随后，在每次调用`/classify`接口时，你都会使用这个已经加载并实例化的模型来进行预测，而不需要重新加载模型。<br>

这种方法同样有效地避免了每次请求都重新加载模型的问题，并且是实现这一目标的另一种简洁方式。选择在全局作用域中实例化模型（如你所示例的代码），还是在应用上下文中保存模型实例（如之前的例子所示），主要取决于你的偏好以及应用的具体需求。两种方法都可以达到相同的目标，即减少不必要的重复资源加载，优化服务的响应时间。<br>


### 应用上下文管理`app.ctx`写法和全局变量写法的区别:

应用上下文管理`app.ctx`写法和全局变量写法主要有两个区别，它们分别关涉到Sanic应用的上下文管理和模型实例化的时机。下面是这两种方法的对比：<br>

#### 应用上下文管理 (`app.ctx.classify_model = Classify_data()`):

1. **上下文作用域**：将模型保存在`app.ctx`（即应用的上下文）中，这意味着模型实例与Sanic应用的生命周期紧密相关。这种方法更加面向对象，利用了Sanic提供的应用上下文管理功能，有助于维护和组织代码，尤其是在更大、更复杂的应用中。
2. **模型加载时机**：模型是在`before_server_start`事件监听器中被显式加载的。这允许在服务器完全启动前执行初始化代码，如加载模型、建立数据库连接等。这样做可以确保在处理任何请求之前，所有的初始化工作都已完成。

#### 全局实例化 (`classify_model = Classify_data()`):

1. **全局变量**：将模型实例化为一个全局变量。这种方法简单直接，对于小型应用或者脚本来说非常方便和足够用。它直观地显示了模型是如何和何时被加载的，使得代码易于理解。
2. **模型加载时机**：模型在脚本执行期间（包括服务器启动前）就被加载了。这意味着，一旦开始执行脚本，无论服务器是否准备好接受请求，模型加载的工作就已经开始。这对于启动时间不敏感的应用是可行的。

#### 选择依据:

- **应用规模**：对于较大、结构复杂的应用，推荐使用应用上下文管理方式，因为它有助于更好地组织代码和管理资源。而对于简单的应用或脚本，全局实例化方式可能更直接和简便。
- **代码组织**：使用`app.ctx`可以让资源和配置与特定的Sanic应用实例关联，这在处理多个Sanic实例或在工厂函数中创建Sanic应用时特别有用。
- **初始化时机**：如果你的模型加载或其他初始化操作需要异步执行，或者需要在服务器完全启动前完成特定的准备工作，使用`before_server_start`监听器会更适合。

总的来说，两种方法都可以实现避免重复加载模型的目标，选择哪一种主要取决于你的具体需求和偏好。<br>


## Sanic Blueprint:

通常，我们创建的Sanic服务代码如下：<br>

```python
from sanic import Sanic
from sanic import response
import jieba

app = Sanic(__name__)

@app.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"用户数据分词结果为：": result})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

但在路由命名过程中，可能出现多个目录下命名重复的情况，例如：<br>

```log
.
├── 耐克
│   ├── 男装
│   └── 女装
└── 阿迪达斯
    └── 男装
    └── 女装
```

此时，我们为了区分"耐克"和"阿迪达斯"旗下的"男装"和"女装"，需要对命名空间进行隔离，我们应该怎么做呢？<br>

🫠🫠🫠正确答案是使用Sanic提供的蓝图(Blueprint)功能，**蓝图(Blueprint)中的 `url_prefix` 参数表示该蓝图的公共前缀**🐳🐳🐳，下面是如何使用 Sanic Blueprint 功能的一些关键概念和步骤：：<br>

1. 导入 Sanic 、response 和 Blueprint：

```python
from sanic import Sanic
from sanic import response
from sanic import Blueprint
import jieba    # 自己根据情况判断是否导入jieba
```

2. 创建一个 Sanic 应用程序和一个或多个 Blueprint：

如果你创建一个 Blueprint，可以参考下列写法：<br>

```python
app = Sanic(__name__)
# 定义蓝图
bp = Blueprint('my_blueprint', url_prefix='/my_blueprint')
```

如果你创建多个 Blueprint，可以参考下列写法：<br>

```python
app = Sanic(__name__)
# 定义蓝图
bp1 = Blueprint('my_blueprint1', url_prefix='/my_blueprint1')
bp2 = Blueprint('my_blueprint2', url_prefix='/my_blueprint2')  
```

3. 在对应的 Blueprint 中定义路由和视图函数：

> `async def index()`部分被称为视图函数。
> 将`@app.route`替换为`@bp.route`。

```python
@bp.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"用户数据分词结果为：": result})
```

4. 将 Blueprint 注册到应用程序中：

如果你创建一个 Blueprint，可以参考下列写法：<br>

```python
# 将蓝图注册到应用程序
app.blueprint(bp)
```

如果你创建多个 Blueprint，可以参考下列写法：<br>

```python
# 将蓝图注册到应用程序
app.blueprint(bp1)
app.blueprint(bp2)
```

5. 添加运行脚本：

```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

前面，我们讲过，**如果我们的应用程序不添加蓝图**，用户可以通过"http://8.140.203.xxx:8848/ans/"访问我们的服务。现在我们添加了蓝图，只需要在url的端口后添加url_prefix的部分即可，例如：<br>

```log
http://8.140.203.xxx:8848/my_blueprint/ans/
```

🤭🤭🤭不必担心url的部分重复，因为前缀不一样，所以对应的网址也不一样。<br>

```log
http://8.140.203.xxx:8848/my_blueprint1/ans/
http://8.140.203.xxx:8848/my_blueprint2/ans/
```


使用 Blueprint 的主要好处是，它允许你将应用程序拆分为多个模块，每个模块都有自己的路由和视图函数，这有助于提高代码的可维护性和可扩展性。你可以创建多个 Blueprint，并根据需要将它们注册到应用程序中，从而轻松地管理大型应用程序的路由和视图。<br>

### 完整代码--单个蓝图：

```python
from sanic import Sanic
from sanic import response
from sanic import Blueprint
import jieba    # 自己根据情况判断是否导入jieba

app = Sanic(__name__)
# 定义蓝图
bp = Blueprint('my_blueprint', url_prefix='/my_blueprint')

@bp.route("/ans", methods=["POST"])
async def answer(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"用户数据分词结果为：": result})

# 将蓝图注册到应用程序
app.blueprint(bp)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

### 完整代码--多个蓝图：

假设你创建了两个蓝图，你想让两个蓝图都调用"/ans"接口，可以参考以下代码示例：<br>

```python
from sanic import Sanic
from sanic import response
from sanic import Blueprint
import jieba    # 自己根据情况判断是否导入jieba

app = Sanic(__name__)
# 定义第一个蓝图
bp1 = Blueprint('my_blueprint1', url_prefix='/my_blueprint1')

@bp1.route("/ans", methods=["POST"])
async def answer1(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    result = jieba.lcut(text)
    return response.json({"answer1接口下，用户数据分词结果为：": result})

# 定义第二个蓝图
bp2 = Blueprint('my_blueprint2', url_prefix='/my_blueprint2')

@bp2.route("/ans", methods=["POST"])
async def answer2(request):
    # 获取用户数据
    text = request.form.get("usr_input")
    if not text:
        return response.json({"error": "Missing 'usr_input' parameter"}, status=400)

    # 在第二个蓝图中处理与"/ans"接口相同的逻辑
    result = jieba.lcut(text)
    return response.json({"answer2接口下，用户数据分词结果为：": result})

# 将两个蓝图都注册到应用程序
app.blueprint(bp1)
app.blueprint(bp2)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

现在，你的应用程序将同时支持两个蓝图的"/ans"接口，分别由`answer1`和`answer2`处理。这两个蓝图可以分别在不同的URL前缀下访问。<br>

🚨🚨🚨注意：虽然两个蓝图"/ans"接口的处理相同，但由于我们定义的是2个视图函数，不能都使用`async def answer(request)`写法，要进行区分。<br>


## 使用Sanic实现Server Sent Event(SSE):

使用sanic实现SSE传输，服务器所返回的响应内容需符合 SSE 格式规范：<br>

- 将消息内容放在 `data:` 之后；
- 在每条消息结尾使用 2 个换行符(`\n\n`)，即每条消息之间空一行；

下面以Sanic官方给出的代码示例进行示范，在同一目录下创建`index.html`、`server.py`文件，然后将下列代码分别粘贴到文件内。<br>

终端运行以下指令:<br>

```bash
python server.py
```

打开 `http://8.140.203.xxx:8848/` 即可看到效果。<br>

### `index.html`文件代码如下:

```html
<!DOCTYPE html>
<script>
    let eventSource
    let in_focus = true
    let should_run = false

    window.onblur = function () {
        in_focus = false
    }
    window.onfocus = function () {
        in_focus = true
        if (should_run) {
            start()
        }
    }

    function start() {
        should_run = true
        if (!window.EventSource) {
            // IE or an old browser
            alert("The browser doesn't support EventSource.")
            return
        }

        eventSource = new EventSource('/sse')

        eventSource.onopen = function (e) {
            log("Event: open")
        }

        eventSource.onerror = function (e) {
            log("Event: error")
            if (this.readyState == EventSource.CONNECTING) {
                log(`Reconnecting (readyState=${this.readyState})...`)
            } else {
                log("Error has occured.")
            }
        }

        eventSource.addEventListener('bye', function (e) {
            log("Event: bye, data: " + e.data)
            if (e.data == "close") {
                eventSource.close()
            }
        })

        eventSource.addEventListener('first_only', function (e) {
            log("Event: first_only, data: " + e.data)
        })

        eventSource.onmessage = function (e) {
            log("Event: message, data: " + e.data)
            if (!in_focus) {
                do_stop()
            }
        }
    }

    function stop(manual) {
        should_run = false
        do_stop()
    }

    function do_stop() {
        eventSource.close()
        log("eventSource.close()")
    }

    function log(msg) {
        console.log(msg)
        logElem.innerHTML += msg + "<br>"
        document.documentElement.scrollTop = 99999999
    }

    function reset() {
        document.querySelector("#logElem").innerHTML = ""
    }
</script>
<button onclick="start()">Start</button>
<button onclick="stop()">Stop</button>
<button onclick="reset()">Clear</button>
<pre id="logElem" style="margin: 6px 0"></pre>
```

### `server.py`文件代码如下:

```python
import asyncio
from itertools import count

from sanic import Sanic
from sanic.request import Request
from sanic.response import redirect

c = count()
app = Sanic("__BASE__")
asyncio.set_event_loop_policy(None)


@app.get("/sse")
async def sse(request: Request):
    print("Incoming SSE")
    headers = {"Cache-Control": "no-cache"}
    response = await request.respond(
        headers=headers, content_type="text/event-stream"
    )

    await response.send("event: first_only\n")
    while True:
        i = next(c)
        await response.send(f"data: {i}\n\n")
        await asyncio.sleep(1)

        if i and i % 4 == 0:
            await response.send("event: bye\n")
            await response.send("data: close\n\n")
            break

        await response.send("event: message\n")
    print("Done")


app.static("/index.html", "./index.html")


@app.route("/")
def index(request):
    return redirect("/index.html")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

### POST方式进行SSE传输:

```python
import asyncio
from sanic import Sanic
from sanic.request import Request

app = Sanic("MyApp")

@app.route("/ans", methods=["POST"])
async def test(request: Request):
    user_input = request.form.get("user_input")
    response = await request.respond(content_type="text/event-stream")
    await response.send(f"data: {user_input}\n\n")  # 测试post传输的值
    await asyncio.sleep(1)
    await response.send("data: 您\n\n")
    await asyncio.sleep(1)
    await response.send("data: 好，有\n\n")
    await asyncio.sleep(1)
    await response.send("data: 什么\n\n")
    await asyncio.sleep(1)
    await response.send("data: 可\n\n")
    await asyncio.sleep(1)
    await response.send("data: 以为您\n\n")
    await asyncio.sleep(1)
    await response.send("data: 效劳的\n\n")
    await asyncio.sleep(1)
    await response.send("data: 呢？\n\n")
    await asyncio.sleep(1)
    print("Done")   # 终端输出，不会返回给前端或postman

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

运行以上代码后，Postman测试效果为:<br>

![](./sanic_sse.gif)


### 结合LLM API的SSE示例:

创建一个`.env.local`文件，然后在其中写入自己的 "OPENAI_API_KEY" 即可使用下列代码。假设你创建了一个名为 `sanic_sse_example.py` 文件，将下列代码粘贴其中，终端运行下列指令即可使用 Postman 进行测试:<br>

```bash
python sanic_see_example.py
```

`sanic_see_example.py`:<br>

```python
import os
import json
from sanic import Sanic
from loguru import logger
from dotenv import load_dotenv
from openai import AsyncOpenAI

app = Sanic("my_app")

# 加载环境变量
dotenv_path = '.env.local'
load_dotenv(dotenv_path=dotenv_path)

# 设置日志
logger.remove()
logger.add("openai_stream.log", rotation="1 GB", backtrace=True, diagnose=True, format="{time} {level} {message}")


async def get_openai_response(response, chat_history):
    """
    Args:
        response:Sanic response object.
    """
    async with AsyncOpenAI(api_key=os.getenv("OPENAI_API_KEY")) as client:
        completion = await client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=chat_history,
            stream=True
        )
        async for chunk in completion:
            if chunk.choices[0].delta.content is not None:
                await response.send(f"data:{chunk.choices[0].delta.content}\n\n")   # 标准sse响应需要的格式，适用于postman测试

@app.route("/ans", methods=["POST"])
async def answer(request):
    # fetch user's input.
    user_input = request.form.get("user_input")
    # fetch user's historical chat data.
    chat_history = request.form.get("chat_history") # string,such as '[]'
    response = await request.respond(content_type="text/event-stream")
    try:
        if not user_input:
            logger.warning("用户输入为空")
            raise ValueError("用户输入为空")

        # 因为传入的是json数据，需要解码
        chat_history = json.loads(chat_history)
        logger.info(f"当前对话历史为:\n{chat_history}\n 数据类型为:{type(chat_history)}")

        user_history = []   # 存储用户聊天信息
        # 如果历史聊天数据不为空
        if chat_history:
            for user_message, bot_message in chat_history:
                user_history.append({"role": "user", "content": user_message})
                user_history.append({"role": "assistant", "content": bot_message})
        # 前一步构成的user_history是偶数，这里需要加上当前输入，构成奇数的content
        user_history.append({"role": "user", "content": user_input})

        # 调用聊天API
        await get_openai_response(response, user_history)

    except Exception as e:
        logger.error(f"处理聊天请求时发生错误：{e}")
        raise f"处理聊天请求时发生错误：{e}"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```


## 利用Chrome浏览器以非域名的形式解除限制访问自己的服务:

工作中你可能会遇到以下情况:<br>

本地/服务器启动了一个服务，以`http://ip:port`的方式访问网页，网址的标签显示 **"不安全"** ，点击 **"不安全"** 后发现 麦克风等功能均无法使用。<br>

解决方案:<br>

### 方案一: 为自己服务注册/绑定一个域名。

优点: 适用范围广。<br>

缺点: 操作步骤多，需要注册域名，需要做Nginx反向代理。<br>

### 方案二: 更改Chrome试验模式配置。

1. chrome浏览器网址栏输入:

```txt
chrome://flags/#unsafely-treat-insecure-origin-as-secure
```

2. 在打开的网页中搜索:

```txt
Insecure origins treated as secure
```

3. 在 "Insecure origins treated as secure" 选项的搜索框输入自己服务的网址，例如:

```txt
http://8.140.203.xxx:8082,http://8.140.203.xxx:11167
```

注意:<br>

- 多个网址之间使用英文逗号(`,`)间隔。
- "Insecure origins treated as secure"的状态需要为"Enabled"。

4. 点击右下角的"Relauch"，应用更改。

当前界面有修改时，下方栏会自动弹出"Relauch"选项。点击右下角的"Relauch"后，chrome会自动重新启动打开网页。<br>
