# Quick Start  

大虚拟环境名称: cha  
python虚拟环境名称: venv  
```
conda create -n cha python=3.10  
conda activate cha  
cd CHA  
python -m venv ./venv  
source venv/bin/activate  
(以其中的ppg_hrv分支为例，后续的一些指令还要看下面原本的quick start去适配main分支的)  
git clone -b ppg_hrv https://github.com/Institute4FutureHealth/CHA.git  
cd CHA  
pip install .  
pip install '.[all]'  

export OPENAI_API_KEY="api-key"  
6006port: https://u359324-98fe-51c16d33.bjb1.seetacloud.com:8443  
```
取消设置代理指令：
```
unset HTTP_PROXY HTTPS_PROXY ALL_PROXY  
unset http_proxy  
unset https_proxy  
```
修改完端口问题以外还要处理open ai apikey无法使用的问题（一直无法连接）  
使用中转api并修改代码：openai.py中的‘class OpenAILLM(BaseLLM):’，使其可以使用中转，这要就无需使用各个vpn节点来回切换了。具体方法可以去看下自己写的博客。  

参考一下方法：  
```
from openai import OpenAI

client = OpenAI(
    api_key = "本平台生成的key",
    base_url = "https://ai.nengyongai.cn/v1"
)

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "user", "content": "你好"}
    ],
    stream=True
)

for chunk in response:
    if chunk.choices:
        print(chunk.choices[0].delta.content, end="", flush=True)
```
使用下面的命令可以查看指定端口是否可以正常使用：  
```
python -m http.server 6006 #测试服务 浏览器可正常打开说明该端口正常
```

将autodl中的文件夹打包成zip格式才可以下载到本地：  
```
# autodl服务器终端输入以下命令 安装打包相关软件
apt-get update && apt-get install -y zip
# 将目标文件夹进行打包，这个将你的result文件夹打包成result.zip
zip -r result.zip result
# 如果有多个文件夹，可以合并打包，这里会将文件夹1,2,3全部打包在target文件中
zip -r target.zip 文件夹1 文件夹2 文件夹3...
```

===========
* [Documentation page](https://docs.opencha.com)
* [User Guide](https://docs.opencha.com/user_guide/index.html)
* [How to Contribute](https://docs.opencha.com/user_guide/contribute.html)
* [API Docs](https://docs.opencha.com/api/index.html)
* [Examples](https://docs.opencha.com/examples/index.html)

To use CHA in a safe and stable way, ensure you have Python 3.10 or higher installed. First, create a virtual environment:

```python
# Create the virtual environment
python -m venv /path/to/new/virtual/environment

# Activate the virtual environment
source /path/to/new/virtual/environment/bin/activate
```

Now, install the CHA package.
```bash
pip install openCHA
playwright install
这里每次直接安装playwright都会出现问题，需要安装额外的依赖：
playwright install-deps
```

Manual Installation
-------------------

```bash
git clone https://github.com/Institute4FutureHealth/CHA.git
cd CHA
pip install -e '.[all]'
playwright install
```

To simplify installation with minimum requirements and be ready to go, you can use the following command. This installs OpenAI, React Planner, as well as SerpAPI (search) and Playwright (browser) tasks:

```bash
pip install -e '.[minimum]'
```

If you want to install all requirements for all tasks and other components, use the following command:

```bash
pip install -e '.[all]'
```

Running openCHA
-------------------

After installing the package, based on what tasks you want to use, you may need to acquire some api_keys. For example, to get started using openAI GPT3.5 model as LLM in CHA, you need to signup
in their website and get the api_key. Then you should add openAI api_key as environment vairable in your terminal:

```bash
export OPENAI_API_KEY="your api_key"
```

The same goes for using tasks like SerpAPI:

```bash
export SERPAPI_API_KEY="your api_key"
```

Finally, you can start running our framework with the following simple code:

```python
from openCHA import openCHA

cha = openCHA()
cha.run_with_interface()
```

This code will run the default interface, and you can access it at the following URL:

**http://127.0.0.1:7860**

For more examples, visit the [Examples page](https://docs.opencha.com/examples/index.html).

![Alt Text](https://docs.opencha.com/_images/Interface.png)



​
openai Python库（openai）在内部处理了与API的通信，包括API的基础URL。

在使用 openai 库时，默认情况下，它会自动使用OpenAI的官方API URL，通常是 https://api.openai.com/v1/，无需开发者显式指定。

具体来说，openai 库的 OpenAI 类和相关API方法（例如 chat.completions.create）会自动将请求发送到正确的API端点，无需手动指定完整的URL。

如果你想自定义OpenAI的API URL（例如使用代理或其他API端点），openai 库提供了一个配置选项来更改API的基础URL。你可以通过设置 openai.api_base 来更改API的基础URL，如下所示：

import openai
openai.api_base = "https://your-custom-api-url.com/v1/"
修改从环境中加载至使用指定中转案例：

原来的代码：

    @model_validator(mode="before")
    def validate_environment(cls, values: Dict) -> Dict:
        """
            Validate that api key and python package exists in environment.

            This method is defined as a validation model for the class and checks the required environment values for using OpenAILLM.
            If the "openai_api_key" key exists in the input, its value is assigned to the "api_key" variable.
            Additionally, it checksthe existence of the openai library, and if it's not found, it raises an error.

        Args:
            cls (type): The class itself.
            values (Dict): The dictionary containing the values for validation.
        Return:
            Dict: The validated dictionary with updated values.
        Raise:
            ValueError: If the anthropic python package cannot be imported.

        """

        openai_api_key = get_from_dict_or_env(
            values, "openai_api_key", "OPENAI_API_KEY"
        )
        values["api_key"] = openai_api_key
        try:
            from openai import OpenAI

            values["llm_model"] = OpenAI()
        except ImportError:
            raise ValueError(
                "Could not import openai python package. "
                "Please install it with pip install openai."
            )
        return values
修改之后：

    @model_validator(mode="before")
    def validate_environment(cls, values: Dict) -> Dict:
        """
            Validate that api key and python package exists in environment.
            ...
        """
        # 修改开始: 直接将您的API Key硬编码到代码中
        # 移除了 get_from_dict_or_env 函数调用
        openai_api_key = "api-key"
        values["api_key"] = openai_api_key
        # 修改结束

        try:
            from openai import OpenAI
            
            values["llm_model"] = OpenAI(
                api_key=openai_api_key,
                base_url="https://your-url/v1"
            )

        except ImportError:
            raise ValueError(
                "Could not import openai python package. "
                "Please install it with `pip install openai`."
            )
        return values


​
