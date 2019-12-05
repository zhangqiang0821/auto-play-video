# 1、下载 jsmpeg.js

ffmpeg : http://ffmpeg.zeranoe.com/builds/ win64

https://evermeet.cx/ffmpeg/ mac OS X 64

jsmpeg.js :https://github.com/phoboslab/jsmpeg

#、 win 64 版为例：
下载 ffmpeg 后,解压 ffmpeg，进入 bin 目录看到几个 exe 这就是处理程序。

win+r 输入 cmd 打开命令窗口，转到 bin 目录，并输入转码代码:
ffmpeg -i demo1.mp4 -f mpegts -codec:v mpeg1video -codec:a mp2 -b 0 ts.ts

demo1.mp4 是我们要原始视频 （这里我直接把视频放在 bin 目录里）

回车运行后，bin 目录输出 ts.ts

# 更多参数配置命令

ffmpeg -i 1.mp4 -f mpegts -codec:v mpeg1video -s 640x1040 -b:v 1500k -r 30 -bf 0 -codec:a mp2 -ar 44100 -ac 1 -b:a 128k out.ts
原始视频　　　　　　　　　　　　　 　 导出尺寸　 导出码率 导出帧频　　　　　　　 音频采样率　　 音频比特率

ps:相关参数请参看https://github.com/phoboslab/jsmpeg 文档

# 播放控制：　

play() – start playback
pause() – pause playback
stop() – stop playback and seek to the beginning
destroy() – stops playback, disconnects the source and cleans up WebGL and WebAudio state. The player can not be used afterwards.
volume – get or set the audio volume (0-1)
currentTime – get or set the current playback position in seconds

例如 player.pause()

# 注意：

1 清晰度和原视频差不多的条件下，码率几乎需要多设置 1 倍；
2 跨域问题
jsmpeg 请求 ts 媒体文件，使用的是 XMLHttpRequest，且同时使用了 setRequestHeader("Range", "bytes=xxxx"); 来请求切割数据，这样拿到我们设定 xxx 的字节的数据后，我们可以及时对数据进行处理（jsmpeg 设置参数 chunkSize 就是这里的 xxx）。

因为跨域，切割数据 服务器认为这是对文件进行二次操作所以被拒绝。

那么对服务进行配置一下 ：我这里使用的 serve 工具 跑个服务即可

# serve 实例：

1.首先需要安装 node

2.使用 npm 命令行来安装 serve

npm install -g serve

3. 安装完成后，使用 serve -s .\dist 进入文件文件目录就可以了

# html 代码

<body>
    <canvas id="video-canvas"></canvas>    
    <script type="text/javascript" src="jsmpeg.min.js"></script>
    <script type="text/javascript">
    window.onload=function(){
        var canvas = document.getElementById('video-canvas');
        var url = 'demo-ts.ts';
        //var url = 'ws://'+document.location.hostname+':8082/';
        var player = new JSMpeg.Player(url, {canvas: canvas,loop: true, autoplay: true});
    }
    </script>
</body>
