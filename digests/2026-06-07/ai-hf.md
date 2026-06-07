# Hugging Face 热门模型日报 2026-06-07

> 数据来源: [Hugging Face Hub](https://huggingface.co/) | 共 30 个模型 | 生成时间: 2026-06-07 14:39 UTC

---

# Hugging Face 热门模型日报 (2026-06-07)

## 📰 今日速览
本周 Hugging Face 迎来多项重磅发布，DeepSeek-V4-Pro 以绝对优势霸榜，狂揽近4700赞与超550万下载。Google 正式发布 Gemma-4 系列并原生支持 Any-to-Any 多模态，社区量化版迅速跟进。Nvidia 则展现了极强的生态统治力，一口气推出视觉定位 LocateAnything、世界模型 Cosmos3 全家桶及超大 MoE 架构 Nemotron-3-Ultra。此外，MoE（混合专家）架构已成为新晋开源模型的标配，Qwen3.6 的社区去审查微调版与量化版均获得极高下载量，本地化部署需求持续爆发。

---

## 🔥 热门模型

### 🧠 语言模型（LLM、对话模型、指令微调）
- **[deepseek-ai/DeepSeek-V4-Pro](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro)**
  作者: deepseek-ai | 👍 4,689 | ⬇️ 5,515,325
  一句话说明：新一代 Pro 级旗舰大模型，凭借极强性能与开源权重创下本周最高下载与点赞纪录。

- **[nvidia/NVIDIA-Nemotron-3-Ultra-550B-A55B-BF16](https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Ultra-550B-A55B-BF16)**
  作者: nvidia | 👍 151 | ⬇️ 49,784
  一句话说明：550B 总参数、55B 激活的超大 MoE 模型，Nvidia 挑战开源大模型极限的力作。

- **[openbmb/MiniCPM5-1B](https://huggingface.co/openbmb/MiniCPM5-1B)**
  作者: openbmb | 👍 779 | ⬇️ 114,329
  一句话说明：面壁智能最新 1B 端侧小钢炮，以极小体积提供越级的文本生成能力。

- **[sapientinc/HRM-Text-1B](https://huggingface.co/sapientinc/HRM-Text-1B)**
  作者: sapientinc | 👍 717 | ⬇️ 162,822
  一句话说明：全新架构的 1B 文本模型，高点赞下载比显示出社区对其潜力的极高期待。

- **[LiquidAI/LFM2.5-8B-A1B](https://huggingface.co/LiquidAI/LFM2.5-8B-A1B)**
  作者: LiquidAI | 👍 536 | ⬇️ 118,326
  一句话说明：Liquid 基础模型二代半，8B 总参仅 1B 激活，将 MoE 稀疏度做到极致。

### 🎨 多模态与生成（图像、视频、音频、文本到X）
- **[SulphurAI/Sulphur-2-base](https://huggingface.co/SulphurAI/Sulphur-2-base)**
  作者: SulphurAI | 👍 1,583 | ⬇️ 1,715,710
  一句话说明：基于 LTX-2.3 的视频生成基础模型，支持 GGUF 格式，引爆本地视频生成热潮。

- **[google/gemma-4-12B-it](https://huggingface.co/google/gemma-4-12B-it)**
  作者: google | 👍 655 | ⬇️ 434,969
  一句话说明：Gemma 第四代指令微调版，原生跨模态 Any-to-Any 架构，多模态生态新基座。

- **[nvidia/Cosmos3-Super](https://huggingface.co/nvidia/Cosmos3-Super)** (及 Nano/Text2Image/Image2Video 系列)
  作者: nvidia | 👍 340+ (系列总和) | ⬇️ 63,000+
  一句话说明：Nvidia 物理世界生成模型 Cosmos3 全家桶，覆盖文生图、图生视频等全链路。

- **[stepfun-ai/Step-3.7-Flash](https://huggingface.co/stepfun-ai/Step-3.7-Flash)**
  作者: stepfun-ai | 👍 346 | ⬇️ 43,196
  一句话说明：阶跃星辰最新视觉语言快速版，多模态理解与生成双升级。

- **[nvidia/LocateAnything-3B](https://huggingface.co/nvidia/LocateAnything-3B)**
  作者: nvidia | 👍 1,499 | ⬇️ 115,556
  一句话说明：精准视觉定位模型，专攻图像中的特征提取与目标找寻，填补细分空白。

- **[meituan-longcat/LongCat-Video-Avatar-1.5](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.5)**
  作者: meituan-longcat | 👍 531 | ⬇️ 1,884
  一句话说明：美团推出的音频文本驱动数字人视频生成模型，动作流畅度表现抢眼。

- **[ideogram-ai/ideogram-4-fp8](https://huggingface.co/ideogram-ai/ideogram-4-fp8)**
  作者: ideogram-ai | 👍 330 | ⬇️ 4,377
  一句话说明：Ideogram 4 文生图模型的 fp8 量化版，兼顾出色文字渲染与低显存需求。

- **[bosonai/higgs-audio-v3-tts-4b](https://huggingface.co/bosonai/higgs-audio-v3-tts-4b)**
  作者: bosonai | 👍 186 | ⬇️ 7,557
  一句话说明：4B 参数的多模态语音合成模型，情感表现力与音色克隆能力受关注。

- **[nvidia/nemotron-3.5-asr-streaming-0.6b](https://huggingface.co/nvidia/nemotron-3.5-asr-streaming-0.6b)**
  作者: nvidia | 👍 239 | ⬇️ 3,439
  一句话说明：专为流式语音识别设计的轻量模型，支持缓存感知，低延迟实战利器。

### 🔧 专用模型（代码、数学、医疗、嵌入）
- **[JetBrains/Mellum2-12B-A2.5B-Thinking](https://huggingface.co/JetBrains/Mellum2-12B-A2.5B-Thinking)**
  作者: JetBrains | 👍 246 | ⬇️ 16,924
  一句话说明：专精代码生成与逻辑思考的 MoE 模型，开发者 IDE 智能助手的理想内核。

- **[PaddlePaddle/PaddleOCR-VL-1.6](https://huggingface.co/PaddlePaddle/PaddleOCR-VL-1.6)**
  作者: PaddlePaddle | 👍 265 | ⬇️ 9,084
  一句话说明：融合 ERNIE4.5 视觉能力的最新版 OCR 王牌，文档解析与场景文字识别利器。

- **[nvidia/PiD](https://huggingface.co/nvidia/PiD)**
  作者: nvidia | 👍 314 | ⬇️ 1,082
  一句话说明：专注图像超分辨率的扩散模型，画质增强与细节修复的专用工具。

### 📦 微调与量化（社区微调、GGUF、AWQ）
- **[HauhauCS/Qwen3.6-35B-A3B-Uncensored-HauhauCS-Aggressive](https://huggingface.co/HauhauCS/Qwen3.6-35B-A3B-Uncensored-HauhauCS-Aggressive)**
  作者: HauhauCS | 👍 1,503 | ⬇️ 2,923,564
  一句话说明：Qwen3.6 MoE 的去审查激进微调版，凭借高自由度对话斩获近300万下载。

- **[unsloth/gemma-4-12b-it-GGUF](https://huggingface.co/unsloth/gemma-4-12b-it-GGUF)**
  作者: unsloth | 👍 437 | ⬇️ 568,158
  一句话说明：跟进极快的 Gemma-4 GGUF 量化版，让普通玩家也能本地跑起多模态大模型。

- **[nvidia/Qwen3.6-35B-A3B-NVFP4](https://huggingface.co/nvidia/Qwen3.6-35B-A3B-NVFP4)**
  作者: nvidia | 👍 200 | ⬇️ 1,185,362
  一句话说明：Nvidia 官方出手使用 Model Optimizer 对 Qwen3.6 进行 NVFP4 量化，超百万下载验证其实用性。

- **[unsloth/gemma-4-12B-it-qat-GGUF](https://huggingface.co/unsloth/gemma-4-12B-it-qat-GGUF)**
  作者: unsloth | 👍 107 | ⬇️ 85,842
  一句话说明：采用量化感知训练（QAT）的 Gemma 4 GGUF，相比普通量化保留更多模型精度。

---

## 📡 生态信号
本周生态呈现三大趋势：**MoE 架构全面普及**，Qwen3.6、Nemotron-3、LFM2.5 等新晋模型无一例外采用 MoE，总参数大而激活参数小成为开源对抗闭源算力门槛的共识；**Any-to-Any 多模态成基座标配**，以 Gemma-4 为代表，纯文本 LLM 的发布热度正在被原生支持图文音视的统一模型超越；**量化与去审查微调极速响应**，Unsloth 与 HauhauCS 等社区力量在大厂发布数小时内即推出 GGUF 和去审查版，NVFP4 等新量化格式也迅速抢占下载榜，印证了本地部署和边缘侧推理的强烈需求。开源权重正加速逼近闭源模型能力边界。

---

## 💡 值得探索
1. **[nvidia/LocateAnything-3B](https://huggingface.co/nvidia/LocateAnything-3B)**：视觉定位是 Agent 与机器人交互的核心能力，该模型在图文对齐基础上专攻目标定位，相比通用 VLM 在空间指代任务上更精准，值得相关应用开发者深入测试。
2. **[SulphurAI/Sulphur-2-base](https://huggingface.co/SulphurAI/Sulphur-2-base)**：视频生成赛道的热门黑马，且提供 GGUF 格式。它在保证生成质量的同时极大降低了显存门槛，是本地运行和二次开发视频生成的绝佳切入点。
3. **[JetBrains/Mellum2-12B-A2.5B-Thinking](https://huggingface.co/JetBrains/Mellum2-12B-A2.5B-Thinking)**：不同于通用大模型，Mellum2 专门针对代码与逻辑推理进行了优化，且加入了“Thinking”机制。对于构建代码助手或自动化开发工具链而言，其表现可能超越同量级通用模型。

---
*本日报由 [agents-radar](https://github.com/yanzi6039/agents-radar) 自动生成。*