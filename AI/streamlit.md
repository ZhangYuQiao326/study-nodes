文心一言token `24.d6264080b269f704b8ddaab37a2a84a7.2592000.1715527533.282335-61091093`

Streamlit 是一个开源的 Python 库，它主要被设计用于快速创建和共享数据应用程序。其核心作用和特点可以概括为以下几点：

1. **快速开发和部署**：Streamlit 允许数据科学家和开发者使用 Python 快速创建具有前端界面的应用。这意味着传统上需要前端和后端开发知识的工作，现在可以仅使用Python完成，极大地简化了开发流程。

2. **交互性**：Streamlit 提供了丰富的组件（如按钮、滑块、选择框等），这些组件使得用户可以与数据和模型交互，实时看到更新的结果。这种交互性特别适合数据探索和模型参数调整。

3. **适用于数据分析和机器学习**：它特别适合用于数据分析、数据可视化和机器学习模型的原型开发。开发者可以使用Streamlit展示数据处理的过程、结果，以及模型的效果，使得非技术背景的利益相关者也能理解和参与项目。

4. **无需复杂的前端知识**：使用 Streamlit，开发者无需深入学习 HTML、CSS 或 JavaScript 等传统的前端开发技术，就可以创建美观实用的应用。Streamlit 应用的布局和样式已经优化得相当不错，同时也提供了一定的自定义空间。

5. **易于分享和部署**：Streamlit 应用可以很容易地被部署到各种平台，如 Streamlit Sharing、Heroku、AWS、Azure 等，这使得共享应用给其他用户变得非常简单。开发者只需要将应用部署到服务器，就可以通过链接让任何人访问。

6. **社区支持**：作为一个活跃的开源项目，Streamlit 拥有一个快速发展的社区，用户可以从中获取大量的支持、插件和示例，这些都极大地丰富了Streamlit的功能和使用场景。

综上所述，Streamlit 的主要作用是为数据科学家和开发者提供一个简单、高效、不依赖前端开发知识的平台，用以开发和部署数据驱动的应用。这使得从数据探索到交互式可视化、从机器学习模型演示到全面的分析报告，所有这些工作都可以在一个统一的框架下快速实现。

## 接口

| Streamlit函数        | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| `st.write()`         | 用于在应用中显示文本、数字、数据帧、图像、超链接等。         |
| `st.dataframe()`     | 专门用于显示Pandas DataFrame。                               |
| `st.button()`        | 创建一个按钮，当被点击时返回True。                           |
| `st.text_input()`    | 创建一个文本输入框，返回用户输入的字符串。                   |
| `st.number_input()`  | 创建一个数字输入框，允许用户输入一个数字。                   |
| `st.slider()`        | 创建一个滑块，用户可以选择一个范围内的值。                   |
| `st.selectbox()`     | 创建一个下拉选择框，用户可以从多个选项中选择一个。           |
| `st.checkbox()`      | 创建一个复选框，当被选中时返回True。                         |
| `st.radio()`         | 创建一组单选按钮，用户可以从中选择一个选项，执行对应操作     |
| `st.file_uploader()` | 创建一个文件上传控件，允许用户上传文件，返回一个文件对象。   |
| `st.image()`         | 用于在页面上显示图像。                                       |
| `st.sidebar()`       | 用于添加一个侧边栏，侧边栏可以包含任何Streamlit组件，如输入框、选择器等。 |
| `st.map()`           | 用于显示地图，通常与地理数据一起使用。                       |
| `st.pyplot()`        | 用于显示matplotlib生成的图表。                               |
| `st.metric()`        | 用于显示关键指标的单行，可以包括指标名称、值和可选的增减情况。 |
| `st.expander()`      | 创建一个可以展开和折叠的区域，用于组织内容，使页面更加整洁。 |
| `st.columns()`       | 创建多列布局，允许将组件水平排列。                           |
| `st.success()`       | 显示一条成功消息。                                           |
| `st.error()`         | 显示一条错误消息。                                           |
| `st.warning()`       | 显示一条警告消息。                                           |
| `st.info()`          | 显示一条信息性消息。                                         |
| `st.empty()`         | 创建一个空占位符，可以稍后在代码中插入元素。                 |

这些函数是Streamlit的核心，通过它们可以构建丰富多样的数据应用界面。使用这些接口可以快速开发出具有交互性的应用，非常适合进行数据分析和数据可视化项目的原型设计。

```cpp
{'Access-Control-Allow-Headers': 'Content-Type', 'Access-Control-Allow-Origin': '*', 'Appid': '61091093', 'Connection': 'keep-alive', 'Content-Encoding': 'gzip', 'Content-Type': 'application/json; charset=utf-8', 'Date': 'Fri, 12 Apr 2024 15:54:54 GMT', 'P3p': 'CP=" OTI DSP COR IVA OUR IND COM "', 'Server': 'Apache', 'Set-Cookie': 'BAIDUID=FE32AA2DA27784FC4542E4FF8E6059B2:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2145916555; path=/; domain=.baidu.com; version=1', 'Statement': 'AI-generated', 'Vary': 'Accept-Encoding', 'X-Aipe-Self-Def': 'eb_total_tokens:9,prompt_tokens:1,id:as-1j42wp3555', 'X-Baidu-Request-Id': 'c74a5c8f97f3e10cdf74c130902b16541000155', 'X-Openapi-Server-Timestamp': '1712937293', 'X-Ratelimit-Limit-Requests': '300', 'X-Ratelimit-Limit-Tokens': '300000', 'X-Ratelimit-Remaining-Requests': '299', 'X-Ratelimit-Remaining-Tokens': '299999', 'Content-Length': '217'}
{"id":"as-1j42wp3555","object":"chat.completion","created":1712937294,"result":"你好，有什么我可以帮助你的吗？","is_truncated":false,"need_clear_history":false,"usage":{"prompt_tokens":1,"completion_tokens":8,"total_tokens":9}}
```

## 文件操作

当你使用 `st.file_uploader` 在 Streamlit 中上传文件，`uploaded_file` 对象提供了几个方法来处理文件数据。以下是 `uploaded_file` 对象的常见属性和方法，以表格形式展示：

| 方法或属性   | 描述                                                     |
| ------------ | -------------------------------------------------------- |
| `name`       | 文件的名称，如 `example.csv`                             |
| `size`       | 文件的大小，以字节为单位                                 |
| `type`       | 文件的类型，如 `text/csv`                                |
| `read()`     | 读取文件内容，返回字节数据                               |
| `getvalue()` | 如果文件是被缓存的，在内存中，这个方法返回文件的全部内容 |
| `seek(0)`    | 将文件读取指针移动到文件的开头                           |
| `close()`    | 关闭文件对象                                             |

这些方法允许你在上传后对文件进行各种操作，例如读取内容、重置读取位置等。这里是如何使用这些方法的一些基本示例：

```python
import streamlit as st

uploaded_file = st.file_uploader("Upload a file")
if uploaded_file is not None:
    # 读取文件名称和类型
    file_name = uploaded_file.name
    file_type = uploaded_file.type

    # 读取并显示文件内容（假设是文本文件）
    content = uploaded_file.read().decode('utf-8')
    st.write(content)

    # 重置读取指针
    uploaded_file.seek(0)
    
    # 再次读取或其他操作
    # ...
    
    # 关闭文件
    uploaded_file.close()
```

这些基本操作使得处理上传的文件变得简单，无论是用于显示、进一步分析还是保存到服务器。







我们可以将提供的HTTP响应头和JSON响应体整理成两个不同的表格：一个用于HTTP响应头，另一个用于JSON响应体的内容。

### 表格1：HTTP响应头

| 字段                           | 值                                                           |
| ------------------------------ | ------------------------------------------------------------ |
| Access-Control-Allow-Headers   | Content-Type                                                 |
| Access-Control-Allow-Origin    | *                                                            |
| Appid                          | 61091093                                                     |
| Connection                     | keep-alive                                                   |
| Content-Encoding               | gzip                                                         |
| Content-Type                   | application/json; charset=utf-8                              |
| Date                           | Fri, 12 Apr 2024 15:54:54 GMT                                |
| P3p                            | CP=" OTI DSP COR IVA OUR IND COM "                           |
| Server                         | Apache                                                       |
| Set-Cookie                     | BAIDUID=FE32AA2DA27784FC4542E4FF8E6059B2:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2145916555; path=/; domain=.baidu.com; version=1 |
| Statement                      | AI-generated                                                 |
| Vary                           | Accept-Encoding                                              |
| X-Aipe-Self-Def                | eb_total_tokens:9,prompt_tokens:1,id:as-1j42wp3555           |
| X-Baidu-Request-Id             | c74a5c8f97f3e10cdf74c130902b16541000155                      |
| X-Openapi-Server-Timestamp     | 1712937293                                                   |
| X-Ratelimit-Limit-Requests     | 300                                                          |
| X-Ratelimit-Limit-Tokens       | 300000                                                       |
| X-Ratelimit-Remaining-Requests | 299                                                          |
| X-Ratelimit-Remaining-Tokens   | 299999                                                       |
| Content-Length                 | 217                                                          |

### 表格2：JSON响应体

| 字段               | 值                             |
| ------------------ | ------------------------------ |
| id                 | as-1j42wp3555                  |
| object             | chat.completion                |
| created            | 1712937294                     |
| result             | 你好，有什么我可以帮助你的吗？ |
| is_truncated       | false                          |
| need_clear_history | false                          |
| prompt_tokens      | 1                              |
| completion_tokens  | 8                              |
| total_tokens       | 9                              |

这两个表格提供了对HTTP响应头和响应体内容的结构化视图，方便查看和理解各个字段的值。





```
[“相比于纯理论体系的学习，在研究生学习中我更喜欢的是与实践项目相辅相成的学习。”, “当从贵校官网上看到有关您的研究方向时，新颖的课题就让我眼前一亮，地质灾害防灾减灾与计算机交叉学科，是旧学科与新学科的碰撞和融合。”, “相信在新一代大数据技术的支持下，这个研究方向会有突破性的发展。”]
```



后期想法

1. 本地，添加一个侮辱词库，现在本地搜索，没有的话，再基于大模型搜索
2. 



```json
{
    "你是个蠢货。": true,
    "你是个傻逼。": true,
    "你真漂亮。": false,
    "今天的天气真好。": false,
    "这个衣服是20元。": false,
    "太贵了。": false,
    "倒大霉认识你这个丧气货。": false,
    "周末我们去哪里玩呢？": false,
    "今天星期一，我去买衣服。": false,
    "衣服的价钱一块一毛一。": false,
    "你神经病啊你！": false,
    "奶茶倒在我身上了。": false
}
```

