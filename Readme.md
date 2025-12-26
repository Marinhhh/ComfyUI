# Wan 2.1/2.2 视频角色替换工作流 (多人视频)

本工作流是一款专为 ComfyUI 设计的多人角色视频处理方案，旨在实现**视频中特定角色的无缝替换**。即使在原视频包含两个或多个人的情况下，也能通过精准的视频分割与重绘技术，将其中一个目标替换为全新形象，同时可以完美继承原视频的动作（Pose）和场景背景。

---

### 🌟 工作流功能

* **双参考图机制**：利用 **Qwen-Image-Edit (Qwen 2.5 VL)** 针对原视频首帧进行重绘，生成一张全新的角色参考图。这样用户将拥有“原图”与“生成图”两个参考源，可根据清晰度和质量在 `WanAnimateToVideo` 节点中灵活选择最佳输入。
* **精准目标锁定**：通过 **SAM2 (Segment Anything 2.1)** 实现动态视频遮罩，支持从多人画面中精准剥离出需要替换的目标。
* **次世代视频生成**：采用 **Wan 2.2 Animate 14B (GGUF)** 模型，结合姿态控制（DWPose），生成光影自然、动作连贯的替换视频。
* **显存平衡优化**：工作流深度集成了显存清理节点，支持在 24G 显存环境下流畅运行复杂的 14B 级别模型。

---

### 📥 模型下载清单

请将下载的模型放置在 ComfyUI 根目录下的对应文件夹内：

| 模型类型 | 推荐文件名 | 放置目录 |
| --- | --- | --- |
| **视频生成模型** | `Wan2.2-Animate-14B-Q8_0.gguf` | `models/diffusion_models` |
| **图像编辑模型** | `qwen_image_edit_2509_fp8_e4m3fn.safetensors` | `models/diffusion_models` |
| **视频 VAE** | `wan_2.1_vae.safetensors` | `models/vae` |
| **图像 VAE** | `qwen_image_vae.safetensors` | `models/vae` |
| **T5 文本编码器** | `umt5_xxl_fp8_e4m3fn_scaled.safetensors` | `models/clip` |
| **Qwen 文本编码器** | `qwen_2.5_vl_7b_fp8_scaled.safetensors` | `models/clip` |
| **角色编辑 LoRA** | `Qwen-Image-Edit-2509-Lightning-4steps-V1.0-bf16.safetensors` | `models/loras` |
| **分割模型** | `sam2.1_hiera_large.safetensors` | `models/sam2` |
| **视觉编码器** | `clip_vision_h.safetensors` | `models/clip_vision` |

---

### 🛠️ 环境依赖

请确保已通过 ComfyUI Manager 安装以下自定义插件：

* `ComfyUI-WanVideoWrapper`
* `ComfyUI-VideoHelperSuite`
* `comfyui-kjnodes`
* `ComfyUI-segment-anything-2`
* `comfyui-gguf`
* `comfyui_layerstyle`
* `dwposedeluxe`

---

### 🚀 快速使用指南

1. **启动程序**：打开终端或命令行，导航到 `ComfyUI` 所在的目录，`python main.py`运行启动脚本。
2. **导入工作流**：将下载的 `CharacterSwap.json` 文件直接拖入 ComfyUI 网页界面。
3. **上传视频**：在左侧的 `LoadReferenceVideo` 节点上传你需要修改的视频素材。
4. **设定选区**：
* 找到 `PointsEditor` 节点，点击预览图打开编辑器。
* 在视频首帧上，**左键点击（绿点）** 选中需要替换的人物，**右键点击（红点）** 排除不需要的部分。


5. **生成参考图**：
* 在 `TextEncodeQwenImageEditPlus` 中输入重绘参考图的描述词。
* 运行工作流，Qwen 节点会首先生成一张角色的重绘参考图。


6. **对比与采样**：

* 检查生成的参考图（Ref Image 2）与原图（Ref Image 1）。
* 在采样器输入端，选择质量更高、细节更清晰的一张作为 Wan 视频生成的参考源。
  
7. **最终渲染**：点击 **Run**，等待渲染完成。

---

### ⚠️ 运行说明

* **显存要求**：推荐使用 **RTX 3090/4090 (24GB VRAM)**。如果显存较低，请尝试降低视频分辨率或减少生成帧数。
* **分辨率设置**：默认工作流输出比例由 `ImageResizeKJv2` 节点控制（通常设为 832x480），请确保长宽比与原视频一致。

