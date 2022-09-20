# 青年大学习/团课自动打卡 

![](https://github.com/838239178/tk-auto-study/workflows/auto-study/badge.svg) ![](https://img.shields.io/github/stars/838239178/tk-auto-study) ![](https://img.shields.io/github/forks/838239178/tk-auto-study) ![](https://img.shields.io/badge/Python-3.7-green.svg)

[此处展示最近更新日志，完整日志搓这里](./doc/Log.md)

> 2022.07.28: 新增微软Azure的计算机视觉OCR，仔细阅读帮助文档来使用这个新功能
>
> 2022.08.24: 新增配置 maxRetry 自定义最大重试次数
>
> 2022.08.31: 新增pytesseract OCR识别, 需要安装依赖，Linux推荐(1.3.3及以上可用),修复send.mode总是fail的问题
>
> 2022.09.12: 修复一个可能导致 connection timeout 的原因
>
> 2022.09.20: 优化执行流程和pytesseract参数

🤺妈妈再也不用担心我团课没看被团支书赶着催了

### 点个:star:再走吧,❤感谢大家的Star和Fork,有问题可以发issue

**仅供福建共青团团员学习交流使用** 项目遵循GPL协议，请勿拿来盈利、诈骗和违法之事！否则后果需要自负。

### 参与贡献！

🖊️ 如果你有新的或更好OCR识别方式 请参考 [OCR贡献文档](https://github.com/838239178/tk-auto-study/blob/1.2.5/doc/OCR_Module_Rule.md) 做出你的贡献！

🖊️ 如果你有新的或更好消息推送方式 请参考 [消息推送贡献文档](https://github.com/838239178/tk-auto-study/blob/1.2.5/doc/send_module_rule.md) 做出你的贡献！

## 使用方法

#### 🍎pub_key:

```
A7E74D2B6282AEB1C5EA3C28D25660A7
```

#### 申请Ocr识别接口的权限 （1.3.1版本以后可选本地OCR，如果不使用BaiduAPI则跳过该步骤）

[详细教程请点击这里](https://blog.pressed.top/2021/02/14/signUpBaiduOcr/)

*请选择使用一种可以识别文字的api，建议使用BaiduAI的Ocr接口*

- 首先要有一个百度账号，进入[这个网址](https://ai.baidu.com/)，点击控制台并登录
- 完成个人实名认证，申请文字识别的使用权
- 点击管理应用，点击创建应用，按要求填一些信息，创建完成后记住**API KEY**和**SECRET KEY**

#### 部署在平台上定时执行

这里介绍如何在GitHubActions中运行，因代理IP的相关代码，需确保**Python3.9**及以上版本。

- fork该项目到你的库中

- 添加三个secrets，分别为：username,  pwd,  pub_key

- `1.3.0`开始，需要添加验证码识别的OCR的 ocr_api_key，ocr_secret_key，ocr_type

- 将[该文件](./.github/workflows/run.yml)中的`#`删除并修改cron为你想要触发的时间，默认是每周三14点运行一次，cron如何写请自行百度

  ![image](https://user-images.githubusercontent.com/55338151/161259594-21812419-25e3-4b1f-b64f-e06b826351b8.png)

- 进入 Actions 中手动触发一次（点击auto-study 右边 Run workflow)，测试是否成功
  
  ![image](https://user-images.githubusercontent.com/55338151/161258385-eccd7f2f-8b7e-4002-aa8b-c436e96c01d7.png)

> `1.2.8` 版本以上可以使用Docker脚本运行，使用环境变量配置参数（格式同Actions)，可能无法配置多人打卡，可以尝试使用`\n`换行符（未测试）

### 如何在服务器上部署

在本地或者国内服务器上部署不需要代理，可兼容**Python3.7**版本

> requirements.full.txt 包含了项目中所有可能的python依赖 
> 
> requirements.txt 包含默认情况下需要的最少依赖

#### crontab

克隆项目并更改配置文件名称

```shell
git clone https://github.com/838239178/tk-auto-study.git && \
cd tk-auto-study && \
mv config.json.bak config.json
```

按照要求填写配置文件

```shell
vi config.json
```

修改 crontab

```shell
crontab -e
# 将下面这行复制到里面，cd的路径按照需要更改
00 08 * * 3 cd /root/tk-auto-study && python3 main.py >> crontab.log 2>&1
# 或者
00 08 * * 3 python3 /root/tk-auto-study/main.py >> crontab.log 2>&1
```

使用 `crontab -l` 查看是否修改成功

#### docker

除了crontab也可以使用目录下的`docker-compose.yml`，不需要填写 `config.json` 但需要修改文件内的`environments`

```shell
docker-compose up -d
```

## 可选识别类型

> `1.3.0` 及以上  

GithubAction（可选）添加新的secrets:ocr_type来指定识别类型

其他方式在config.json中修改指定配置项即可

| 可选值      | 使用方法                                                     | 支持版本 |
| ----------- | ------------------------------------------------------------ | -------- |
| baidu_image | [默认方法,需要到百度AI中申请](https://blog.pressed.top/2021/02/14/signUpBaiduOcr/) | 1.3.0    |
| tesseract   | [本地ocr识别,Windows推荐,Linux需要安装配置](./doc/ocr_help/tesseract.md) | 1.3.1    |
| azure       | [需要微软账号申请](./doc/ocr_help/azure.md)                  | 1.3.2    |
| pytesseract | [需要安装依赖，推荐linux下使用](./doc/ocr_help/pytesseract.md) | 1.3.3    |

## 可选消息推送

> 仅 `1.2.2` 版本及以上可用

使用消息推送 如微信推送、QQ推送

### 配置

GithubAction用户可通过添加secrets：send_type, send_key, send_mode 来使用消息推送

普通用户可查看最新的 `config.json.bak` 浏览新配置项

**配置项解读**

| 配置项    | 说明                                                         | 可选值                                                       |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| send_type | 消息推送类型 **不填写则不推送**                              | [server_chan](./doc/send_help/server_chan.md) [bark(Beta)](./doc/send_help/bark.md) [push_plus](./doc/send_help/push_plus.md) |
| send_key  | 消息推送服务的密钥                                           | 在推送服务的官网注册获得                                     |
| send_mode | 推送模式 打卡失败时推送(fail) 打卡成功时推送(success) 无论成功与否都推送(both) **默认失败时推送** | fail success both                                            |

## 多人打卡

> 仅支持 `1.2.3` 以上版本

配置多个账号一起打卡

1. 在 `Github Action` 上配置

    添加新secrets `EXT_USERS`, 按以下格式填写账号：
    
    ```text
   手机号1 密码1
   手机号2 密码2
   ```
   
   原先配置的secrets不需要改动，建议自己保存好多人的账号密码，以便以后增加或删除账号

2. 在本地 `config.json` 上配置

   参考 [config.json.bak](./config.json.bak) 的内容添加新的配置，原配置不需要改动

## Stargazers over time

[![Stargazers over time](https://starchart.cc/838239178/tk-auto-study.svg)](https://starchart.cc/838239178/tk-auto-study)

## 鸣谢

> [Pycharm](https://zh.wikipedia.org/wiki/PyCharm) 是一个在各个方面都最大程度地提高开发人员的生产力的 IDE，适用于 Python 语言。

特别感谢 [JetBrains](https://www.jetbrains.com/?from=mirai) 为开源项目提供免费的 [PyCharm](https://www.jetbrains.com/pycharm/?from=mirai) 等 IDE 的授权

[<img src="https://github.com/mamoe/mirai/raw/dev/.github/jetbrains-variant-3.png" width="200"/>](https://www.jetbrains.com/?from=mirai)


## 赏我一杯Coffee

![qq_pic_merged_1633171137809](https://cdn.jsdelivr.net/gh/838239178/PicgoBed/img/qq_pic_merged_1633171137809.jpg)

