# Open NMR Workbench 0.1.0

这是一个核磁自动解谱软件的雏形，不包含或调用 Mnova/MestReNova 的专有代码、模型或文件格式。

## 这一版的功能

- 直接读取 Bruker 一维原始 `fid + acqus`，打开后完成窗函数、零填充、FFT、数字滤波延迟点移除、自动/手动相位、极性判断和基线校正。
- 读取 Bruker 处理后 `pdata/*/1r + procs`、两列 CSV/TSV 和未压缩 AFFN JCAMP-DX。
- 在桌面谱图中缩放、平移、手动加/删峰、框选积分和化学位移定标。
- 自动估计噪声、寻峰、把裂分线归为信号、积分，并给出 `s/d/t/q/quint/sext/sept/dd/m`、J 值、位移区域和逐信号置信度。
- 根据氘代溶剂匹配残余溶剂、水和常见实验室杂质。内置值用于筛查，数据来源与边界写在 `reference_shifts.json` 中。
- 根据本次提供的 Fulmer 正文与补充资料扩展为 12 种氘代溶剂：`THF-d8`、`CD2Cl2`、`CDCl3`、`toluene-d8`、`C6D6`、`C6D5Cl`、`acetone-d6`、`DMSO-d6`、`CD3CN`、`TFE-d3`、`CD3OD`、`D2O`；保留 `1H`/`13C` 残峰与水峰数据。
- 从信号组合提出可追溯的结构片段提示，例如同 J 的 `t/q` 及积分关系支持乙基，而不是只给一个黑箱标签。
- 将实验谱与用户候选结构库中的预期信号进行全局匹配，综合位移、覆盖率、积分、多重性和分子式，生成 0–100 的一致性排序。
- 导出自包含 HTML 报告（带矢量谱图）或 JSON 数据，便于组会、实验记录和后续程序处理。
- 打开独立二维结构画板，以类似常见化学结构编辑器的方式绘制单/双/三/芳香键、苯环/六元环、常见有机元素与形式电荷，并支持选择、移动、删除、撤销和重做。
- 编辑停止约半秒后自动预测 `1H`、`13C`、`31P` 或 `19F` 一维谱，同步显示反向 ppm 谱图、原子定位峰表、积分/碳类型、近似裂分/J 值、环境说明、置信度和经验不确定度。
- 可把画板结构的预测信号与主窗口实验谱一对一全局配对，显示预测/实验覆盖率、位移 RMSE、缺失预测峰和未解释实验峰。
- 画板结构可保存为可继续编辑的 `.onmrstruct`，并通过 RDKit 导入 SMILES/MOL/SDF、导出 MOL/规范 SMILES，预测峰表可导出 CSV 或 JSON。
- 主界面、画板、账号和数据库窗口支持简体中文/English 即时切换，Matplotlib 会优先选择系统 CJK 字体，修复欢迎页和谱图中的方框乱码。
- 主窗口加入有界撤销/重做栈，可恢复载入、处理、定标、自动解谱、手动峰和积分；`Esc` 只取消当前手动工具，无需关闭软件。
- 数据读取与预测后端采用插件协议。当前内置 Bruker、JCAMP-DX 和分隔文本读取器，后续 JEOL/Agilent/Varian、二维 NMR 或质谱读取器可作为独立入口安装。
- 数据库关联窗口内置 PubChem、nmrshiftdb2、SDBS、HMDB、BMRB、ChemSpider、Reaxys 与 CAS SciFinder，并允许添加自建数据库 HTTPS 检索模板，订阅库仅打开合法入口，不保存密码或绕过许可。（具体的可以自由发挥）。
- 提供base内部测试用本地账号雏形：SQLite、NFKC 用户名、带独立盐的 scrypt 密码散列、登录锁定、散列会话令牌、过期/撤销和不记录密码的审计事件。（此功能主要是我用的，基地内网。成熟后会酌情扩大范围）。
- 谱图处理、预测与自动解谱核心仍在本机执行，只有用户主动点击数据库检索时才会在默认浏览器打开相应网址。

## Windows 使用（目前只有Windows哦）

支持 64 位 Python 3.10–3.14，并优先使用 Python 3.14。本版针对 Python 3.14.6 配置了 NumPy、SciPy、Matplotlib 和 RDKit 的 Windows 二进制轮子。后面升级时请使用新的空文件夹，不要复制旧版的 `.venv`；然后：

1. 双击 `install_windows.bat`，它会创建或检查隔离的 `.venv` 环境、自动补装缺失的 `pip`，并安装依赖。
2. 双击 `run_windows.bat` 启动软件。
3. 第一次可点击“演示谱”，确认自动解谱结果中乙酸乙酯位于首位。

如果你已经安装 Python 3.14.6，则无需重复安装。0.1.0 会识别新版 Python Install Manager、旧版 `py`、`python3.14`/`python` 命令、WindowsApps 应用执行别名、PEP 514 注册表项以及用户/系统常见安装目录，并优先选择 3.14。若只安装了 Python Install Manager、运行时尚未安装，或注册/别名失效，安装器会自动执行官方的运行时注册刷新。仍无可用运行时时，再请求安装标准 Python 3.14，然后继续创建 `.venv`。检测与修复明细写入同目录的 `python_detection.log`。

若已有 `.venv` 仅缺少 `pip`，安装器会给原地修复。若因移动文件夹或其它原因无法启动，则会把它保留为 `.venv-incomplete-日期时间` 后重建，不用手动删除。安装和启动始终使用相对于脚本位置解析出的绝对路径，不依赖当前盘符或 `activate.bat`。安装明细写入 `install_log.txt`，安装错误写入 `install_error.txt`，启动错误写入 `run_error.txt`。

若自动修复也失败，可先在命令提示符中运行 `pymanager install --refresh`、`pymanager install 3.14` 和 `pymanager list --format=exe`；如果 `pymanager` 命令不可用，请在 Windows“管理应用执行别名”中启用“Python install manager”，关闭旧命令提示符后重新打开。也可以把真实 `python.exe` 的完整路径写入软件目录中的 `python_path.txt` 第一行。[Python 3.14 Windows 官方说明](https://docs.python.org/3.14/using/windows.html)~~

也可在 PowerShell 或命令提示符中执行：

```powershell
py -3.14 -m venv .venv
.venv\Scripts\python.exe -m ensurepip --upgrade
.venv\Scripts\python.exe -m pip install -e .
.venv\Scripts\python.exe -m nmr_workbench gui
```

界面语言在“语言”菜单切换，设置保存在当前用户目录；切换时保留已载入谱图和分析结果。主窗口快捷键为 `Ctrl+Z` 撤销、`Ctrl+Y` 重做、`Esc` 取消当前峰/积分工具。

如果希望生成无需源码启动的 Windows 程序，在同一文件夹双击 `build_windows_exe.bat`。完成后入口位于 `dist\OpenNMRWorkbench\OpenNMRWorkbench.exe`。PyInstaller 会连同 Python 运行时与科学计算依赖一起打包，所以文件夹体积会明显大于源码。

## 结构画板与核磁预测

从主界面点击工具栏“结构画板 / NMR 预测，或进入分析 → 结构画板与 NMR 预测”。画板的常用操作如下：

1. 选择“单键”，在空白处单击放置第一个碳；从顶点向外拖动可连续生成碳骨架，拖到已有顶点则成环/成键。
2. 用“单键/双键/三键/芳香键”修改键级，苯环和六元环可直接放置模板。
3. 选择 `N/O/F/P/S/Cl/Br/I/H` 后单击顶点替换元素；先选中原子再用“电荷 +/−”设置形式电荷。
4. 右侧选择 `1H`、`13C`、`31P` 或 `19F`。软件会自动校验常见价态，并在编辑停止后刷新谱图和峰表。点击峰表行可在画板高亮对应原子。
5. “示例”菜单内置乙醇、氟苯和磷酸三甲酯，可分别快速检查 `1H/13C`、`19F` 与 `31P` 工作流。

画板本身不依赖网络。RDKit 用于标准 SMILES/MOL/SDF 解析、芳香性/价态清洗和规范 SMILES 输出；手动画板、项目 JSON 和经验预测核心仍在本地独立运行。RDKit 2026.3.4 已提供 CPython 3.14 的 Windows x86-64 轮子，安装脚本会要求二进制包，避免在用户电脑上编译。

### 预测结果应如何解读

这一版采用可检查的官能团与邻域增量规则，而不是把宽泛的“AI 预测”当作确定答案：

- `1H` 根据杂原子/卤素、羰基 α 位、苄位、烯丙位、芳香/烯烃/醛等环境估算位移，并从相邻非等价 H 给出近似 `s/d/t/q/...` 和 J。OH/NH/SH 标为交换性宽峰。
- `13C` 区分醛/酮/酸/酯/酰胺羰基、腈、芳香、烯/炔和脂肪碳；峰表中的 `CH3/CH2/CH/Cq` 是宽带去耦谱下的碳类型提示。
- `19F` 覆盖芳基 F、脂肪族 CF/CF2/CF3、酰氟和 P–F/S–F 的宽范围规则。
- `31P` 覆盖磷酸酯/磷酸盐、膦酸/次膦酸衍生物、氧化膦、亚磷酸酯、芳基/烷基膦及含 P–F 的常见环境。

每个信号都给出 `±ppm` 和置信度。它们是经验适用范围提醒，不是统计学置信区间。溶剂、温度、浓度、pH、构象、氢键、交换、互变异构、手性/非对映异位、金属配位及超价结构均可造成更大偏差。复杂结构应使用实验谱、标准品或经验证的数据库/量化/机器学习预测器复核。

## 打开真实 Bruker 数据

对于一维原始数据，选择包含下列内容的实验目录：

```text
sample/1/
├── acqus
├── fid
└── pdata/
    └── 1/
        ├── procs
        └── 1r
```

点击“打开 Bruker 目录”时，若顶层同时存在 `fid`，软件优先读取原始时域数据；如只想读取处理后的数据，可直接选择 `1r` 文件。加载后请检查左侧核种、频率和氘代溶剂是否正确，再运行自动解谱。

对于 CSV/TSV，前两列应为 `ppm,intensity`，有无表头均可。ppm 升序或降序均可，载入时会统一为 NMR 常用的高场向右显示。

## 自动解谱的含义

本软件区分两个科学问题：

1. 未知结构分析：从实验信号提出位移区域、积分、多重性、J 值和局部片段。这些是约束，不足以从任意一张一维谱唯一重建复杂分子。
2. 候选结构验证：当你已经有一个或多个合理候选结构时，将候选的预期信号与实验信号匹配并排序。这与合成中“目标物是否正确”的实际问题更接近，也比无约束猜结构可靠。

因此，候选得分表示“当前实验谱支持该候选的程度”，不是结构鉴定证明。复杂、重叠、动态交换、互变异构、顺磁或多组分体系，仍需结合 13C、DEPT、COSY、HSQC、HMBC、NOE/ROE、DOSY、HRMS 以及合成背景复核。

提高准确率的实验与数据工作建议见 `docs/ACCURACY_GUIDE_zh-CN.md`。其中最重要的不是继续堆一维规则，而是统一采集条件、保留原始 FID/元数据、加入 13C/DEPT/COSY/HSQC/HMBC 约束，并用本单位人工复核的真实谱图持续建立外部盲测集和内部参考库。

## 数据库、账号与版本边界

“数据库 → 数据库关联”可启用/停用预置入口，也可新增自建库。检索 URL 模板允许 `{query}`、`{name}`、`{formula}`、`{smiles}`、`{inchi_key}`、`{cas}`值会进行 URL 编码。外部地址必须使用 HTTPS，只有明确勾选后才允许 `localhost` 或私有局域网地址使用 HTTP。软件不在数据库 URL 中接受用户名/密码。

“账号 → 登录/注册”当前仅用于我的基地内部单机流程验证，不是互联网多用户服务，账号文件保存在当前 Windows 用户的应用数据目录，首个账号是本猫。上线前必须改用服务端认证、TLS、MFA/恢复、RBAC、密钥管理、集中审计、备份与安全更新，详见 `docs/SECURITY_AND_COMMERCIALIZATION.md`。

当前社区内部预览版全部功能免费并可从源码编译。已经发布的 MIT 源码不能在未来被远程变成“不可编译”，后续如果有其它计划另说。

## 候选结构库格式

软件接受 JSON，或带 JSON `signals` 列的 CSV/TSV。JSON 推荐格式如下：

```json
[
  {
    "name": "目标化合物 A",
    "formula": "C10H12O2",
    "smiles": "CCOC(=O)c1ccccc1",
    "signals": [
      {"shift": 8.02, "multiplicity": "m", "protons": 2, "tolerance": 0.25},
      {"shift": 7.43, "multiplicity": "m", "protons": 3, "tolerance": 0.30},
      {"shift": 4.36, "multiplicity": "q", "protons": 2, "tolerance": 0.15},
      {"shift": 1.38, "multiplicity": "t", "protons": 3, "tolerance": 0.15}
    ]
  }
]
```

`tolerance` 是该信号允许的位移误差（ppm）。它应综合考虑溶剂、温度、浓度、构象和预测方法误差，不要为提高得分而无依据地设得过宽。`examples/candidate_library_template.json` 可直接复制修改。

## 命令行与批处理入口

图形界面和命令行使用同一分析内核：

```powershell
# 内置演示并生成 HTML
nmr-workbench demo -o demo_report.html

# 分析 Bruker 实验目录
nmr-workbench analyze D:\NMR\sample\1 --solvent CDCl3 --formula C18H22O3 -o report.html

# 使用自己的候选库
nmr-workbench analyze spectrum.csv --frequency 400.13 --candidates candidates.json -o report.json

# 从 SMILES 预测峰表（需安装 RDKit）
nmr-workbench predict "CCO" --nucleus 1H --solvent CDCl3 --frequency 400 -o ethanol_1H.csv
nmr-workbench predict "Fc1ccccc1" --nucleus 19F --frequency 376.5 -o fluorobenzene_19F.json
nmr-workbench predict "CCO" --nucleus 1H --solvent DMSO-d6 --temperature 298.15 --concentration 0.05
```

运行 `nmr-workbench analyze -h` 可查看相位、基线、零填充和 S/N 等参数。

## 算法概要

- 自动相位：多起点、带界的熵/负峰面积目标函数；负峰面积是主要物理约束，并对无证据的大一阶相位加轻度正则化。
- 基线：默认采用非对称惩罚最小二乘（AsLS），超长谱图先在稀疏网格求解再插值，以控制内存和时间，另提供迭代多项式基线。
- 寻峰与归组：Savitzky–Golay 轻度平滑后按稳健噪声估计设置 prominence 阈值，相邻裂分线按 Hz 而非固定 ppm 归组。
- 积分：对基线校正后的正信号积分，相邻窗口在中点截断，分子式或总氢数存在时才换算为估算氢数。
- 候选匹配：匈牙利算法完成实验/预期信号的一对一全局匹配，避免多个预期峰反复占用同一实验峰。
- 片段规则：使用可检查的化学位移、多重性、J 值与积分关系，所有提示都携带证据和信号索引。
- 分子图与结构互换：画板使用稳定的本地原子/键图完成撤销、保存和预测；边界处通过 RDKit 做 SMILES/MOL 解析、芳香性与常见价态清洗。
- 结构预测：按原子四层拓扑环境合并严格等价位点，再应用官能团/邻域增量规则。模拟谱仅用于显示，用 Lorentzian 线形和近似 Pascal 裂分生成。

## 测试

开发测试只依赖 Python 标准库 `unittest`：

```powershell
python -m unittest discover -s tests -v
```

当前回归集共 43 项（Python 3.14 专用导入测试在非 3.14 验证机上条件跳过）。覆盖分子式、Bruker 参数/二进制读取、FFT 与自动相位、演示谱寻峰/积分/片段识别、候选排序、CSV/HTML 报告、分子图与四核预测、预测—实验谱配对、12 种溶剂数据、双语资源、撤销/重做、插件协议、数据库 URL 安全和本地账号/会话安全。

## 当前边界与下一步

0.1.0 是可运行的一维 NMR 工作台与结构预测/验证预览版，距离 Mnova/ChemDraw 还很远。很明显的差距：

- Agilent/Varian、JEOL 等读取插件及所有 Bruker 数字滤波变体的完整兼容；
- 通用 SQZ/DIF/DUP 压缩 JCAMP 解码；
- 2D COSY/HSQC/HMBC/NOESY 处理、交叉峰关联与自动指认；
- 经过大型实验数据库或量化计算校准的高精度 `1H/13C/31P/19F` 位移模型；
- 楔形键、E/Z、R/S、构象/配位几何及手性环境下非对映异位 H 的完整编辑与预测；
- 对严重重叠峰的全局谱线去卷积和机器学习峰拾取；
- 无候选前提下的任意复杂未知物唯一结构生成；
- 可直接暴露在公网的生产级账号服务、同步、团队权限、计费与授权系统。

下一阶段计划：先加入 13C/DEPT 与 HSQC/COSY，使质子和碳形成关联图。再为画板接入可审计、带版本的数据驱动预测后端及用户自己的标准品数据库。最后用人工确认的数据训练峰拾取/去卷积模型。这样自动化能力建立在可复核的二维证据上，而不是放大一维谱的歧义。

模块边界、第三方读取器/预测器接口及未来质谱扩展路径见 `docs/ARCHITECTURE.md`。

## 参考数据

内置残余溶剂/杂质位移基于以下经典文献整理，并保留了随条件变化的容差：

- Gottlieb, H. E.; Kotlyar, V.; Nudelman, A. *J. Org. Chem.* **1997**, 62, 7512–7515. DOI: `10.1021/jo971176v`.
- Fulmer, G. R. et al. *Organometallics* **2010**, 29, 2176–2179. DOI: `10.1021/om100106e`.

结构互换使用开源 [RDKit](https://www.rdkit.org/)；Python 发布包与当前平台轮子见 [RDKit on PyPI](https://pypi.org/project/rdkit/)。第三方依赖许可提示见 `THIRD_PARTY_NOTICES.md`。

## 许可证

MIT。详见 `LICENSE`。


## 一些补充

我们同时也在自建数据库，本地部署大模型，数据源包括公开文献资料和基地内部的技术文件。科学无国界，这是个有意义的工作，期待大家一起带软件升级打怪呀，感谢你们的支持~
