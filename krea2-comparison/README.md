# Krea 2 主模型与特调模型效果对比

> Krea 2 开源模型家族（Raw / Turbo / Large 主模型 + 官方 9 款特调 LoRA + 社区特调模型）的效果对比速查。
> 信息截至 2026 年 10 月，依据官方仓库与第三方实测评测（见文末来源）。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## 快速导航

- 📊 完整对比表：[Krea2主模型与特调模型效果对比.md](Krea2主模型与特调模型效果对比.md)
- 📌 一句话结论：**特调 LoRA 必须加触发词**，9 款里 7 款不加触发词会退化成雷同的"通用插画风"；主模型里 Turbo 适合日常出图（8 步 2K），Raw 适合自训 LoRA，Large 适合风格迁移。

## 核心对比速览

### 主模型

| 模型 | 定位 | 速度 | 适用场景 |
| --- | --- | --- | --- |
| Krea-2-Raw | 未蒸馏基线 | 慢 | 自训 LoRA、极致画质 |
| Krea-2-Turbo | 蒸馏加速 | 8 步出 2K | 日常出图、快速试稿 |
| Krea 2 Large | 线上旗舰 | — | 风格迁移（参考图定调） |

### 官方 9 款特调 LoRA

| LoRA | 触发词 | 风格效果 |
| --- | --- | --- |
| `krea2_darkbrush` | `monochrome ink wash style` | 单色水墨、写意笔触 |
| `krea2_dotmatrix` | `monochrome stippling style` | 黑白点画、印刷感 |
| `krea2_kidsdrawing` | `naive expressive sketch style` | 童趣速写、粗线条 |
| `krea2_neondrip` | `textured abstract style` | 抽象油画、高饱和 |
| `krea2_rainywindow` | `rainy window style` | 玻璃雨痕叠加 |
| `krea2_retroanime` | `purple retro anime style` | 80/90s 动漫、紫调 |
| `krea2_softwatercolor` | `art deco watercolor style` | 水彩 + 装饰艺术线条 |
| `krea2_sunsetblur` | `ethereal motion blur style` | 梦幻动态模糊、暖调 |
| `krea2_vintagetarot` | `vintage tarot style` | 复古塔罗牌插画 |

## 使用要点（避坑）

1. 加载 LoRA 后把触发词放在提示词**最前面**，否则大部分 LoRA 不出效果；
2. 提示词只写内容，风格交给 LoRA（风格词会与 LoRA 冲突）；
3. Turbo 的反向提示词基本失效，靠正向选词规避；
4. `(word:1.3)` 加权语法失效（Qwen3VL 按整句理解），改用语序提前、换词重复、具体词汇；
5. LoRA 用 `LoraLoaderModelOnly` 加载，CLIPLoader 的 type 选 `krea2`；
6. 显存：FP8 ≈ 12GiB（16GB 卡可跑），BF16 ≈ 25GiB，低配用社区 INT8/INT4。

## 来源与致谢

- [Comfy-Org/Krea-2（HuggingFace 官方打包）](https://huggingface.co/Comfy-Org/Krea-2)
- [The 9 Krea 2 Style LoRAs: A Real Side-by-Side Comparison — ComfyLab](https://comfylab.dev/blog/guides-pro/krea-2-comfyui-style-loras-comparison/)
- [Krea2 LoRA 9종 트리거 문구 총정리 + 비교 실험 — n8n-diy](https://n8n-diy.com/2026/08/krea2-style-lora-comparison.html)
- [Krea 2 Large shines in style fidelity — Krea Blog](https://www.krea.ai/blog/krea-2-style-transfer-benchmark)

## License

[MIT](LICENSE) © 2026 <你的姓名>
