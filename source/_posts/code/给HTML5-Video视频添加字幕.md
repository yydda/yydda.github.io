---
title: 给 HTML5 Video 视频添加字幕
categories:
  - Code
tags:
  - HTML5
  - Video
abbrlink: 26670
date: 2020-04-19 15:25:25
---

随着 HTML5 的出现，我们可以用更便捷的方式在网页上添加视频。然而，如果你需要让视频配有字幕，该怎么办呢？

HTML5 Video 标签支持 WebVTT（Web 视频文本轨道）格式的字幕文件，这意味着你可以通过创建一个简单的 WebVTT 文件来为你的 HTML5 视频添加字幕。

下面是一个简单的示例。假设你有一个名为 myvideo.mp4 的视频，以及一个名为 mysubtitles.vtt 的字幕文件。你可以按照以下步骤将它们组合起来：

## 给 HTML5 Video 视频添加字幕

### 步骤 1：准备你的视频

首先，你需要引入一个 `<video>` 标签来嵌入你的视频：

```html
<video controls>
  <source src="myvideo.mp4" type="video/mp4" />
</video>
```

在上面的代码中，我们使用了 `<source>` 元素来指定视频文件的位置和类型。`controls` 属性使浏览器显示包括播放/暂停按钮、音量控制和时间线等控件的默认视频控件。

### 步骤 2：准备你的字幕文件

接下来，你需要准备一个 WebVTT 字幕文件。WebVTT 文件具有以下格式：

```text
WEBVTT

00:00:00.000 --> 00:00:05.000
Hello, world!

00:00:05.000 --> 00:00:10.000
This is a sample subtitle file.
```

在上面的示例中，我们定义了两个字幕片段。每个字幕片段都有一个开始时间和一个结束时间，以及一个文本内容。你可以使用 [WebVTT 字幕编辑器](https://subplayer.js.org/) 来创建和编辑 WebVTT 文件。

### 步骤 3：将字幕文件添加到 video 标签中

现在，你可以将字幕文件与 video 标签结合起来：

```html
<video controls>
  <source src="myvideo.mp4" type="video/mp4" />
  <track src="mysubtitles.vtt" kind="subtitles" srclang="en" label="English" />
</video>
```

在上面的代码中，我们使用了 `<track>` 元素来指定字幕文件的位置和类型。`kind` 属性指定字幕类型，这里是 “subtitles”。`srclang` 属性指定字幕语言（这里是英语），而 `label` 属性则用于为字幕添加一个标签（这里是“English”）。

现在，如果你播放该视频，字幕将自动显示在视频控件下方。

## 浏览器支持

目前，所有主流浏览器都支持 HTML5 Video 标签。然而，WebVTT 字幕文件的支持程度则不尽相同。下表列出了各个浏览器对 WebVTT 的支持情况：

| 浏览器              | 支持情况 |
| ------------------- | -------- |
| Chrome              | 支持     |
| Firefox             | 支持     |
| Safari              | 支持     |
| Edge                | 支持     |
| Internet Explorer   | 不支持   |
| Opera               | 支持     |
| Android Browser     | 支持     |
| Chrome for Android  | 支持     |
| Firefox for Android | 支持     |
| Opera for Android   | 支持     |
| Safari on iOS       | 支持     |
| Samsung Internet    | 支持     |

## `<track>` 标签属性

在上文提到的 `<track>` 标签中，除了 `src`、`kind`、`srclang` 和 `label` 属性外，还有以下常用属性：

- `default`：如果设置了这个属性，那么这个字幕文件将成为默认字幕。如果视频没有其它可用的字幕文件，则将显示此字幕。
- `srcLang`：指定外部字幕文件中使用的语言。
- `mime-type`：指定外部字幕文件的 MIME 类型。

样式控制方面，可以使用 CSS 对字幕进行控制。可以使用以下属性：

- `text-shadow`：这个属性可以给字幕添加阴影效果。例如，`text-shadow: 2px 2px 2px rgba(0,0,0,0.5);`会在字幕下面添加一个黑色半透明阴影。
- `font-size`：可以控制字幕的字号大小。
- `font-family`：可以指定字幕所使用的字体。
- `color`：可以指定字幕的颜色。
- `background-color`：可以为字幕添加背景色。
- `opacity`：可以使用这个属性来改变字幕的透明度。

需要注意的是，不是所有浏览器都支持使用 CSS 样式控制字幕。一些浏览器可能只支持字幕本身的样式属性，而不支持 CSS 样式属性。

## 替代方案

HTML5 字幕系统代码如下，可以参考使用：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>HTML5 自定义字幕系统</title>
    <style>
      video {
        width: 100%;
      }

      .subtitle {
        position: absolute;
        bottom: 10px;
        left: 50%;
        transform: translateX(-50%);
        font-size: 20px;
        color: #fff;
        text-align: center;
        background-color: rgba(0, 0, 0, 0.6);
        padding: 10px 15px;
        border-radius: 5px;
        opacity: 0;
        transition: opacity 0.2s ease-in-out;
      }

      .show-subtitle {
        opacity: 1;
      }
    </style>
  </head>
  <body>
    <video id="myVideo" controls>
      <source src="video.mp4" type="video/mp4" />
      <!-- 添加字幕轨迹 -->
      <track
        label="My English Subtitles"
        kind="subtitles"
        srclang="en"
        src="subtitles.vtt"
        default
      />
    </video>

    <!-- 自定义字幕区域 -->
    <div id="subtitle" class="subtitle"></div>

    <script>
      var video = document.getElementById("myVideo");
      var subtitleDiv = document.getElementById("subtitle");

      // 监听视频时间更新事件
      video.addEventListener("timeupdate", function () {
        var currentTime = video.currentTime;
        // 获取当前时间对应的字幕
        var currentSubtitle = getSubtitle(currentTime);
        if (currentSubtitle) {
          // 更新字幕内容
          subtitleDiv.innerHTML = currentSubtitle.text;
          // 显示字幕
          subtitleDiv.classList.add("show-subtitle");
        } else {
          // 隐藏字幕
          subtitleDiv.classList.remove("show-subtitle");
        }
      });

      // 获取当前时间对应的字幕
      function getSubtitle(currentTime) {
        var track = video.textTracks[0];
        for (var i = 0; i < track.cues.length; i++) {
          var cue = track.cues[i];
          if (currentTime >= cue.startTime && currentTime <= cue.endTime) {
            return cue;
          }
        }
        return null;
      }
    </script>
  </body>
</html>
```

这个字幕系统使用了自定义的字幕区域，并通过 CSS 样式进行了一些控制。在脚本中，我们监听了视频的时间更新事件，通过 `getSubtitle()` 函数来获取当前时间对应的字幕，并更新字幕内容。同时，我们也通过 CSS 样式来控制字幕的缓慢显示和隐藏。

## 结论

通过创建 WebVTT 字幕文件并将其与 HTML5 Video 标签结合使用，你可以轻松地为视频添加字幕。这对于那些希望提供多种语言选项、提高可访问性或增强用户体验的网站和应用程序是非常重要的。

## 参考资料

- [HTML5 Video](https://www.w3schools.com/html/html5_video.asp)
