# Quick Start  

大虚拟环境名称: cha  
python虚拟环境名称: venv  

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

取消设置代理指令：  
unset HTTP_PROXY HTTPS_PROXY ALL_PROXY  
unset http_proxy  
unset https_proxy  

修改完端口问题以外还要处理open ai apikey无法使用的问题（一直无法连接）  
使用中转api并修改代码：openai.py中的‘class OpenAILLM(BaseLLM):’，使其可以使用中转，这要就无需使用各个vpn节点来回切换了。具体方法可以去看下自己写的博客。  

参考一下方法：  
bash
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
python -m http.server 6006 #测试服务 浏览器可正常打开说明该端口正常  

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
