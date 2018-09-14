# 视频相关术语
### Codec
Codec是一种对视频文件进行编码/解码的程序，包括对视频数据和音频数据的编解码

视频数据在不处理的情况下，每秒视频体积为宽*高*3*24。所以一个720P的视频每秒2.7M,一分钟为3.9GB.所以需要压缩处理
* encode(编码)
    通过算法对视频文件进行压缩
* decode(解码) 
    对编码后的文件进行解压

一个完整的流程为：录制的原始数据->编码->播放平台->解码->播放    

### 常用编码方案
* H.265/MPEG-H HEVC
* H.264/MPEG-4 AVC
    * x264
    * Nero Digital
    * QuickTime H.264
    * DivX Pro Codec
* H.263/MPEG-4 
    * DivX Pro Codec
    * Xvid
    * FFmpeg MPEG-4
    * 3ivx

### Container format file(视频容器文件)
常见的视频文件如:'mp4,rmvb,avi',严格来讲不应该叫视频文件，而是应该叫做容器文件。

它属与被Codec编码后的产物，做为一个容器，包含了视频数据，音频数据，有的还有字幕等其它数据。

它是一个结构化的窗口，包含的所有数据都必须按照一定的规范放在文件的指定位置

#### MP4的结构
MP4是格式规范，严格定义了文件的位置。

MP4中存在一个'meta data',属于其它文件的位置索引记录。然后视频数据和音频数据都使用'Track'保存

###### meta data
 里面包含了很多头文件。每个头文件称为Atom Header。Atom Header分为Leaf Atom和Container Atom。
 * Leaf Atom 一个连接着字符串信息的头文件
 * Container Atom 一个包含了若干个子Atom的头文件

两种Atom之间有层级关系。顶层的Atom为Movie Atom（moov）．

其中最重要的为'Sample Table Atoms'采样索引表。这个索引表保存了所有视频数据与视频时间的对应关系（以微秒为单位），还包括每个数据的大小及起始时间

### 播放器
通常由三个部分组成
* 读取器'Extractor'
    负责读取数据源
* 渲染器'TrackRender'
    接收数据，渲染到屏幕上
* 加载控制器'Load Controller'
    控制读取数据的策略

传统的播放器需要把所有的头文件读入内存，然后在内存中构建对应的表，然后根据时间表表，从数据源中读数据到缓存中

现在新出现的两种技术'Adaptive Streaming'自适应播放使得分段式视频产生。'Progressive Downloading'渐进式下载使得在线视频播放得以实现

### Adaptive Streaming
自适应播放适应的是网络状态。视频被分割成很多小块(Chunk),一般以5-10秒为单位。后台提供一个索引表，记录同一视频不同清晰度版本的Url。

前端播放器根据自身网络状态选择对应的url进行加载

总而言之，自适应视频不是一个完整的视频对应一个容器文件，而是分割为多个容器保存，用一张索引表记录

#### 自适应视频的规范
* HLS 苹果家的。 对应的索引表格式为'M3u8' 
* DASH 对应的索引表格式为'MPD'

###### MPD解析
* adaptionset  每个MPD至少有两个，记录音频，视频文件的位置
* representation 每个adaptionset里会有多个。每个都代表了不同分辨率的视频/音频
    * bandwidth 该分辨率视频所需最低带宽，单位为byte/s
    * height 视频高度，用来表示清晰度
    * baseurl 视频的url。相对url，实际上就是视频的文件名。完整的url应该是MPD的path+baseUrl