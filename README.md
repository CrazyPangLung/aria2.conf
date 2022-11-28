# Aria2 完美配置

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=License)](https://github.com/P3TERX/aria2.conf/blob/master/LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/P3TERX/aria2.conf.svg?style=flat-square&label=Stars&logo=github)](https://github.com/P3TERX/aria2.conf/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/P3TERX/aria2.conf.svg?style=flat-square&label=Forks&logo=github)](https://github.com/P3TERX/aria2.conf/fork)

一套 Aria2 配置方案，包含了配置文件、附加功能脚本等文件，用于实现 Aria2 功能的增强和扩展，提升 Aria2 的下载速度与使用体验。

## 功能特性

* BT 下载率高、速度快
* 重启后不丢失任务进度、不重复下载
* 删除正在下载的任务自动删除未完成的文件
* 下载错误自动删除未完成的文件
* 下载完成自动删除控制文件(`.aria2`后缀名文件)
* 下载完成自动删除种子文件(`.torrent`后缀名文件)
* 下载完成自动删除空目录
* 下载完成自动移动文件到指定目录
* 下载完成自动上传到 Google Drive 和 OneDrive 等网盘(RCLONE 联动功能)
* BT 下载完成自动清除垃圾文件(文件类型过滤功能)
* BT 下载完成自动清除小文件(文件大小过滤功能)
* 一键自动更新 BT tracker，进一步加速 BT 下载
* 有一定的防版权投诉、防迅雷吸血效果
* 更好的 PT 下载支持

## 部署方法

**推荐使用以下项目部署以获得最佳使用体验**

- [Aria2 Pro](https://github.com/P3TERX/docker-aria2-pro) (Docker)
- [Aria2 一键安装管理脚本 增强版](https://github.com/P3TERX/aria2.sh) (GNU/Linux)

* 安装和配置 Rclone
RCLONE 官方提供了一键安装脚本：
```
wget -N git.io/aria2.sh && chmod +x aria2.sh
```
安装完后，输入 rclone config 命令进入交互式配置选项，按照提示一步一步来进行操作即可。如果你一脸懵逼，可以去看《Rclone 安装配置教程》来了解配置的详细过程。

* 配置自动上传脚本
Aria2 一键安装管理脚本 增强版 整合了 Aria2 完美配置 ，安装后会附带一些附加功能脚本功能脚本，RCLONE 自动上传脚本就是其中之一。由于默认不启用，所以需要手动启用。
> **NOTICE:** 本项目的上传脚本使用更稳定快速的原生命令上传方式，而非处在测试阶段的挂载方式，这点和一般的脚本不同。
  输入`nano /root/.aria2c/aria2.conf`打开 Aria2 配置文件进行修改。或使用Aria2 一键安装管理脚本 增强版中的手动修改选项打开配置文件进行修改。找到“下载完成后执行的命令”，把`clean.sh`替换为`upload.sh`。
```
# 下载完成后执行的命令
on-download-complete=/root/.aria2c/upload.sh
```
* 输入nano /root/.aria2c/script.conf打开附加功能脚本配置文件进行修改，有中文注释，按照自己的实际情况进行修改，第一次使用只建议修改网盘名称。
```
# 网盘名称(RCLONE 配置时填写的 name)
drive-name=OneDrive
```
* 重启 Aria2 。脚本选项重启或者执行以下命令：
```
service aria2 restart
```
* 检查配置是否成功
* 执行upload.sh脚本，提示success即代上传脚本能正常被调用，否则请检查与 RCLONE 有关的配置。
```
/root/.aria2c/upload.sh
```
* 打开实时日志并下载任意文件，出现上传成功信息即代表配置成功，否则请认真阅读教程并重新开始。
* 检查网盘是否存在相关文件，若不存在说明你搞错网盘了。


## 进阶玩法

* [OneDrive、Google Drive 等网盘离线下载](https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html)
* [百度网盘转存到 OneDrive 、Google Drive 等其他网盘](https://p3terx.com/archives/baidunetdisk-transfer-to-onedrive-and-google-drive.html)

## 文件说明

> **TIPS:** 附加功能脚本需配合配置文件使用，仅适用于 GNU/Linux ，理论上 macOS 安装 GNU 工具套件可以使用。依赖：`bash`、`curl`、`findutils`、`jq`

| 文件                    | 说明                                                                                                                                                                                                                                                                                      |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aria2.conf`            | Aria2 配置文件。建议配合 1.35.0 及以上版本使用。内附可能比官方文档还详细的中文注释说明和修改建议，在不了解的情况下随意修改可能导致本方案的特性失效。                                                                                                                                      |
| `script.conf`           | Aria2 附加功能脚本配置文件。                                                                                                                                                                                                                                                              |
| `core`                  | Aria2 附加功能脚本核心文件。所有脚本都依赖于此文件运行。                                                                                                                                                                                                                                  |
| `clean.sh`              | 文件清理脚本。在下载完成后执行([on-download-complete](https://aria2.github.io/manual/en/html/aria2c.html#cmdoption-on-download-complete))，自动清理控制文件(`*.aria2`)、种子文件(`*.torrent`)和空目录。（默认启用）                                                                       |
| `delete.sh`             | 文件删除脚本。在下载停止后执行([on-download-stop](https://aria2.github.io/manual/en/html/aria2c.html#cmdoption-on-download-stop))，当下载错误或删除正在下载的任务后自动删除相关文件，并自动清理控制文件(`*.aria2`)、种子文件(`*.torrent`)和空目录，防止不必要的磁盘空间占用。（默认启用） |
| `upload.sh`             | 文件上传脚本。在下载完成后执行([on-download-complete](https://aria2.github.io/manual/en/html/aria2c.html#cmdoption-on-download-complete))，自动调用 RCLONE 上传(move)下载完成的文件到网盘，并自动清理控制文件(`*.aria2`)、种子文件(`*.torrent`)和空目录。（默认不启用）                   |
| `rclone.env`            | [RCLONE 环境变量](https://rclone.org/docs/#environment-variables)文件。配合`upload.sh`使用，用于配置 RCLONE 的选项参数。                                                                                                                                                                  |
| `move.sh`               | 文件移动脚本。在下载完成后执行([on-download-complete](https://aria2.github.io/manual/en/html/aria2c.html#cmdoption-on-download-complete))，自动将下载完成的文件移动到指定目录，并自动清理控制文件(`*.aria2`)、种子文件(`*.torrent`)和空目录。（默认不启用）                               |
| `tracker.sh`            | BT tracker 列表更新脚本。在 Aria2 配置文件(`aria2.conf`)所在目录执行即可获取[最新 tracker 列表](https://raw.githubusercontent.com/XIU2/TrackersListCollection/master/all.txt)并添加到配置文件中。其它使用方法详见 [tracker.md](./tracker.md)                                              |
| `dht.dat`<br>`dht6.dat` | DHT 网络节点数据文件。提升 BT 下载率和下载速度的关键之一。相关科普：《[解决 Aria2 无法下载磁力链接、BT种子和速度慢的问题](https://p3terx.com/archives/solved-aria2-cant-download-magnetic-link-bt-seed-and-slow-speed.html)》                                                             |

## 遇到问题如何处理

遇到问题先看 [FAQ](https://p3terx.com/archives/aria2_perfect_config-faq.html) 再提问，你还可以加入 [Aria2 TG 群](https://t.me/Aria2c)和小伙伴们一起讨论。要注意提问的方式和提供有用的信息，提问前建议去学习《[提问的智慧](https://github.com/ryanhanwu/How-To-Ask-Questions-The-Smart-Way/blob/master/README-zh_CN.md)》，这能更好的帮助你去解决问题和节约时间。诸如 “为什么不能使用？”、“那你能帮帮我吗？” 之类的问题应该没有人会知道。

## 更新日志

更新推送：[Aria2 Channel](https://t.me/Aria2_Channel)

### 2021-07-04

- 新增 正则表达式文件过滤功能。感谢 @hereisderek
- 新增 支持自定义多个 tracker 列表 URL ([Aria2 Pro](https://github.com/P3TERX/Aria2-Pro-Docker))。感谢 @hereisderek
- 修复 目录名有特殊符号时导致的路径处理错误。感谢 @zzjjbb

### 2020-12-24

- 调整一些过于激进的配置选项值，提升稳定性。
  - 单个 BT 下载任务最大连接数(`bt-max-peers`)从`0`(不限制)调整为`128`，减少不必要的 CPU 和网络占用。
  - 下载进度自动保存间隔时间(`auto-save-interval`)从`1`秒调整为`20`秒，减少磁盘 I/O 。
- 补充一些说明和设置建议。

### 2020-12-09

- 优化 RCLONE 环境变量文件加载方法
- 更换 User Agent（因部分用户的滥用行为导致被某知名开源镜像站屏蔽）
- 增加一些高级选项及说明

### 2020-07-12 | V3

- 全新 附加功能脚本
- 新增 种子文件删除功能
- 新增 全局文件过滤功能
- 新增 前端自定义临时下载目录功能。免手动设定脚本中的路径。
- 新增 附加功能脚本核心文件(`core`)。强大的功能源自于强大的核心。
- 新增 附加功能脚本配置文件(`script.conf`)。独立文件持久化配置，方便更新。
- 新增 RCLONE 环境变量文件(`rclone.env`)
- 优化 文件删除机制。防止极限环境和极端使用习惯情况下误删文件。
- 更多改进请自行体验

<details>
<summary>历史记录</summary>

早期版本和其它记录已归档至 [v2 分支](https://github.com/P3TERX/aria2.conf/tree/v2)

</details>

## 声明

本项目使用 [MIT](https://github.com/P3TERX/aria2.conf/blob/master/LICENSE) 开源协议，对于本项复制、修改、发布等行为请遵守相关协议保留所有文件中的版权信息，谢谢合作！
