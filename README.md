## 2022年8月更新

目前网易云已屏蔽海外IP，建议使用其他歌词源。
有另一个使用QQ API的插件，推荐使用：https://github.com/LightAPIs/qq_music_aum

# Synology Lrc Plugin

群晖 Audio Station/DS Audio 的歌词插件。

用户向的手把手使用教程可参考：[群晖NAS进阶使用教程（一）：Audio Station网易歌词插件](http://tsuinte.ru/2017/synology_audio_station_lyrics_plugin/)

## 功能特色

- 按照*艺术家*和*标题*的相似度排序搜索结果
- 如果有提供，支持中文歌词翻译

## 用法

从 [release](https://github.com/LudySu/Synology-LrcPlugin/releases) 下载。有两种版本：`netease_org.aum` 为原文版，仅显示原始歌词；`netease_trans.aum` 为翻译版，若有则显示中文翻译。

进入 `Audio Station -> 设置 -> 歌词插件 -> 添加`，选择你喜欢的 `aum` 文件并启用。播放歌曲时会自动下载歌词。

如果觉得歌词匹配度不高，在 **Audio Station** 右键点击歌曲，选择 `歌曲信息 -> 歌词 -> 从网络搜索`，可查看所有搜索结果。可尝试列表中的第二项。

## TODO（可能会做）

- [ ] 日文歌词通过Google自动翻译为假名、罗马音

## 构建方法

在项目根目录运行自带的 bash 脚本，生成 **Audio Station** 所需的 `.aum` 文件。会同时生成原文和翻译两个版本。

```bash
./build.sh
```

## 关于开发

### 不要使用 echo 函数

如果 PHP 脚本中有 `echo()`，**Audio Station** 将无法返回结果！

卡了很久才发现，PHP 里有 `echo()` 时，**Audio Station** 不会返回任何内容。第一次写 PHP，整个插件业余时间花了一个多星期。

### 计算最佳匹配

1. 找到完全或部分匹配的*标题*
2. 在所有艺术家中找到最佳匹配的*艺术家*（一首歌可能有多个艺术家）
3. 使用 `similar_text()` 按*艺术家*和*标题*的相似度排序搜索结果

### 中文翻译

部分歌曲有中文翻译，翻译内容在另一歌词文件中。PHP 会在时间标签匹配时，将翻译追加到原歌词行末尾。

### 用 curl 测试 Web API

**步骤1**：用以下命令搜索名为 `Tell Your World` 的歌曲，获得 JSON 响应：

```bash
curl -v -X POST http://music.163.com/api/search/pc -d "s=Tell Your World&type=1"
```

**步骤2**：在上一步响应中找到 `id`，如 `858520`，然后下载歌词文件，获得包含歌词的 JSON 响应：

```bash
curl -v -X GET "http://music.163.com/api/song/lyric?os=pc&id=858520&lv=-1&kv=0&tv=-1"
```

> 请求参数说明：`lv` = 原文歌词；`tv` = 翻译歌词；`kv` = 卡拉OK歌词，极少有。若不需要某种版本，参数设为0。

## 灵感来源
[Frank Lai 的 Synology Lyric 项目](https://bitbucket.org/franklai/synologylyric)

[Moonlib 的 PHP API](http://moonlib.com/606.html)
[Synology Audio Station 歌词模块开发指南](https://cndl.synology.cn/download/Document/Software/DeveloperGuide/Package/AudioStation/All/enu/AS_Guide.pdf)
