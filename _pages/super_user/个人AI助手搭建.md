---
date: 2024.08.28
---
> 在AI时代，自己搭建一个AI助手似乎才能跟上时代的潮流。现代 AI 助手的核心是**大语言模型**，以大语言模型为基座，附加上各种**图片生成、网页搜索和文件解析**等插件功能就可以实现一个个人的AI助手啦。


# 大语言模型

随着大语言模型开源生态日益发展，使用*ollama*本地运行大语言模型是一件很轻松的事情，在 Arch Linux 下运行如下命令：
```bash
sudo pacman -Syu ollama
ollama serve > /dev/null 2>&1 &
```

你就获得了类似于OpenAI的GPT接口的大语言模型服务。虽然性能要逊色不少，但是免费且隐私，这也就是我们想要个人助手所需要做出的取舍。

接下来在[ollama仓库](https://ollama.com/library)选取一些你想要的模型即可。
```bash
ollama pull llama3.1 # 默认8b，4.7G的大小，是效果与速度的平衡点
ollama pull phi:mini # 默认3.8b的小模型
```
# Open WebUI

有了基座模型之后，我们需要在此基础上扩充它的能力，这时候就需要搬出扩展性极强的忍义手（*只狼玩家探头*）——*Open WebUI*了。Open WebUI除了提供大语言模型的网页UI外，还为支持集成各种各样的插件，这同样是后续各种功能的基础。

[Open WebUI 官方](https://docs.openwebui.com/)推荐采用 docker 部署：
```bash
sudo docker run -d --network=host \
	-v open-webui:/app/backend/data \
	-e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
	-e AUTOMATIC1111_BASE_URL=http://127.0.0.1:7860/ \
	-e ENABLE_IMAGE_GENERATION=True \
	--name open-webui \
	--restart always ghcr.io/open-webui/open-webui:main
```

此处让容器共享宿主机的网络以使用本地启动的 ollama（以及 stable diffusion）服务，并制定ollama和stable diffusion的服务地址。

以下内容均由 Open WebUI提供支持
---
## 网络搜索


搜索引擎巨头 Google 提供了免费的搜索 API 给个人的搜索引擎使用，并且考虑到Google优秀的搜索能力，我直接调用Google的API以实现AI助手的搜索能力。

> Tips: 墙内的同志们想要使用Google的搜索引擎API需要在python中配置代理，编辑`apps/rag/search/google_xxx.py` 在 `requests` 模块的方法调用中增加 `proxies`参数，如
> `proxies ={ 	'http': 'http://localhost:7890', }`


## 图片生成

伴随着开源图片生成模型 *stable diffusion* 而来的 *stable diffusion webui* 使得部署图片生成服务变成一件很轻松的事情。根据运行的平台选择对应的安装方式即可，[GitHub上有详尽的说明](https://github.com/AUTOMATIC1111/stable-diffusion-webui?tab=readme-ov-file)。

同样做一些提醒吧：
1. 推荐使用 `python3.10` 作为运行环境安装，如果使用更新（或更老）的python版本会出现一些依赖库的编译错误问题；
2. 不要使用 `pyenv` 管理python版本，这可能有点暴论的意思，因为`pyenv`通过设置环境变量使用不同的python版本，但是这会干扰很多系统级需要调用python的程序安装他们需要的依赖，使得 Arch Linux 上的 `pacman` 包管理失效。



