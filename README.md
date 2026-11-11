# reading-helper · 英语深度阅读理解导师

一个面向英语精读场景的 Agent Skill：粘贴一段英文原文，按固定结构一次输出**学习概览、三步层级阅读引导、逐段核心语言点、阅读理解题、质量自检**。讲解用中文，题目与原文引用用英文；不分批、不追问、不加鼓励语。

## 项目解决什么问题

英语精读最常见的困境不是「查不到词义」，而是缺少一条从读懂到会做的路径：

- **只有译文，没有结构**：整篇翻译看完仍说不清段落怎么推进、作者态度从哪个词看出来。本 Skill 不复述全文、不逐句翻译，只拆段落逻辑、信号词、关键句与长难句。
- **生词太多，分不清主次**：逐词查词典会淹没重点。本 Skill 每段只给 3–6 条真正影响理解的词条（短段落 2–3 条，含熟词僻义、逻辑词、主题术语），每条带搭配与原文依据。
- **长难句靠语感硬猜**：本 Skill 固定按「主干 → 修饰成分 → 逻辑关系 → 参考翻译」四步拆，结构拿不准时标「结构存疑」，不硬拆。
- **精读与刷题脱节**：题目全部依托原文，词义题直接考刚讲过的词条；每题给原文定位与逐项解析，不用常识替代原文。
- **每次输出格式都不一样**：五段结构与字段固定，末尾附质量自检，便于对照和复盘。

适用于课文精读、时文拆解、四六级／专四／考研阅读训练，也可作为精读课的讲解骨架。

## 主要功能

| 模块 | 输出内容 | 关键规则 |
| --- | --- | --- |
| 一、学习概览 | 体裁、话题、总词数、建议难度、学习目标、题量说明 | 六字段固定；难度按平均句长、从句密度、词汇、衔接四维判定并写出可核依据 |
| 二、三步层级引导 | 第一步整体感知（主旨／结构／写作目的＋2–4 个引导问题）；第二步段落精读（段主旨／句间逻辑／信号词／关键句＋1–3 个精读问题）；第三步深度理解（推理／作者态度／写作手法＋2–4 个深度问题） | 三步独立成节，不合并、不跳步，问题均只提问不给答案；段落编号与原文一致，关键句 ≤20 词；态度须给用词证据，推理须写明依据位置 |
| 三、逐段核心语言点 | 段落主旨＋词汇表＋语法点＋长难句拆解 | 词汇表固定五列「单词｜词性｜语境释义｜搭配｜原文依据」，依据 ≤12 词并加粗原词；语法点写「名称／功能／易错提醒」；长难句四步拆解，无则注明 |
| 四、阅读题目实战 | 题干、A–D 选项、答案、原文定位、解析 | 默认 5 题；1–8 题按固定配比，超过 8 题按 8 题配比循环；每题四选项、答案唯一；定位精确到段与句；解析逐项说明干扰项错在哪 |
| 五、质量自检 | 4 项勾选＋违规项 | 逐段覆盖、长难句拆解、出题合规；有不合格如实写入「违规项」 |

### 参数

| 参数 | 示例说法 | 默认 | 行为 |
| --- | --- | --- | --- |
| 难度 | 「按 B2」「简单点」「考研难度」 | 自动判定 | 指定等级直接采用；口语说法映射到讲解深度与题型倾向 |
| 学习目标 | 「备考专四」「积累写作表达」 | 综合精读 | 只调整讲解侧重与题型配比，不改五段结构 |
| 题量 | 「出 3 题」「来 8 题」 | 5 题 | 按 N 取题型配比；原文撑不住时按可支撑题数出题并注明 |
| 题型偏好 | 「多出推断题」「只要主旨和细节」 | 题型均衡 | 在总题量内替换非偏好题型，主旨题至多 1 题 |

## 安装方法

Skill 本体是 `reading-helper/` 文件夹，整个复制到所用 Agent 的 skills 目录即可：

```text
reading-helper/                # 仓库根目录
├── README.md                  # 本说明
├── LICENSE                    # MIT 许可
└── reading-helper/            # Skill 本体
    ├── SKILL.md               # 规则与模板（唯一必需文件）
    └── agents/
        └── openai.yaml        # Agent 展示信息
```

以 Codex 风格的 `~/.codex/skills` 为例，在仓库根目录执行：

```powershell
$target = Join-Path $env:USERPROFILE ".codex\skills\reading-helper"
New-Item -ItemType Directory -Force (Split-Path $target) | Out-Null
Copy-Item -LiteralPath ".\reading-helper" -Destination $target -Recurse
```

复制完成后应得到 `~/.codex/skills/reading-helper/SKILL.md` 与 `~/.codex/skills/reading-helper/agents/openai.yaml`。其他平台把目标路径换成该平台的 skills 根目录即可；`agents/openai.yaml` 只提供展示名称与默认提示词，缺失不影响规则生效。

若所用平台没有 skills 目录机制，也可把 `SKILL.md` 正文作为该 Agent 的固定指令或系统提示使用；这种方式没有元数据自动触发，需要手动启用。

## 使用方法

直接粘贴英文原文即可触发，不需要写指令词；原文至少 2 句连续英文。

```text
Many managers still believe that longer hours mean more output. Yet a growing body of research, much of it carried out in offices where overtime is routine, suggests that the gain in the sixth and seventh hours of work is so slight, and the errors accumulated so costly, that extending the day may leave a team worse off.
```

带参数时把要求写在同一条消息里，例如：

- `按 B2 精读这篇文章`
- `出 8 题，多出推断题`
- `按考研难度，重点积累写作表达`

行为说明：

- **短文**：原文不足 60 词时题量降为 3，难度标注「（参考）」。
- **长文**：超过 8 段或 600 词时进入压缩模式，段落精读每段压到三行，语言点仍逐段提取。
- **追问**：解析完成后可就某一段、某一句、某一题追问，只答该点，不重跑整套。
- **改格式**：要求换总表、只要题目、改英文输出或删模块时，会先说明「按固定结构输出」，随后仍完整交付五段。

## 输入输出示例

输入（58 词，1 段 2 句）：

> Many managers still believe that longer hours mean more output. Yet a growing body of research, much of it carried out in offices where overtime is routine, suggests that the gain in the sixth and seventh hours of work is so slight, and the errors accumulated so costly, that extending the day may leave a team worse off.

输出（节选，省略第二步、第三步的逐段内容与部分题目）：

```text
## 一、学习概览
- 体裁：议论文（职场评论）
- 话题：延长工时是否带来更多产出
- 总词数：58
- 建议难度：C1（参考：2 句，平均句长 29 词，最长句 48 词；多重从句与插入语密集，篇幅过短，判定仅供参考）
- 学习目标：综合精读
- 题量说明：3 题：主旨 1、细节 1、推断 1（原文不足 60 词，题量按可支撑范围调整）

## 二、三步层级阅读引导
### 第一步：整体感知层
### 第二步：段落精读层
### 第三步：深度理解层

## 三、逐段核心语言点提取
### Paragraph 1
#### 段落主旨
管理者仍相信工时会线性换来产出，而研究显示第 6、7 小时的产出增量极小、错误代价高。

#### 词汇短语
| 单词 | 词性 | 语境释义 | 搭配 | 原文依据 |
| --- | --- | --- | --- | --- |
| body | n. | 大量（研究成果） | a growing body of research | a growing **body** of research |
| routine | adj. | 司空见惯的 | be routine in… | offices where overtime is **routine** |
| slight | adj. | 微乎其微 | a slight gain | the gain … is so **slight** |

#### 语法点
- 名称：双 so…that 结果结构（so slight … and so costly … that …）。
- 功能：把「产出增量极小」推到「延长工时反而更糟」的结论上。
- 易错提醒：and 连接第二个主语 the errors，其谓语 are 被省略；不要把 and 读成转折，也不要看成两个独立句子。

#### 长难句拆解
1. 主干：a growing body of research suggests that…（主语 research，谓语 suggests，宾语为 that 从句）。
2. 修饰成分：`much of it carried out in offices` 为独立主格插入语，补充研究来源；`where overtime is routine` 是定语从句，修饰 offices；`in the sixth and seventh hours of work` 修饰 gain；`accumulated` 为过去分词后置，修饰 errors。
3. 逻辑关系：`Yet` 与上句构成转折；`so…that` 表结果；`and` 并列两个主语。
4. 参考翻译：然而，越来越多的研究——其中不少是在加班成常态的办公室里完成的——表明，工作第 6、7 个小时带来的产出增量微乎其微，而累积的错误代价高昂，以致延长工时反而可能让团队处境更差。

## 四、阅读题目实战
### 第 1 题（主旨）
What is the main claim of the passage?

A. Longer hours always raise output in proportion to time.
B. Extra hours add little output but much costly error.
C. Offices with routine overtime are the most productive.
D. Managers should measure output by the hour.

答案：B

原文定位：Paragraph 1 · 第 2 句 · `the gain … is so slight, and the errors accumulated so costly`

解析：原文用 so…that 说明产出增量极小、错误代价高，B 是这一因果的准确概括。A 与「gain is so slight」相反；C 把研究场景当成结论，原文只说这类办公室是研究对象；D 原文未提计酬方式。

## 五、质量自检
- [x] 三步引导完整：整体感知 → 段落精读 → 深度理解，未跳步、未合并
- [x] 逐段提取：共 1 段全覆盖，词汇／语法／长难句均有原文依据
- [x] 长难句：主干、修饰、逻辑、译文一致（本段 1 句已拆解）
- [x] 出题合规：3 题、每题四选项、答案唯一、均附原文定位与逐项解析
- 违规项：无
```

## 不适用范围

- 只处理粘贴的英文文本；不支持网页抓取、PDF 解析、图片识别与图形界面操作。
- 不做整篇翻译、词汇背诵表、作文批改与系统语法课；这些请求按固定话术回复。
- 原文涉及争议话题时只拆论证与语言，不评价立场、不补原文之外的背景知识。

## 已验证

- `quick_validate.py` 校验通过，frontmatter 与命名符合 Skill 规范。
- 实测 4 篇不同题材：议论文 210 词、科普说明文 164 词、记叙短文 37 词、教育政策评论 246 词；含参数分支（C1、4 题、推断优先）。结构、题量与格式均按 `SKILL.md` 规则输出。

## 许可

本项目以 [MIT 许可](LICENSE) 发布：可自由使用、修改、再分发（含商业用途），保留版权与许可声明即可。
