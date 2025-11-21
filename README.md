# EGG音频分析工具 (EGG Audio Analysis Tool)

EGG 声门电图与音频分析桌面应用（PyQt6 GUI）。支持 GCI/GOI 检测、F0 曲线显示与规则校正、闭合相逆滤波（LPC 阶数可调）、底部时间轴 1 分钟视窗与滑条导航。

## 功能亮点
- 双通道输入：左通道 EGG，右通道 Audio（自动归一与滤波）
- 事件检测：GCI/GOI（峰-谷混合准则，可调峰谷显著性）
- F0 显示与校正：Praat F0 + 基于 GCI 的 F0；满足“<100 Hz 一律保留”的校正规则
- 逆滤波：简化闭合相 CP 逆滤波，支持 `IF阶数` 文本框手动设置（留空自动：`fs/1000 + 6`）
- 视图交互：底部时间轴最多显示 1 分钟；长音频用进度滑条定位视窗
- GUI 细节：中文悬浮提示、播放/停止 ROI、频谱窗口大小可调、F0 叠加坐标轴

## 环境与依赖
- Windows 10/11，Python 3.11+（推荐 3.13，与当前开发环境一致）
- 依赖：`numpy`、`scipy`、`matplotlib`、`PyQt6`、`sounddevice`、`praat-parselmouth`、`pandas`
- 一键安装：
```bash
pip install -r requirements.txt
```

## 运行
```bash
python main_app.py
```
- 加载 WAV（双声道）：左通道为 EGG、右通道为 Audio
- 设置 ROI 起始与时长，点击“应用参数并刷新”
- 逆滤波：在 “Inverse Filter” 左侧输入 `IF阶数`（正整数），或留空用自动阶数；点击按钮后导出两段音频（原始/逆滤波）

## 打包为可执行程序（含 DLL）
- 使用已提供的 `EGGAnalysisApp.spec`：
```bash
pyinstaller --noconfirm --clean EGGAnalysisApp.spec
```
- 构建完成后，分发整个文件夹：
  - `dist/EGGAnalysisApp/EGGAnalysisApp.exe`
  - 该目录包含 `_internal` 子目录的 Python 运行时与所需 DLL，避免 `[PYI-8080]` 缺失 DLL 问题
- 不建议仅分发单个 `EGGAnalysisApp.exe`；请压缩并分享整个 `EGGAnalysisApp` 文件夹或发布到 GitHub Releases

## 常见问题
- 运行脚本时报 `ImportError: DLL load failed while importing QtWidgets`：
  - 确认当前 Python 环境已安装 `PyQt6`，且未受其他环境冲突影响（建议使用虚拟环境）
- 运行打包版报缺失 DLL：
  - 请确保使用 `dist/EGGAnalysisApp` 整体文件夹运行；如果仍报错，检查是否被杀毒软件拦截或路径含有权限限制
- 大音频文件：
  - 建议用 Git LFS 管理 `.wav`，或只保留小样例，发布正式数据到 Releases

## 项目结构（关键文件）
- `main_app.py`：GUI 主程序（PyQt6）
- `inverse_filtering.py`：闭合相逆滤波实现（LPC/Yule-Walker + 预加重/去加重 + 倾斜补偿）
- `analysis_algorithms.py`：事件检测与滤波
- `gui_components.py`：Matplotlib 嵌入
- `EGGAnalysisApp.spec`：PyInstaller 打包配置（收集 DLL/资源/隐式导入）
- `requirements.txt`：依赖列表

## 安装忽略项（.gitignore 建议）
- `dist/`、`build/`、`__pycache__/`、`*.pyc`、`.venv/`、`.vscode/`、`.idea/`
- 大样例数据：如需上传，建议用 LFS：`git lfs track "*.wav"`

## 许可与致谢
- 许可：MIT（或你选择的协议）
- 致谢：本项目使用开源库 `PyQt6, NumPy, SciPy, Matplotlib, sounddevice, praat-parselmouth` 等
