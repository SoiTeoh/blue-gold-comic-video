# 彩色漫画逐笔绘制

新增 scripts/render_comic.cjs 和 assets/comic-draw.html，原 Python 渲染器保留。无需将图片重生成线稿；自动使用 Sobel 梯度、非极大值抑制和邻接路径追踪提取轮廓。纸面上逐笔画出轮廓后，以蛇形笔刷遮罩显示原图。最后完整显示缩放后的原图，不做背景改色。

## Windows 使用

安装 Node.js 与 Chrome 或 Edge，然后在项目目录执行 npm install。

```powershell
node scripts/render_comic.cjs --image "你的图片.png" --output "outputs/single.mp4" --width 540 --fps 24 --ink-sec 12 --color-sec 7 --hold-sec 2
```

默认使用本机 Chrome/Edge 和 npm 的 Windows FFmpeg 二进制。其他系统安装系统 FFmpeg 并使用 --ffmpeg 路径和 --browser 浏览器路径。

参数：--edge-threshold 默认65（降低会增加细节与噪声）；--min-stroke 默认7；--line-width 默认1.35；--brush-radius 默认28；--paper 默认#F6F1E3；--ink-color 默认#17202B；--no-hand 隐藏手；--regions 区域排序JSON。区域坐标归一化0到1，示例 [{"x":0,"y":0,"width":1,"height":0.5},{"x":0,"y":0.5,"width":1,"height":0.5}]。按照笔画中点排序，尚未实现精确的对象分割、保护区与逐元素字幕编排。

输出MP4、blank/ink/line/color/final五张阶段图和.render.json参数记录。输入保持完整比例，不裁剪；输出宽度需为偶数。视频无声。

## 当前验证

experiments/comic-handdraw/single-image.mp4：用户第二张图，540×960，24fps，21.83秒。流程为空白0.8秒→描线12秒→原色刷涂7秒→完整画面停留2秒。

自动提取轮廓可能产生双线、断线或遗漏弱对比边缘，不代表手工作者原始笔顺。手部沿轨迹叠加，使用仓库原有手部素材（带原作者文字），可以替换手素材。此阶段仅测试单图；没有批量组合旁白。

## 固定样式与批量流程（2026-10-09）

默认改为 presets/blue-gold-comic-v1.json：540×960、24fps、空白0.5秒、描线7秒、上色3.5秒、停留3秒。默认不加字幕或音频。新批量入口 scripts/render_comic_batch.cjs 支持文件名排序、横版/正方形过滤、完全重复去重、合并及解码验证。完整操作流程见 使用手册_蓝黄漫画手绘视频.md。单图以及批量无字幕端到端检查已通过。
