# Krea 2 主模型与特调模型效果对比

> 本文整理 Krea 2 开源模型家族（主模型 + 官方特调 LoRA + 社区特调模型）的效果对比。
> 信息截至 2026 年 10 月，依据：Krea 官方文档/HuggingFace 仓库、ComfyLab 实测（2026-07）、
> n8n-diy 实测（2026-08）。本人未在本机跑全部模型，结论以来源评测为准。

## 0. 背景速览

- Krea 2 于 **2026-06-22** 开源（Krea 2 Community License），是 Krea AI **从零自研**的图像模型，**不是 FLUX 的套壳**；
- 架构：独立 DiT **12.9B**，文本编码器为 **Qwen3VL-4B**（支持图文混合理解），VAE 为 `qwen_image_vae`；
- 与 2025 年的「FLUX.1 Krea [dev]」是两回事：后者是 BFL 底座的微调版，LoRA 与工作流互不通用；
- ComfyUI 0.26.0+ 原生支持，**零自定义节点**；官方模板库自带文生图工作流。

## 1. 主模型对比

| 模型 | 定位 | 速度/步数 | 画质特点 | 本地显存（FP8/量化） | 适用场景 | 注意 |
| --- | --- | --- | --- | --- | --- | --- |
| **Krea-2-Raw** | 未蒸馏基线模型 | 慢，步数多 | 细节上限最高，风格可塑性强 | BF16 约 24.8GiB，FP8 约 12GiB | **训练自用 LoRA**、追求极致画质 | 官方 9 款特调 LoRA 全部用 Raw 训练 |
| **Krea-2-Turbo** | 蒸馏加速版 | **8 步出 2048×2048** | 质量接近 Raw，速度快一个量级 | 同上；50 系可 NVFP4，低配可用社区 INT8/INT4 | 日常出图、概念快速试稿、批量生产 | 蒸馏时关闭了引导，**反向提示词基本失效** |
| **Krea 2 Large**（在线版） | 平台/API 旗舰 | — | 风格保真（Style Fidelity）强：Contra Labs 四模型评测中仅次于 GPT Image 2，远超 Gemini 3 Pro Image 与 Seedream 5.0 Lite | 云端，无本地要求 | **风格迁移/风格简报**（参考图定调，换主体不跑偏） | 提示词主导的创作类任务（无参考图）表现一般，H2H 投票第 4（39.5%） |

## 2. 官方 9 款特调 LoRA 效果对比（核心表格）

评测条件（ComfyLab，RTX 3090）：同一提示词 + 同一种子，8 步 / cfg 1.0 / euler-simple / LoRA 强度 1.0，只换 LoRA 与触发词。
**关键结论：必须加触发词！** 不加触发词时 9 款中只有 2 款保持特色，其余 7 款退化成雷同的"通用插画风"。

| 特调 LoRA | 触发词 | 实际风格效果 | 不加触发词时 | 推荐强度 | 适合题材 |
| --- | --- | --- | --- | --- | --- |
| `krea2_darkbrush` | `monochrome ink wash style` | 单色水墨，笔触松弛写意 | ✅ 仍保持特色（9 款中仅有的 2 款之一） | 1.0 | 国风海报、艺术插画 |
| `krea2_dotmatrix` | `monochrome stippling style` | 黑白点画/点彩，印刷感强 | ❌ 退化为通用插画 | 1.0 | 报纸/复古印刷风格 |
| `krea2_kidsdrawing` | `naive expressive sketch style` | 童趣速写，粗线条、天真感 | ❌ 退化为通用插画 | 1.0 | 儿童绘本、包装插画 |
| `krea2_neondrip` | `textured abstract style` | 抽象油画质感，高饱和用色 | ❌ 退化为通用插画 | 1.0 | 潮流海报、专辑封面 |
| `krea2_rainywindow` | `rainy window style` | 玻璃雨痕叠加效果，氛围感强 | ❌ 完全失去雨痕效果 | 1.0 | 情绪氛围图、电影感画面 |
| `krea2_retroanime` | `purple retro anime style` | 80/90 年代复古动漫，紫色主导色调 | ❌ 无紫色调、无动漫感 | 1.0 | 怀旧动漫风、赛博复古 |
| `krea2_softwatercolor` | `art deco watercolor style` | 水彩 + 装饰艺术线条 | ❌ 退化为通用插画 | 1.0 | 清新插画、贺卡、装修效果图 |
| `krea2_sunsetblur` | `ethereal motion blur style` | 梦幻动态模糊，暖色调 | ❌ 退化为通用插画 | 1.0 | 音乐 MV 静帧、梦幻场景 |
| `krea2_vintagetarot` | `vintage tarot style` | 复古塔罗牌插画，扁平配色 | ✅ 仍保持特色 | 1.0 | 卡牌设计、神秘学风格 |

## 3. 仓库里另外两个"非风格" LoRA

| 文件 | 实际用途 | 别搞混 |
| --- | --- | --- |
| `krea2_style_reference`（ostris/krea2_turbo_style_reference） | 参考图风格迁移（image style reference） | 不是风格 LoRA，配合参考图工作流使用 |
| `krea2_turbo_lora_rank_64_bf16` | 蒸馏辅助用 | 不是风格 LoRA |

> ⚠️ 早期教程里出现的 `krea2_warmpastel`、`krea2_coolblue`、`krea2_plasmoid` 已于 2026 年 7 月被官方从仓库移除（LoRA 目录会轮换），以 HuggingFace `Comfy-Org/Krea-2/loras` 当前内容为准。

## 4. 社区特调模型举例（2026 年 10 月抽查）

社区基于 Krea 2 训练的第三方模型更新极快，下表仅为例证，用前请到对应平台核对更新：

| 模型 | 平台 | 特调方向 |
| --- | --- | --- |
| Gemlight Dream Midjourney Style | PromptHero | 模仿 Midjourney 的梦幻风格 |
| Krea2 Anime 2.5D-DQ 2.0 | Tensor.Art | 动漫 2.5D 角色 |
| V67 Macro V1 | comfy.icu | 微距摄影质感 |
| Dreamlike Nostalgia | Civitai | 梦幻怀旧胶片感 |
| CINEMATIC KREA 2 | Tensor.Art | 电影感写实 |

## 5. 实测使用要点（避坑清单）

1. **触发词是刚需**：文件名和触发词几乎对不上（如 softwatercolor 的触发词是 `art deco watercolor style`），加载 LoRA 后把触发词放在提示词**最前面**；
2. **提示词写内容，风格交给 LoRA**：提示词里别再加 `painterly`、`brushstrokes` 之类的风格词，会和 LoRA 打架（实测：LoRA 通常输）；
3. **Turbo 的反向提示词基本无效**：负向词写了没反应，靠正向提示词和选词来规避问题；
4. **`(word:1.3)` 加权语法失效**：Qwen3VL 按整句理解语义，旧 CLIP 时代的加权语法会整体打乱条件化；改用**语序提前、换词重复、用具体词**（"红"→"牛血红"）；
5. **LoRA 加载用 `LoraLoaderModelOnly`**，只挂模型不挂 CLIP；CLIPLoader 的 type 必须选 `krea2`，选 `flux2`/`qwen_image` 会静默出怪图；
6. **显存选择**：BF16 ≈ 24.8GiB；FP8 ≈ 12GiB（16GB 显卡可连文本编码器一起跑）；50 系可用 NVFP4，低配找社区 INT8/INT4；
7. **已知短板**：图内文字一般（英文招牌尚可、中文不行）；对已有图片的精细编辑弱（这类任务换 Nano Banana / GPT-image 系更合适）；
8. **自训 LoRA 用 Raw 训练、Turbo 使用**：Turbo 已对齐，微调吃不进去，官方 9 款就是这个流程。

## 6. 来源

- [Comfy-Org/Krea-2（HuggingFace 官方打包）](https://huggingface.co/Comfy-Org/Krea-2)
- [The 9 Krea 2 Style LoRAs: A Real Side-by-Side Comparison — ComfyLab](https://comfylab.dev/blog/guides-pro/krea-2-comfyui-style-loras-comparison/)
- [Krea2 LoRA 9종 트리거 문구 총정리 + 비교 실험 — n8n-diy](https://n8n-diy.com/2026/08/krea2-style-lora-comparison.html)
- [Krea 2 Large shines in style fidelity — Krea Blog](https://www.krea.ai/blog/krea-2-style-transfer-benchmark)

---

*回到 [README](README.md)*
