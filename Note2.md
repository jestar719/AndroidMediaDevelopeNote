# Android Media API

### MediaExtractor

该类可以对容文件进行读取控制。通过URL获取容器文件的轨道数量，轨道信息(Track).然后选择要解码的轨道，从轨道中读取数据。

所以音轨和视频轨道需要两个'MediaExtractor'进行分别读取。分别解码

### MediaCodec

用与对数据进行编解码的类。在创建时需要选择Codec的类型。

其内部维护两个队列，InputQueue和OutputQueue。

### DRM（Digital Rights Management）数字版权保护

调用configure方法时传入MediaCrypto类。

MediaCodec负责解码，所以它需要一个MediaCrypto对象和一个MediaDrm对象。后者提供sessionId，前者根据id从Framwork中获取对应的license,

### Camera2

* CameraManager 摄像头管理器,通过'getCameraCharacterisitics(String)'方法获取'CameraCharacterisitics'
* CameraCharacteristics 摄像头特征，描述摄像头支持的各种特性
* CameraDevice 系统摄像头
    * 通过StatCallback监听摄像头的四种状态。onOpen,onCloseed,onDisconnected,onError
    * 创建CameraCaptureSession
    * 创建CaptureRequest请求
* CameraCaptureSession 控制预览，拍照的类。创建后关联对应的摄像头，直到摄像头关闭
    * setRepeatingRequest() 控制预览，实际上是不断重复捕获图像。
    * stopRepeating() 停止预览
    * capture() 提交请求
    * 通过StateCallback回调管理CameraDevice配置状态。onConfigured /onConfigureFailed
    * 通过CaptureCallback回调管理捕获请求结果
* CameraRequest 预览和拍照所需参数。代表一次捕获请求及各种参数
* CameraRequest。Builder 负责生成CameraRequest对象

#### 相机控制步骤
* 通过CameraManager获取摄像头id列表，根据id获取摄像头信息（CameraCharActeristics）,取得需要控制的摄像头ID
* 通过CameraManager，使用摄像头的ID打开摄像头。通过回调获取CameraDevice
* 需要创建CameraCaptureSession 通过它发送请求，获取数据。创建时需要设定接收数据端列表（List<Surface>) 
    * new Surface(TextureSurface)/SurfaceView.getHolder()  手机屏幕端
    * ImageReader.getSurface()  用与拍照
    * MediaRecoder.getSurface() 用与录像 
* 发送请求
    * 预览 CameraDevice.TEMPLATE_PREVIEW CameraSession需要使用'setRepeatingRequest()'发送持续请求,target指定为手机屏幕'Surface'
    * 拍照 CameraDevice.TEMPLATE_STILL_CAPTURE  CameraSession需要使用'capture()'发送一次请求，target指定为'ImageReader.getSurface()'请求前使用'stopRepeating()'
    * 录象 CameraDevice.TEMPLATE_RECORD CameraSession需要使用'setRepeatingRequest()'发送持续请求,target指定为'MediaRecoder.getSurface()',使用MediaRecoder.start()/stop()控制录制
    
