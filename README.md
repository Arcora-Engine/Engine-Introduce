# Arcora 引擎介绍

Arcora 是基于 Haxe 的 2D、3D 游戏与实时图形引擎，源自 Heaps 2.1 代码线。项目由引擎核心及多个配套库组成，覆盖游戏运行、图形渲染、内容制作、脚本执行和跨平台构建。

## 功能总览

| 功能领域 | 支持内容 |
| --- | --- |
| 游戏与场景 | 场景、对象和组件、游戏循环、输入、相机、声音、资源与存档。 |
| 2D 与 3D | 精灵、文字、界面、2D 光影和粒子；3D 场景、PBR 材质、灯光、阴影、地形和后期处理。 |
| 图形底层 | Vulkan、Direct3D 12、OpenGL、Direct3D 11、WebGL、NullRHI，以及部分 Metal 和实验性 WebGPU 接入；提供 RHI、FrameGraph、着色器与 GPU 资源管理。 |
| 画面增强 | 时序超分辨率、抗锯齿、动态分辨率、帧生成，以及部分 NVIDIA、AMD、Intel 和移动设备厂商方案。 |
| 动画与物理 | 动画状态机、混合、骨骼、IK、运动匹配；刚体、碰撞、约束、角色、车辆、布娃娃和软体。 |
| 音频与视频 | 实时混音、声音调度、DSP、节奏校准、空间音频；平台视频解码、外部时钟和画面传输。 |
| 内容与工具 | 资源导入、转换、构建、打包、流送与更新；场景和资源编辑、界面制作及图片视频水印。 |
| 脚本与运行时 | Haxe 脚本编译、宿主交互、运行中更新、HashLink 字节码执行、垃圾回收和 LLVM AOT 构建。 |
| 分析与基础库 | 帧、CPU、GPU、内存和线程分析，以及序列化、网络状态同步、密码学和常用文件格式处理。 |

## 特色功能

- **同一场景的多视图与 2D/3D 混合**：多个视图可以共享场景，同时分别配置相机、尺寸、刷新和输入。二维界面可以嵌入三维角色预览，二维场景也可以显示在三维物体表面，适合装备界面、监控画面等场景。详见 [核心引擎](libraries/arcora.md)。
- **可扩展的显式渲染架构**：RHI 管理 GPU 资源与同步，FrameGraph 根据读写关系安排渲染任务，着色器通过 `.asip` 和 `.aslb` 组织与交付。Vulkan 和 Direct3D 12 是主要原生路径，其他后端的功能范围见下方列表。
- **面向大型场景的资源与 GPU 渲染**：世界流送按距离和预算准备场景单元，纹理 mip 根据屏幕投影需求与预算调度；项目还可使用 GPU 场景剔除、间接绘制、虚拟纹理、虚拟几何和虚拟阴影。各功能需要准备相应资源，并取决于设备及项目配置。
- **运行中更新的 Haxe 脚本**：`hlscriptc` 可通过进程内 Direct 前端或 NativeHaxe 前端生成 HLB6。驻留脚本能在多次调用间保留实例状态，游戏运行时可更新脚本；不可信脚本需要受限的独立进程执行环境。详见 [hlscriptc](libraries/hlscriptc.md)。
- **角色动画与物理组合**：动画系统将状态机、IK、运动匹配、姿态共享和 GPU 姿态计算组合使用；Jolt 物理提供角色、车辆、布娃娃和软体，可为角色与交互场景提供不同的运动方式。详见 [动画](libraries/arcora-anim.md) 和 [物理](libraries/arcora-jolt.md)。
- **面向节奏游戏的音频时间线**：音频以采样帧调度，并将音乐、谱面、设备和主机时间关联，支持变速、循环及输入、输出和显示延迟校准；可选 Steam Audio 提供空间声场处理。详见 [音频](libraries/arcora-audio.md)。
- **从资源制作到运行时更新**：资源管线将模型、纹理、动画等内容构建为资源包，支持按页流送、CPU/GPU 预算及增量更新；编辑器可制作场景、Prefab、材质和动画。远程分发与部分格式需要项目配置服务或原生组件。详见 [资源](libraries/arcora-assets.md) 和 [编辑器](libraries/arcora-editor.md)。
- **图片与视频取证水印**：可在内容中嵌入短标识，并在没有原始素材时检测；视频可结合多帧结果和起始相位搜索。检测效果受素材尺寸、处理方式和编码影响。详见 [取证水印](libraries/arcora-forensic.md)。

## 图形底层支持

### 图形后端

| 后端 | 支持定位 |
| --- | --- |
| Vulkan | 主要原生图形路径，面向 Windows、Linux 和支持相应构建的移动设备。 |
| Direct3D 12 | 主要 Windows 原生图形路径。 |
| OpenGL / OpenGL ES | 兼容与平台图形路径，功能范围取决于上下文版本和设备扩展。 |
| Direct3D 11 | Windows 兼容图形路径。 |
| WebGL | 浏览器兼容图形路径。 |
| Metal | 已有宿主桥接和平台适配源码；Apple 平台的构建、着色器和设备接入仍需完成。 |
| WebGPU | 提供实验性接入，功能取决于浏览器和设备。 |
| NullRHI | 无窗口验证后端，用于 RHI 一致性与渲染图验证，不执行 GPU 着色器。 |

Vulkan 和 Direct3D 12 是核心引擎的主要原生路径，Windows x64 是主要发布范围。OpenGL、Direct3D 11 和 WebGL 提供覆盖范围不同的兼容路径；各后端对计算、资源同步、光线追踪和其他高级功能的支持取决于设备与驱动。

### 底层渲染能力

RHI 统一管理图形设备、队列、命令、纹理、缓冲区、采样器、渲染管线和交换链，并提供资源上传、状态转换、同步、设备能力查询和 GPU 完成后的资源回收。支持的设备还可使用独立工作队列、稀疏资源、高级资源索引和光线追踪。

FrameGraph 根据一帧中渲染任务对资源的读写关系安排执行顺序，处理屏障、临时资源复用和可并行的任务。引擎支持渲染线程及可选的独立图形后端线程；上下文受限的兼容后端采用相应的串行执行方式。

着色器支持 HxSL 和部分原生源码，`.asip` 用于组织着色器输入和契约，`.aslb` 用于交付面向目标后端的程序及相关数据。引擎还提供管线预先创建、缓存、显卡时间查询和按条件进行的资源回读。

## 场景与画面

Arcora 使用场景、对象和组件组织游戏逻辑，支持生命周期、固定时间步、输入、相机以及多个视图共享场景。二维系统支持精灵、图形、文字、滤镜、骨骼动画和粒子，现代 2D 路径还提供可按项目启用的光照和阴影。

三维系统提供前向与延迟渲染、PBR 材质、灯光和阴影、反射、地形植被、粒子及后期处理。较大场景可以使用 LOD/HLOD、资源流送、虚拟纹理、虚拟几何和虚拟阴影；间接光照和光线追踪路径需要相应内容、设备与项目配置。

画面增强库提供超分辨率、时序抗锯齿、动态分辨率和帧生成，也支持在满足设备和运行组件条件时接入部分厂商方案。算法和画质支持范围见 [arcora-renderfx](libraries/arcora-renderfx.md)。

## 动画、物理与媒体

动画系统支持片段、状态机、图层混合、骨骼、IK、重定向、运动匹配、压缩数据流送和 GPU 姿态计算。物理系统基于 Jolt Physics，提供刚体、碰撞查询、约束、角色控制器、车辆、布娃娃及软体模拟。

音频系统支持流式 PCM、混音总线、DSP、采样帧调度、节奏校准与基础 3D 空间声音；可选 Steam Audio CPU 接入支持 HRTF、遮挡、反射和路径传播。视频系统使用平台媒体后端解码画面，支持外部时钟、CPU 或 GPU 画面传输；视频音轨由应用另行播放并同步。

## 资源、脚本与运行时

资源管线支持模型、纹理、动画、字体和音频的导入与转换，以及资源构建、打包、按需加载、流送和远程更新。编辑器提供项目、场景、资源、材质与动画等内容的编辑和预览；界面库支持 RML/RCSS 和数据绑定。

`hlscriptc` 提供 Direct 与 NativeHaxe 两种脚本编译前端，将 Haxe 脚本编译为 HLB6，并支持宿主交互、运行中更新及满足条件的不可信脚本隔离。HashLink 提供字节码执行、JIT、垃圾回收和原生模块连接；Beap 支持 LLVM AOT 构建与平台打包。JavaScript 目标可用于浏览器项目。

不同字节码工具支持的版本需要分别确认：配套脚本与运行时使用 HLB6，`format` 内置的 HashLink 字节码读取功能覆盖 HLB 2 至 5。

## 库介绍

### 引擎、渲染与游戏系统

| 库 | 功能范围 |
| --- | --- |
| [arcora](libraries/arcora.md) | 场景、组件、2D/3D 渲染、RHI 和运行时服务。 |
| [arcora-anim](libraries/arcora-anim.md) | 动画状态机、骨骼、IK、运动匹配与 GPU 姿态。 |
| [arcora-jolt](libraries/arcora-jolt.md) | 刚体、碰撞查询、约束、角色、车辆、布娃娃与软体。 |
| [arcora-renderfx](libraries/arcora-renderfx.md) | 超分辨率、动态分辨率、帧生成与厂商算法接入。 |
| [arcora-rmlui](libraries/arcora-rmlui.md) | RML/RCSS 界面、数据绑定、输入和渲染。 |

### 资源、音频与视频

| 库 | 功能范围 |
| --- | --- |
| [arcora-assets](libraries/arcora-assets.md) | 素材导入、转换、构建、打包、流送与远程更新。 |
| [arcora-audio](libraries/arcora-audio.md) | 音频时钟、混音、DSP、流式播放和空间声音。 |
| [arcora-video](libraries/arcora-video.md) | 平台解码、视频时钟、画面传输和帧管理。 |
| [arcora-forensic](libraries/arcora-forensic.md) | 图片及视频水印嵌入、检测与来源标识。 |

### 编辑器与平台接入

| 库 | 功能范围 |
| --- | --- |
| [arcora-editor](libraries/arcora-editor.md) | 项目、资源、场景、Prefab 和内容编辑工具。 |
| [arcora-mobile](libraries/arcora-mobile.md) | 移动权限、设备信息、触感、分享及平台服务。 |
| [hlsdl](libraries/hlsdl.md) | SDL3 窗口、事件、输入、显示器与图形接口。 |
| [hldx](libraries/hldx.md) | Windows 窗口、Direct3D、DXGI 和原生图形接口。 |

### 脚本、运行时与开发工具

| 库 | 功能范围 |
| --- | --- |
| [hlscriptc](libraries/hlscriptc.md) | 脚本编译、HLB6、宿主交互、驻留执行与热更新。 |
| [hashlink](libraries/hashlink.md) | 程序执行、JIT、垃圾回收和原生模块连接。 |
| [beap](libraries/beap.md) | 项目配置、依赖、构建、LLVM AOT 与平台打包。 |
| [hlmp](libraries/hlmp.md) | 帧、CPU、GPU、内存、线程和垃圾回收分析。 |

### 数据与基础工具

| 库 | 功能范围 |
| --- | --- |
| [hxbit](libraries/hxbit.md) | 二进制序列化、对象状态同步和远程调用。 |
| [crypto](libraries/crypto.md) | 摘要、认证、加密、密钥派生和随机数。 |
| [format](libraries/format.md) | 图像、音频、归档、文档及字节码格式处理。 |