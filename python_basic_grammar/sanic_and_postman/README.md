# Sanic and Postman

Sanic 是一个用于构建异步（asynchronous）Web应用的Python框架，它基于 Python 3.5+ 引入的 `async/await` 语法，旨在提供高性能的 Web 服务器和路由处理功能。它的设计灵感来自于 Flask，但强调了异步处理，适用于高并发的应用。<br>

- [Sanic and Postman](#sanic-and-postman)
  - [Sanic的安装](#sanic的安装)
  - [简单sanic应用示例：](#简单sanic应用示例)
  - [使用postman测试sanic启动的服务：](#使用postman测试sanic启动的服务)
  - [GET和POST方法：](#get和post方法)
    - [GET 方法进阶：](#get-方法进阶)
    - [POST 方法：](#post-方法)
    - [POST 方法路由的测试：](#post-方法路由的测试)
    - [Postman 中 Body 选项的含义与选择：](#postman-中-body-选项的含义与选择)
  - [request 对象：](#request-对象)
  - [response 对象：](#response-对象)
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
  - [Sanic使用公共前缀(Blueprint):](#sanic使用公共前缀blueprint)

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

2. 添加路由和处理函数:

在你的应用中，你可以定义不同的路由，并为每个路由定义处理函数。例如：<br>

```python
@app.route("/")
async def index(request):
    return response.text("Hello, Sanic!")

@app.route("/user/<name>")
async def greet_user(request, name):
    return response.text(f"Hello, {name}!")
```

这里，`@app.route` 装饰器用于指定路由，接受 HTTP 请求，并调用相应的处理函数。<br>

3. 启动 Sanic 应用:

在应用的最后，添加以下代码以运行 Sanic 服务器：<br>

```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

4. 运行应用:

使用以下命令运行你的应用：<br>

```bash
python sanic_server.py
```

现在，你的 Sanic 应用应该已经在本地运行，并监听在端口 8000 上。<br>

这只是 Sanic 的基本用法，它还提供了许多其他功能，如中间件支持、异步请求处理、WebSocket 支持等等，以满足不同类型的 Web 应用需求。你可以查阅官方文档以深入了解更多信息：https://sanicframework.org/<br>

## 使用postman测试sanic启动的服务：

Postman 是一种流行的应用程序，用于测试和调试网络 API（应用程序编程接口）。它提供了一个用户友好的界面，允许开发人员轻松地创建、发送和接收 HTTP 请求，以测试 API 的各种端点和功能。Postman 还允许你组织请求、保存和分享测试集合，以及自动化 API 测试工作流程。它可用于多种用途，包括单元测试、端到端测试、性能测试和文档编制等。<br>

工作中经常使用 Postman 测试 Sanic 用于构建 Web 应用程序和 API。使用 Postman 进行单个测试，或者使用 Postman 的自动化功能来创建 API 文档和测试集合。这有助于确保 API 的质量和性能。<br>

Postman 主界面介绍如下：<br>

![Postman主界面](./postman主界面标识.png)

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
async def test(request):
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

**request**包含了客户端（浏览器）发过来的HTTP请求的各类数据。request不需要显示导入，Sanic内部含有。request 包含以下属性：<br>

| 属性   | 使用方式        | 意义                                        |
|--------|-----------------|---------------------------------------------|
| json   | request.json | 当客户端POST来的数据是json格式时，访问json数据 |
| args   | request.args | 查询字符串变量，即URL中问号?后面的部分        |
| files  | 字典            | 拥有name、body和type的文件对象的字典           |
| form   | 表单            | 以POST方式传递的form变量                     |
| body   | 字节串          | POST的原始数据                               |  


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
async def test(request):
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

## Sanic使用公共前缀(Blueprint):

通常，我们创建的Sanic服务代码如下：<br>

```python
from sanic import Sanic

app = Sanic("HanLP-API")

@app.route("/segment", methods=["POST"])
async def function(request):
    pass

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
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

正确答案是使用Sanic提供的蓝图(Blueprint)功能，下面是如何使用 Sanic Blueprint 功能的一些关键概念和步骤：：<br>

1. 导入 Sanic 和 Blueprint：

```python
from sanic import Sanic
from sanic import Blueprint
```

2. 创建一个 Sanic 应用程序和一个或多个 Blueprint：

```python
app = Sanic(__name__)
bp = Blueprint('my_blueprint', url_prefix='/my_blueprint')
```

3. 在 Blueprint 中定义路由和视图函数：

> `async def index()`部分被称为视图函数。

```python
@bp.route('/')
async def index(request):
    return sanic.response.text('This is the index page of my_blueprint.')

@bp.route('/about')
async def about(request):
    return sanic.response.text('This is the about page of my_blueprint.')
```

4. 将 Blueprint 注册到应用程序中：

```python
app.blueprint(bp)
```

5. 添加运行脚本：

```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8848)
```

使用 Blueprint 的主要好处是，它允许你将应用程序拆分为多个模块，每个模块都有自己的路由和视图函数，这有助于提高代码的可维护性和可扩展性。你可以创建多个 Blueprint，并根据需要将它们注册到应用程序中，从而轻松地管理大型应用程序的路由和视图。

总之，Sanic 的 Blueprint 功能是一种组织和管理路由、中间件和视图的强大工具，使得构建异步 web 应用程序更加灵活和可维护。

Sanic通过使用蓝图(Blueprint)通过**公共前缀**对路由进行命名空间隔离:<br>

bp1 = Blueprint('blueprint1', url_prefix='/bp1')    &nbsp;&nbsp;# url_prefix 表示该蓝图的公共前缀；<br>
bp2 = Blueprint('blueprint2', url_prefix='/bp2')  

即支持下面这种写法：  
@bp1.route("/segment", methods=["POST"])  
@bp2.route("/segment", methods=["POST"])  
  
不必担心url的部分重复，因为前缀不一样，所以对应的网址也不一样。<br>
```python
http://localhost:8000/bp1/segment
http://localhost:8000/bp2/segment  
```