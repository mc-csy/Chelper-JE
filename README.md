# CHelper-JE

我的世界 **Java 版**命令助手 —— 边打字边给命令结构、参数说明、补全候选和错误提示。

作者：**元宝**

仿 CHelper（基岩版）的交互形态，针对 Java 版语法重新实现。

---

## 快速开始

**方式一：单文件版（推荐）**

打开 `chelper-je.html`。样式、引擎、命令数据、值域表全部内联，
不发起任何外部请求，文件管理器 / 微信 / 网盘直接打开都能用。

**方式二：分文件版**

打开 `index.html`（同目录文件必须在），或把整个目录部署到静态服务器。

```bash
# 本地预览
cd chelper-je && python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

---

## 功能

| 功能 | 说明 |
|---|---|
| 全量值域补全 | 9985 条取值，覆盖 64 类 ID（物品 / 方块 / 实体 / 魔咒 / 效果 / 声音 / 粒子 / 药水 / 结构 / 进度 / 配方 / 战利品表 / 地物 / 兴趣点 / 标签 …）全部可搜可点 |
| 中文直接查 | 「涂蜡的氧化切制铜楼梯」→ `waxed_oxidized_cut_copper_stairs`；「猪」→ `pig`；「经验修补」→ `mending`；「狂乱的鸡尾酒」→ `nether/all_potions`；「村庄」→ `village`；「死亡不掉落」→ `keep_inventory` |
| 中文别名 | 官方译名不直观的补了常用叫法：速度（官方"迅捷"）、苦力怕、小黑、回血、钻石剑 |
| 一键中文转 ID | 「中文 → ID」按钮，整条命令的中文参数一次性转成可粘贴的英文 ID |
| 数值范围校验 | `/enchant` 等级上限跟着魔咒走（锋利 ≤5、时运 ≤3、耐久 ≤3、经验修补 ≤1）；数量、效果等级、游戏规则各有范围 |
| 标签补全 | 物品 / 方块 / 实体 / 魔咒 / 生物群系 / 伤害 / 结构 标签一并给出（`#minecraft:village`、`#minecraft:wool`） |
| 语法结构 | 顶部实时显示整条命令的语法骨架 |
| 参数说明 | 显示光标所在参数的中文含义、是否必填、类型与约束 |
| 错误提示 | 未知命令、非法取值、超范围、参数过多、命令不完整、execute 未收尾等 |
| 链式解析 | `/execute` 走专用链式解析器，支持子命令任意串联 |
| 坐标识别 | 坐标参数按 3 个值处理，`~ ~ ~` / `100 64 -200` 都不会错位 |
| 语法高亮 | 命令名 / 关键字 / 选择器 / 坐标 / 数字分色显示 |
| 版本切换 | 三个 Java 版本档位，游戏规则命名随版本变化 |
| 一键复制 | 复制整条命令，直接粘贴进游戏 |

操作：点击候选项自动补全并跳到下一格；`Tab` 键补全首项。

---

## 版本档位

| 档位 | 游戏规则命名 | 说明 |
|---|---|---|
| **Java 1.21.11+**（默认） | `advance_time` / `spawn_mobs` | 小写下划线命名 |
| Java 1.21.5 – 1.21.10 | `doDaylightCycle` / `doMobSpawning` | 驼峰命名，移除 `/waypoint` |
| Java 1.20.4 及更早 | 驼峰命名 | 物品数据用 NBT 花括号 `{ }`，另移除 `/test`、`/dialog` |

> 说明：命令集以 **1.21.11 官方数据**为基准。低版本档位只做有据可查的减法（移除已知的新增命令、回退游戏规则命名），不代表该版本命令集的完整枚举。

---

## 数据来源

- **命令语法**：Mojang 官方命令树（data generator 导出），版本 1.21.11，共 **85 条命令 / 1273 条语法**
- **中文说明**：元宝整理
- **`/execute` 子命令**：官方命令树对递归命令只导出了 `if` / `unless` 分支，**缺失 `as`、`at`、`positioned`、`rotated`、`store`、`run` 等全部核心子命令**，因此这部分由 `scripts/execute_chain.py` 手工补齐 69 条，并配专用链式解析器

---

## 目录结构

```
chelper-je/
├── index.html            页面
├── css/style.css         样式
├── js/engine.js          命令解析与补全核心
├── js/app.js             界面逻辑
├── chelper-je.html       单文件版（推荐）：全部内联，零外部依赖，约 730 KB
├── data/commands.js      命令数据集（页面实际加载）
├── data/commands.json    同上，JSON 版，便于二次开发
└── scripts/
    ├── build_data.py     从官方命令树 + 注册表 + 语言文件生成中文数据集
    ├── build_registries.py  生成值域表（物品/方块/实体/魔咒/效果…）
    ├── execute_chain.py  /execute 链式子命令定义（69 条）
    ├── build_standalone.py  打包成单文件 HTML
    └── test_engine.js    引擎自测（node scripts/test_engine.js，37 项）
```

---

## 重新生成数据

```bash
# 1. 取官方命令树（Mojang data generator 导出）
curl -L -o je_commands.json \
  "https://raw.githubusercontent.com/chadgcribbins/minecraft/main/data/java-official-commands.json"

# 2. 生成中文数据集
python3 scripts/build_data.py
```

`build_data.py` 里的 `CMD_CN` / `ARG_CN` 是中文说明表，改这里即可调整翻译。

---

## 已知限制

1. **暂不支持 NBT 与物品组件解析** —— 按需求范围未实现。后续可在 `engine.js` 增加组件 / NBT 词法分析扩展。
2. **命令集基准为 1.21.11** —— 低版本档位是减法近似，不是该版本的完整枚举。
3. **`/execute` 子命令为手工维护** —— 补齐了 1.21 语法（69 条），未来版本若有新子命令需同步更新 `scripts/execute_chain.py`。
4. **谓词、函数、结构模板、对话框等"自定义内容"无官方枚举** —— 它们由数据包定义，原版没有固定列表，对应位置只提示参数类型不列值。
5. **世界生成内部数据为规则化翻译** —— `block_type`、`placed_feature`、`configured_feature`、
   `processor_list`、`structure_set`、`trial_spawner`、`map_decoration`、`stat_type` 等原版无译名，
   由 `scripts/build_registries.py` 的 `WORLDGEN_WORDS` 词典按词拼译，个别可能生硬。
6. **声音、标签、配方的中文是规则化翻译** —— 官方只给声音配了字幕、没给标签和配方配译名，
   `SOUND_WORDS` / `TAG_WORDS` / `RECIPE_SUFFIX` 按词翻译。个别可能不自然。
7. **陶罐图案、部分进度触发器、部分标签仍无中文** —— 约 600 条，只能按英文 ID 搜。
8. **中文别名表是手工维护的** —— 覆盖物品、实体、魔咒、效果、游戏规则、结构、粒子、
   药水、旗帜、战利品表的常用叫法，冷门别名可能搜不到，可编辑 `scripts/build_data.py` 的 `ALIAS`
   与 `scripts/build_registries.py` 的 `CN_*` 表补充。

---

## 许可

命令语法原始数据来自 Mojang 官方 data generator；本项目的整理、翻译与代码可自由使用与修改。
