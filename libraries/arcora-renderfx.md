# arcora-renderfx 画面增强

[返回总览](../README.md)

`arcora-renderfx` 是 Arcora 可选的画面增强库，提供超分辨率、时序抗锯齿、动态分辨率、帧生成及部分厂商图形技术接入。它与引擎的渲染管线、FrameGraph 和图形资源系统配合工作。

## 功能总览

| 功能 | 支持范围 |
| --- | --- |
| 内置超分辨率 | Arcora TSR 和便携式空间放大，支持锐化和不同画质档。 |
| 抗锯齿 | 原生分辨率 TAA，以及结合升采样的 TAAU。 |
| 厂商超分辨率 | NVIDIA DLSS、AMD FidelityFX / FSR、Intel XeSS、Arm ASR、Qualcomm SGSR。 |
| 神经超分辨率 | Adreno Neural Fusion、Arm NSS，依赖对应设备与厂商组件。 |
| 动态分辨率 | 根据 GPU 目标时间调整渲染比例，并可按视图分别配置。 |
| 帧生成 | Arcora FG、DLSS-G、FidelityFX FG、XeSS-FG、Qualcomm 帧外推，以及部分移动厂商帧插值。 |
| 图形辅助 | 低延迟阶段标记、Opacity Micromap、调试画面、能力查询和算法回退。 |

## 特色功能

- **自带算法与厂商方案共同接入**：项目可以使用 Arcora TSR、TAA、空间放大和帧生成，也可以根据设备与运行组件选择 DLSS、FSR、XeSS 或移动厂商方案。
- **多视图独立画面策略**：主画面、小地图等视图可以分别使用分辨率策略和时序历史；相机切换、画面尺寸变化和设备恢复时，需要重新建立相应历史。
- **帧生成与界面合成**：内置 Arcora FG 利用前后真实帧及运动、深度信息生成插帧，界面可按所选方案单独合成；还可接入厂商帧生成或帧外推方案。
- **按设备能力选择路径**：动态分辨率、画质档位和回退方案可结合可用后端配置。厂商功能需要匹配的显卡、驱动、图形接口及 SDK 或运行库。

## 超分辨率与抗锯齿

内置 Arcora TSR 使用颜色、深度、法线、运动矢量、相机抖动和时序历史重建画面；首帧或历史失效后会重新建立历史。TAA 用于原生分辨率的时序抗锯齿，TAAU 将抗锯齿与升采样结合；TAA 按原始尺寸输出，不能同时使用动态分辨率。

便携式空间放大使用当前帧进行升采样和锐化，提供计算着色器与图形绘制两种路径，适用于满足相应图形能力的桌面、移动和 Web 目标。

厂商方案包括 NVIDIA DLSS、AMD FidelityFX / FSR 和 Intel XeSS；Android 路径提供 Arm ASR、Qualcomm SGSR1 和 SGSR2，其中 Vulkan SGSR2 支持两遍或三遍计算及正向、反向深度。Adreno Neural Fusion 与 Arm NSS 还提供依赖专用设备能力和厂商组件的神经超分辨率。各方案可用性取决于硬件、驱动、图形接口与运行组件。

画质选项覆盖原生抗锯齿、超高质量、质量、均衡、性能和自定义比例；可按可用能力选择算法或设置回退路径。

## 动态分辨率与多视图

动态分辨率根据 GPU 时间目标调整渲染比例，并受项目设定的上下限约束；控制器可根据连续超预算或低于预算的帧数调整比例，减少频繁波动。该功能默认关闭。

不同视图可以分别管理输出尺寸、分辨率策略和时序历史，适合主画面、小地图及其他独立视图。相机切换、画面尺寸变化和设备恢复会影响历史数据，需要重新建立相应的重建状态。

## 帧生成与低延迟

内置 Arcora FG 结合前后真实帧以及运动、深度与遮挡信息产生插帧。除此之外，还提供 NVIDIA DLSS Frame Generation、AMD FidelityFX Frame Generation、Intel XeSS-FG、Qualcomm 帧外推，以及 Adreno Neural Fusion 和 Arm NFRU 帧插值接入。

生成画面不额外推进游戏逻辑或物理模拟，输入仍由应用处理。UI 可以作为独立图层在生成的画面后合成，也可以使用不含 UI 的场景图像；可用方式取决于具体方案。

Arcora FG 需要计算着色器及支持采样、存储的 RGBA16F 纹理，可生成一到两张独立纹理；插帧处理包含运动场空洞修复、深度一致性检查以及遮挡与透明区域保护。它支持线性、sRGB、PQ 和 HLG 颜色输入，并处理曝光和 Alpha 模式。厂商方案按实现使用交换链代理或独立纹理输出；Qualcomm 帧外推需要相应 OpenGL ES 扩展。

低延迟能力提供模拟、绘制和显示等阶段标记，供支持的厂商工具分析。XeLL 需要对应 XeSS-FG 运行组件和显示配置。

## 平台与图形接口

| 平台与接口 | 支持范围 |
| --- | --- |
| Windows Direct3D 12 | Arcora TSR、DLSS、FidelityFX、XeSS 等路径；厂商功能取决于设备和运行组件。 |
| Windows / Linux Vulkan | Arcora TSR，以及相应的 DLSS、FSR3 超分辨率或帧生成路径；XeSS Linux 运行库不在当前接入范围内。 |
| Windows Direct3D 11 | XeSS 超分辨率和适用的空间放大路径。 |
| Android Vulkan | Arm ASR、SGSR2 计算路径，以及满足条件的移动神经图形技术；ASR 和 SGSR2 构建面向 arm64、API 26 及以上。 |
| Android OpenGL ES | SGSR1、SGSR2 图形路径和 Qualcomm 帧外推，具体取决于设备扩展。 |
| Apple Metal | 已有 MetalFX 空间、时序、帧插值与去噪超分辨率的宿主接入源码；Apple 宿主扩展、目标构建和设备接入仍需完成。 |
| WebGPU | 提供实验性浏览器宿主接入；算法可用性取决于浏览器和设备。 |

DLSS 需要有效的 NVIDIA 项目或应用身份。Arm NSS 和 NFRU 需要 Vulkan 1.3 及相应神经图形扩展，NFRU 还需要光流能力；Adreno Neural Fusion 需要匹配的设备、厂商组件和图像格式，超分辨率输出宽高分别为输入的两倍。

## 渲染集成与诊断

画面增强可使用场景颜色、深度、运动矢量、相机抖动、曝光、法线、粗糙度和响应遮罩等数据。标准管线在 tone mapping 前提供线性 HDR 场景颜色；算法对数据的格式与语义有各自要求。

Vulkan Opacity Micromap 功能可以使用预制透明度数据辅助光线追踪，依赖相应设备扩展和内容制作流程；当前构建范围限定为单个三角形几何体。

诊断功能包括算法选择、画面尺寸、资源与耗时信息，内置 TSR 可显示历史权重、遮挡、运动、深度、闪烁、透明区域、历史锁定和画面差异。着色器内容通过 `.asip` 与目标后端匹配的 `.aslb` 交付；厂商算法还需要对应 SDK 或运行库。