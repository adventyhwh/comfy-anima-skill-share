---
name: anima-prompt
version: fourth-share
description: Anima 模型（circlestone-labs/Anima）提示词层的权威主线。把模糊输入转成 Anima 优化的 Danbooru-tag prompt（模板 A/B/C），含三条核心公理、负面意图预测、人物一致性、道具/视线/多角色/多人纪律、画师策略、ComfyUI 参数与生成流程。Route anime/illustration/characters → Anima；realism/photos → 不在本套件范围。长篇 doujin（>10p）构思层走 anima-doujin-plan（本文件为提示词层权威主线）。NSFW 解构方法论走 anima-nsfw-prompt（不另起模板）。
---

# 定位与激活

## When to Activate
用户要**生成美术资源**且主体是 anime / illustration / character / 2D art / game art（非写实）时激活；用户点名 anima / Anima / circlestone-labs/Anima 时激活。**路由**：Anima ← 动漫/插画/角色/2D；写实/照片/风景/室内 → 不在本套件范围（如有对应写实 skill 则转交）。含糊 + 角色/动漫 → Anima。不处理视频/3D 模型/纯文字。
**协作边界**：纯场景/背景（无主角）→ anima-scene-prompt；NSFW 解构 → anima-nsfw-prompt；长篇 doujin（>10p）→ anima-doujin-plan（它管"画什么"分幕/事件链/波折/镜头/节奏，产出 12 列分镜表；本 skill 管"怎么写"，把分镜表逐页转 prompt）。doujin 不抢模板层，本 skill 不越权构思层。

# 核心公理（所有写作纪律的根，全部 skill 通用）

以下三条公理是 Anima 行为模型的本质，本文所有"禁止/必须"都是它们的推论。写任何 prompt 前先想公理。

**公理 1 · 组装式模型（拼尸块）**：写了什么就有对应零件，但零件间不有机融合——动作/表情/道具是"摆放"不是"生长"。推论：①每帧只给 **1 个主导动作**，同帧零件数压到最低（次要部位降级或省略）；②抽象状态一律用具象物理状态替代（模型只渲染它认得的物件，不渲染你的意思）；③环境与情绪用 NL 氛围句承载（自然语言是组装件的粘合剂）。

**公理 2 · 强先验吞弱信号**：任何泛称、弱形容词、程度副词、状态词都会被模型默认先验覆盖（long hair→中分短发、broken→完整态、street→模糊背景、"深夜"只靠色温→黄昏、gem gone dark→照旧发光）。推论：①每个外观维度都写**具体值**，禁止概括词；②状态转换写**结构句**（原位已空+落点在场），禁过程动词（spreading/forming/点亮）与程度副词（淡淡/微微/渐现）；③范围即强度——要"淡"给"小范围"，要"满"给"大范围"，禁写浓度；④"不要 X"必须显式编码，不能只靠剧本口头说。

**公理 3 · 字面化一切**：任何比喻/文学词/抽象状态都会被渲染成最常见的具象物件（frozen→冰块、puppet→提线木偶+丝线、like a trophy→真陈列品、pig→真猪鼻）。推论：①角色关键特征（表情/姿态/道具）一律用 **Danbooru tag**，NL 只补氛围/情绪/关系，不重复标签已有信息；②明喻只放模板 C 情绪收尾层，不放 tag 层；③负面主动压字面化产物。

**基础预算（clip 注意力硬纪律）**：正面 **~150-300 词**（经验值 131-235 词高质量，硬上限 ~450 词）；负面 **~60-120 词**。砍词优先级从低到高：环境道具堆砌 → 氛围 NL 扩写 → 动作修饰词 → 表情冗余词；**不可砍**：质量头+安全档+画师、角色块（一致性锚点）、核心动作/表情。负面同理：基线模板+意图预测追加，不加"保险"词。

**Tier 0 单帧可读性（先于一切档位）**：单页只允许 **1 主动作 + 1 主导表情 + 1 空间关系**；超过=拆页或砍动作。分镜/脚本同守。

## 写提示词硬纪律（拼尸块落实 · 先于一切模板）

**提示词 = 拼尸块清单，不是描述画面。** Anima 没有智能，把 token 一块块拼出来；它不推理因果、不补过程、不理解叙事意图。落脚本时逐句自检：

- **每个要出现的视觉元素必须显式、独立、点名**（tag 或具体物理状态）：一个元素 = 一个尸块。想要"红痕在手腕"就写 red marks on her wrists；想要"围猎"就写 blades pointed at her, she parrying, sparks flying；想要"脱衣"就写结果态 topless, bare shoulders, removed clothes on the floor。
- **禁止文学化 / 因果 / 叙事句**：being pulled off... she is left in...（脱衣因果链）、rope pressed into... leaving red marks（勒→痕因果）、a trap closing in from all sides（描述画面）、against her will、the moment...——一律删。状态转换**只写结果态**（现在露什么 + 脱掉的件去向在场），不写过程动词和程度副词。
- **NL 只写"具体可画的物理状态"**（谁 + 哪个身体部位 + 做什么 + 对什么），不承载叙事/情绪/因果；模板 C 只允许末尾一行情绪收尾。
- **模型不补因果 = 一切都要写死**：绑缚→勒痕单独写；衣服被脱→露出单独写；被围→冲突单独写。宁可多一块，不可指望它长出来。
- **自检**：扫一遍正面，任何"需要模型脑补因果/过程"的句子 = 不合格，改成结果态 tag。

# 需求拆解（从模糊输入到完整提示词）

## 1. 理解意图（这张图用来做什么）
- **角色展示**（立绘/肖像/全身）→ 聚焦角色，3:4 或 2:3
- **场景氛围** → 纯场景转 anima-scene-prompt；角色+场景互动用模板 A/C
- **游戏/产品资产** → 按用途定比例，氛围匹配调性
- **NSFW/成人** → 重在 H 动作的场景用模板 B；重在氛围/感觉用模板 C；分阶段套图混用（前段 C 建氛围，H 动作段 B，终态氛围段回 C——混用规划归 doujin-plan N9）
- **黄漫/长篇套图（>10p）** → 剧本与分镜用 anima-doujin-plan，本 skill 负责逐页落脚本
- **测试/对比** → 固定 seed、简化变量、用 _compare 子文件夹、模板 A 快速迭代
- **自动化批量/高质量成品** → 默认模板 C

**模板复杂度**：默认模板 C（叙事）；NSFW 有 H 行为默认模板 B；对话中简短测试用 A，定稿升级 C。纯场景转 anima-scene-prompt。

## 2. 理解主体（画什么？气质是什么）
气质是核心推断轴——构图/光线/表情/画师都围绕它。已知角色 → 补外观（发色/眼睛/服装/招牌 pose），按**实际性格**推气质；原创 → 从描述提气质；场景 → 提情绪基调；多主体 → 各自描述外观+动作+判断关系。
气质→写法对照：锋芒/傲娇/腹黑 → cinematic lighting, confident expression, sharp focus；温婉/清冷/治愈 → soft lighting, gentle expression, portrait；暗黑/哥特 → chiaroscuro, dark background, low saturation, intense/empty eyes；热血/张扬 → dynamic pose, dutch angle, dramatic lighting；日常/萌系 → soft daylight, smile, warm palette, outdoors；神秘/氛围 → atmospheric, volumetric lighting。气质不明确 → 默认柔和。**角色外观不锁固定偏好**（避免批量同质化），按题材自由定。
**上下文线索很重要**：项目语境（如题材语境是"修女"→按暗黑克苏鲁修女处理）优先于预设偏好。

## 3. 推断缺失 + 推断原则
按用途+气质补齐：比例（角色 3:4/全身 2:3/场景 16:9/加载 21:9/头像 1:1）、构图视角（立绘=upper body/looking at viewer，NSFW=按体位）、光线（见气质表）、表情（按气质/动作档位）、安全等级（默认 safe，含 NSFW 词升档）、画师（高人气无画师/冷门按画师策略）、seed（探索随机/对比固定）、质量词（AES 不加 score_*，Turbo/Base 加）、负面（按档位基线+问题预测）。
**推断原则**：①优先满足主要意图，次要可省；②不确定时选合理默认**不反问**，直接生成，用户不满意再调；③注意上下文。

# 提示词结构（按场景选模板）

**Tag 规则**：小写、空格不用下划线（仅 score_* 保留下划线）；Danbooru/Gelbooru 冲突取 Gelbooru 版；画师 tag 加 @ 前缀（无 @ 效果很弱）；权重比 SDXL 高（(chibi:2)）；纯 NL 至少 2 句。
**Tag 顺序（必守）**：`[quality/meta/year/safety] [1girl/1boy/1other] [character] [series] [artist] [appearance] [pose] [setting] → NL 补充`。标签定主体，NL 只补氛围/情绪/关系（1-2 句封顶），不重复标签信息。**不要写互斥标签**（from front + from behind、solo + 1boy）。

## 模板 A：标准（角色立绘/肖像/场景互动）
- **A-简**（纯标签）：`masterpiece, best quality, [safety], [highres], [year], 1girl, [character (series)], [series], [@artist], [hair], [eyes], [body], [clothing], [pose], [expression], [setting], [composition], [lighting]`
- **A-混**（标签+NL，角色+场景互动）：标签定主体，NL 补场景/氛围，不重复标签信息
- **A-极简**（高人气角色/快速测试/LoRA 测试）：`[character], [series], [@artist or style tag]`
- 场景路由：纯场景→scene-prompt；角色+简单背景→A；角色+场景深度互动→A 或 C

## 模板 B：分段式（动态构图/NSFW/多角色）
用**换行分段**，每段一个语义层（模型训练见过大量换行分段）。**NSFW 和动态构图最常用。**
``
[quality + safety + censor]
[composition: 视角/构图/景深]
[character: 1girl/1boy, 角色名, 外观]
[action: 动作/姿势/体位]
[effect: 动态效果/体液/表情]
[setting: 场景/环境/光线]
[text: 文字/音效 (如需)]
[series]
[quality tail: highres, absurdres, masterpiece, best quality]
``
**setting 段不能省**（不写场景=默认纯白背景，NSFW 尤其需要）。`BREAK` 可代替换行但有被模型当画面文字烘焙到纸/卷轴上的风险——**优先真换行分段**；必须用 BREAK 时负面加 `text, english text, letters, words`，且 BREAK 附近避免放文字载体道具。
**六行骨架（长篇 doujin 场景页参考）**：①camera 行 `from {front/side/above/below/pov}, {shot}, {dutch angle|dynamic pose|shallow depth of field|dramatic lighting}` ②角色+服装状态行（人物块固定段 + torn/damaged/removing 状态词头）③动作行（体位/接触点显式）④表情行（三档字面库选档）⑤环境行（**地点+2-3 道具+光源+色调**）⑥回响行 `highres, absurdres, masterpiece, best quality,`。骨架价值是"每层一个语义"而非固定格式。

## 模板 C：叙事模板（成品级/自动化/默认）
用**分号分层法**：分号串联画面层次，每片聚焦一个层次，不分句。比换行更连贯，比标签堆叠更精确。
**混合形态（doujin 封面/终章/状态转换页）**：tag 头（质量头+档位+完整角色块）→ `{场景标记};` → NL 正文（分号分层：姿态动作；表情；环境+光源）→ camera 注 → 情绪收尾 `evoking an atmosphere of X`/`conveying X.`。
写法要点：**角色四件套**（外观+服装每件+动作+表情，单角色 50-80 词）；**否定式定调**（rather than/avoids/not 排除歧义）；**"反摆拍"美学**（characters focused on their own actions rather than looking at the camera, non-staged realism, unposed, candid）；**具体>笼统**（limited to 6 hues: electric cyan, burnt orange… > vibrant colors）；**情绪收尾**（最后 1-2 个分号片段必是 mood/atmosphere）；**画师/风格前置**（长描述前先 @画师锁定画风）。
完整例（C；演示分号分层，实际按画师策略与 AES 纪律——AES 不加 score_*）：
``
masterpiece, best quality, safe, highres, newest, anime coloring, @your-artist. A gentle half-elf girl with very long silver hair and purple eyes, detailed round eyes with large pupils, pointy ears, wearing a white and purple dress with cross-lacing and a crystal pendant; standing in a sunlit garden with soft diffused light casting gentle shadows; looking at the viewer with a gentle smile; shallow depth of field, low-angle portrait composition; rendered with delicate brushwork and soft pastel tones; evoking a serene, ethereal atmosphere of quiet grace.
``

# 质量标签 / 安全等级 / 负面提示词

## 质量标签
- **Aesthetic（默认）**：`masterpiece, best quality` 即可，可加 very aesthetic/amazing quality/ultra detailed/intricate details/highres/newest/professional illustration。**不要 score_***。
- **Turbo/Base**：加 score_9,8,7；负面可加 score_1,2,3。
- 元标签：分辨率 highres/absurdres；时间 newest（默认）/year 20XX/old；风格 anime coloring/anime screenshot/official art/cel shading/clean lineart；美学 very aesthetic/beautiful lighting/ultra detailed。
- **质量词纪律**：质量词只写一次（头或尾择一）；同义词选 3-4 个禁堆叠。

## 安全等级（四档，按画面可见露点判定）
`safe`（全年龄）/ `sensitive`（性感氛围但**不露**）/ `nsfw`（**露乳房**）/ `explicit`（**露性器官**，通常配 uncensored）。explicit 页正面**必须点名露性器官**（penis exposed, penetration, sex, fellatio 按行为写），禁只暗示。**例外（单人裸露氛围页）**：无性行为/无角色交互/不聚焦展示身体的单人 explicit 页（终态/余韵/独处），不强制点名——`nude, bare breasts` 即可，走纯模板 C。判据："讲故事还是展示身体？"讲故事→纯 C；展示身体→B+C。
**bar censor**：正面 bar censor=要求黑条；正面 uncensored=要求无码；负面 censored, bar censor, mosaic censoring=推开打码。要无码：正面 uncensored + 负面 censored, bar censor, mosaic censoring, covered。convenient censoring=便利遮挡。

## 负面提示词（意图预测式，非固定清单）
**原则：负面不是保险清单，而是根据正面可能引发的问题预测性选择。** 每个负面组件解决一个具体问题。先选基线模板，再按问题预测表追加。
**基线唯一 = 三档完整负面模板**（单图/短篇与长篇 doujin 通用）：
``
NEG_CORE = worst quality, low quality, artist name, blurry, jpeg artifacts, bad anatomy, bad hands, missing fingers, extra digits, fewer digits, fused fingers, watermark, signature, text, 3d, realistic, extra limbs, mirror, reflection, duplicate, futanari, gay, yaoi, shemale, femboy
NEG_SAFE  = CORE + lowres, nudity, multiple people, cloned face, spiral eyes, swirly eyes
NEG_NSFW  = CORE + deformed, disfigured, young, minor, multiple people, cloned face, censored, bar censor, mosaic censoring, covered, spiral eyes, swirly eyes
NEG_EXPL  = NEG_NSFW + extra hands, multiple hands, barbie doll anatomy   # explicit 档正面 uncensored
``
**简化变体**：**A 最简**（简单立绘/肖像、无复杂手部）= NEG_CORE 头部（worst quality, low quality, artist name, blurry, jpeg artifacts）；**E 空负面**（像素/复古风、分段式 NSFW 正面够精确、极简标签式、水彩/氛围场景、Aesthetic+极简正面）；**F 自然语言负面**（高级，Aesthetic 尤其有效，如 "This image suffers from low resolution... bad anatomy and six fingers... watermarks and signatures."）。B/C/D 已并入 NEG_*，勿单独用。手部重追加 `(bad hands:1.6), (extra digits:1.4), (6 fingers:1.5)`。
**负面针对性纪律（必做两件事）**：①**反向保护**——正面关键动作/姿态给反向词防误读；②**删误杀项**——通用模板里压制正面在场元素的项要删（场景有散落衣物时删 clothing/bra/panties；正面 ahegao 看镜头时删 looking at viewer；正面有镜子时删 mirror）。**正负冲突扫描（落脚本必做）**：负面不得压制正面已在场的元素——原则：**负面压的是"画面不应出现的东西"，不是"画面正在要求的东西"**。
**排空纪律（集中）**：排空只压"未设计的意外人物"。**非群交页**——正面 `no other people` + 负面 `strangers, bystanders`（禁 multiple people 替代）；**显式群交/多角色页**——保留 `multiple people, cloned face` 防克隆/串角；**设计人群**（分镜已规划的宾客/围观剪影）——不写 no other people、负面不压 strangers/bystanders/crowd，按"人群/阵型 + silhouette 可见性"渲染成模糊剪影。
**问题预测追加表（扫正面，按需追加）**：
| 正面特征 | 可能问题 | 追加负面 |
|---|---|---|
| 手可见/握物/复杂手势 | 手指崩坏 | bad hands, missing fingers, extra digits, fewer digits, fused fingers |
| 严重手部（持武器） | 六指/爪 | (bad hands:1.6), (extra digits:1.4), (6 fingers:1.5), 4 fingers |
| 多角色（2+） | 肢体融合/串角 | extra limbs, extra arms, cloned face |
| 持武器/道具 | 武器复制 | duplicate weapon, duplicated sword, double swords |
| 角色不看镜头 | 模型默认看镜头 | looking at viewer（仅非镜头页用）|
| 面部特写 | 眼/五官崩+刘海漂移 | deformed face, deformed eyes, bad pupils, wide-eyed + altered bangs |
| 全身 | 比例失调 | bad proportions, long neck, long body, missing limbs |
| 不要占版乱码 | 大字水印/多语乱码 | large text, watermark text, english text, chinese text, korean text（别把纸/屏压成纯白，见"防文字烘焙"）|
| 要彩色 | 褪色/单色 | monochrome, greyscale, sepia |
| 兽耳角色（非兽人） | 毛茸茸化 | furry, feral, semi-anthro, mammal, anthropomorphic |
| 动态构图/分段式 | 分镜/多视图 | multiple views, comic（要分版图 inset 时去掉 comic）|
| 职业/制服角色 | 模型默认带标志性配件 | headscarf, wimple, veil, hood（不需时）|
| 明喻/文学词 | 比喻字面化 | puppet strings, marionette strings, mounted head, trophy |
| 非入控页写 spiral | spiral eyes 圈圈眼 | spiral eyes, swirly eyes（光源改 swirl, concentric glow）|
| 异种族/体型差 | 比例失真 | 正面 waist-high to her；负面 miniaturization, chibi, tiny |
| 私密页/双人页 | 背景路人/旁观者 | 正面 empty shop, no other people；负面 strangers, bystanders, customers, crowd |
| 仪式/神秘/契约单人页 | 模型脑补旁观者/无主之手 | solo focus + 双手出画/捧持写死 + 负面压无主之手 |
| 发光/光效元素 | 光效抢主体/多发光串色 | 见"道具纪律-发光"（NL 光感句 + 熄灭写暗态对照）|
| 多人页归属模糊 | 道具/武器归错人 | 见"多角色纪律-归属"（归属句+反面排除句+在场部件）|
| 广角/终局页前景小道具 | 唯一前景实体被放大成主角道具 | 正面给尺寸+状态词；负面 oversized X, emblem, crest, insignia |

**权重语法**：Anima 对低权重不敏感，(tag:1.5) 或更高有效，(tag:0.8) 几乎无效；多层括号≈强加强；负权重 (tag:-1) 压制；画师权重 (@artist:1.5) 加强。**NegPip（正面负权重）**：部分执行脚本支持把正面里的 (tag:-权重) 自动拆到负面节点——**Turbo（CFG 1.0）负面节点几乎无效**，排除词优先用 NegPip 放正面（如 (wimple:-1.5), (puppet strings:-1.5)）；脚本不支持 NegPip 时，Turbo 下改用正面显式排除句（如 no head covering）并接受折损；AES（CFG 4.0）直接加负面节点即可。

**权重进阶**：严重问题加权 (bad hands:1.6), (6 fingers:1.5), (censored:1.5)；风格强推 (3d:1.5), (realistic:1.5)；组加权 (worst quality, low quality, normal quality:1.4)。

**决策流程（落脚本必走）**：①定模型 → AES 不加 score_*/Turbo+Base 加；②选基线 → 按档位 NEG_SAFE/NSFW/EXPL，简单立绘 A 最简、像素/氛围 E 空；③扫正面 → 按问题预测表追加；④严重问题加权；⑤检查冲突 → 要黑条不能负面加 bar censor、要彩色不能负面加 watercolor；⑥正负冲突扫描；⑦道具可见性扫描（见道具纪律）。
**Fixed prefixes**：AES 默认 `masterpiece, best quality, [safe/sensitive/nsfw/explicit], `；Turbo/Base `masterpiece, best quality, score_9, score_8, score_7, [档位], `。

# 人物一致性（一次生图保证角色统一）

同一角色的多张图靠提示词约束保证一致，不依赖 img2img。核心：**每个外观维度写具体值，禁止概括词**。

**名字即锚点（公开动漫角色 vs 原创角色，规则相反）**：模型只记忆**公开动漫作品**的角色（rem (re:zero)、saber (fate)、mikasa (aot) 等）——**公开角色名字即锚，已足够**：角色名 (作品名) 逐字复用即可锁外观，**不必遵守全维度人物一致性纪律**（你往往记不全它所有维度）。只写**确定知道**的维度（如用户给定的发色/瞳色/签名配饰），**不确定的维度一律不写**——写了会画错（如给穿高跟凉鞋的角色补靴子、给圣徒编一套不存在的衣服）。**OC 正好相反**：名字对模型无效（mio/alicia 自造名不锁任何外观），所以 OC 才需要**全维度固定块**（发色+发型+瞳色+肤色+体型+签名配饰+服装）逐字复用；OC 的名字只作文档/分镜表的键，不进 prompt 当锚。

**维度封闭清单（签名优先）**：身高/体型胸围/肤色/发型发色/眼色眼型/眉（只锁颜色+粗细，不锁形状）/刘海（必须具体锁定）/服饰每件/衣领·袖长·裙摆/内搭里衣/袜有无色长/鞋靴长型/发饰位色/首饰/武器唯一位置/贯穿道具四要素。**签名优先**：识别 1-2 个定义辨识度的"签名特征"用规范 tag 钉死；低先验微特征（泪痣/眼线/睫毛/眉形）不写裸 tag，改 NL 钉位句或干脆不锁。
- **发型/发色**：`silver hair, very long hair, blunt bangs, side ponytail`。体位大变页（仰躺/俯身）是发型漂移高发位，重申发型形态句+负面排除漂移。**发长双锚**：强先验分类词作主 tag（long hair）+ 体部位 NL 确认句（hair reaching her waist）并存——禁把主 tag 换成先验弱的细词（只写 waist-length hair 整批读成短发）。
- **眼色/眼型**：`purple eyes, detailed eyes, round eyes, large pupils`；**肤色**：fair skin/pale skin/tan/dark skin（不写会随机）；**体型/胸围**：slim + small breasts（必须写）+ 身高比例（tall/petite/height difference）。
- **服饰（每件+颜色+具体款式）**：white dress shirt, red ribbon, blue pleated skirt, black thighhighs；单件再拆领/袖/裙摆；外套+内搭时里衣显式；**深色大面积服装写死色名+负面防漂白**（black nun habit + 负面 white habit, white robe, pale dress——场景主色场会把深色服装洗浅，亮场景页必查）。
- **武器（战斗角色必写）**：具体款式+银刃；武器**三态固定块**（in hand/on her back/on the ground beside her）每页必选一态；**唯一位置锁定**：正面写死位置+补 no sword at her hip + 负面 sword on hip, scabbard, sword on back, duplicate weapon；状态用静态短句。
- **腿足**：靴/鞋写死长短+样式；袜子维度显式（no socks/black knee-high socks/white thighhighs）；裸体页显式 bare legs + 负面 stockings, pantyhose, thighhighs。
- **头部遮盖**：无头巾=正面 hair fully visible, no head covering + 负面 headscarf, wimple, veil, hood。
- **发饰/配件**：锁款式+颜色+图案（brown headband with triangle pattern）+ 负面 bandana, white headband。**首饰/身体标记**：每件锁款式+材质+颜色。
- **面部细部（宁可少锁不可乱锁）**：眉只锁颜色+粗细（禁 arched/angled/straight 形状 tag）；眼线/睫毛仅大头照特写页用；面部装饰（泪痣/雀斑）默认不加，仅签名才加用 NL 钉位；**刘海必须显式选型禁留空**——三轴定位（遮挡程度 露额→半遮→遮眼 × 线条曲直 平直→斜向→曲线 × 对称轴 居中→偏侧→不对称），Anima 对方向性刘海识别准确（实测），按三轴自由选型；blunt bangs 只是默认之一（hime cut 是发型不是刘海）。
- **规范 tag 纪律**：禁伪 tag（sharp thin eyebrows 合并 token）；鬓发 sidelocks（不写 long sidelocks）；有 blunt bangs 时删冗余 straight hair。
- **氛围/气质**：用具体 pose/表情锚定（looking at viewer, gentle smile, soft lighting，不是 elegant mood）。

**面部特征预算与门控**：特写页安全 ~8 个面部 tag，远景页 ~2 个。身份层每页逐字不变；眼线/睫毛/装饰仅特写页发。**表情页纪律**：面部块每页恒定，表情只通过**追加恰好一个尾部 tag**表达，不在表情页改面部块。

**人物块两段式（长篇 doujin 用，落脚本每页强制）**：
- 第一段固定模板**逐字复用**：**公开动漫角色 = 角色名 (作品名) 即可**（名字即锚；只补确定知道的维度，不确定的不写）；**OC = 不写名 + 全维度固定块**（发色+发型+瞳色+detailed eyes+刘海锁定+fair skin+体型+胸围+签名配饰+服装，15-20 tag 固定顺序）。
- 第二段"状态增量槽"只允许追加，禁止改写第一段。
- **全本逐字复用，无例外**：explicit/H/特写/动作页**不得**把第一段缩成"角色名+当前状态"（`1girl, 名字, nude, collar` = 不合格，无视觉锚）；特写页可**增**面部细节 tag，但**不可删**第一段任何一项（含刘海/签名配饰/肤色）。
- **签名配饰全本在场**：定义辨识度的发饰/配件（如 twin hair ribbons）必须出现在**每一页**，不得前几页有、后面断掉。
- **刘海锁定全本**：每页同一刘海写法（blunt bangs 等），禁某几页写某几页漏。
- 罕见公开角色兜底：系列名在人物块和页尾各写一遍。

**泛称→具体化对照（高频踩坑）**：
| 泛称（会偏离） | 具体化（一致） |
|---|---|
| school uniform | white dress shirt, red ribbon, blue pleated skirt |
| casual clothes | white hoodie, denim shorts, sneakers |
| elegant outfit | black evening gown, lace trim, pearl necklace |
| armor | silver plate armor, pauldrons, leather belt |
| nice body | slim, medium breasts, fair skin |
| beautiful hair | black hair, very long hair, straight, blunt bangs |
| headband | brown headband with triangle pattern |
| small creature | waist-high to her（显式相对高度）|
| sword | wide short sword, silver blade（具体款式）|
| leather boots | brown leather boots, ankle-length / black knee-high boots |
| stockings | black knee-high socks / white thighhighs / no socks（显式有无）|
| 高度 | tall / petite / average height / height difference |
| bangs（泛称）| blunt bangs / swept bangs / long bangs（写死类型）|
| pretty face | blunt bangs, thin eyebrows, eyelashes, large pupils |
| long sidelocks | sidelocks（长度继承 very long hair）|
| sigil/淫纹/符文 | a crimson sigil forming a ring around her navel（显式形状+环绕关系）|
| 姿态/体位 | standing upright, leaning back against the desk edge（仪式/修女语境模型默认跪姿；意图站/躺/坐时显式体位词+参照锚+支撑点+接触面三层）|
**反例**：a beautiful girl with elegant clothes、cute anime girl、silver hair, purple dress。**正例**：silver hair, very long hair, straight hair, blunt bangs, purple eyes, detailed eyes, round eyes, large pupils, black eyebrows, fair skin, slim, small breasts, half-elf, pointy ears, white and purple dress, ribbon, cross-lacing, detached sleeves, crystal pendant + 刘海钉位句 + 负面 parted bangs, swept bangs, hair between eyes, hair over eyes。

**服装纪律（含移除双锚）**：**服装状态转换双锚定**——脱/卸类写"**现在露出的部位显式** + **脱掉的件去向在场**"（topless, bare chest + breastplate and pauldrons discarded on the ground），禁只写"脱掉的件去向"——对着装先验强的角色（骑士甲胄/制服/修女袍），只写护甲散落而漏写裸露，模型把护甲仍画在身上（实测）。**服装变化写清楚即够**：每页穿什么/露什么/脱掉的件去向显式写，禁靠语境暗示（泡澡页不写"毛衣已脱"就穿毛衣泡温泉）。**半脱多结构件按件写状态**：系带裙的半脱=系带+肩带+裙摆多件，每件单独给状态动词+已不在原位；动作依赖状态时动作与状态同帧写死，禁只写动作不写状态（状态不成立则动作无对象，被替换成提裙摆/拨头发/捏细物）。**敞开/裸露类**：角色块默认内搭在裸/露乳页必须显式移除（red skirt front open to her waist, no chest binding, her breasts bare）。**跨角色服装（A 的衣穿 B 身）**：归属主体（his long black coat）+ 穿着状态（wrapped around her bare shoulders）+ 原主在场（his hands closing the collar behind her）。
**元素在场性（升维到角色）**：角色/印记/核心道具只按"该帧是否在场"写完整块——缺席不写块；solo 页禁携带其他角色外观块；本页不应出现的印记显式排除（her skin clear and unmarked, no sigil）。

# 道具纪律（集中；源头：原"发光质感与道具材质先验"+一致性+分版图+原 doujin 执行纪律道具条）

**第一原则：道具按镜头可见性写，禁硬凹在场**。道具只在画面能看到/该页承担剧情功能时写；构图不可见/遮挡时**省略不画**。判别："这页去掉这个道具，剧情/动作还成立吗？"成立就省略。

**道具三档分类（显式定义）**：
- **L0 身份符号（身体附着物：项圈/淫纹/纹身/烙印）**——每页必含（长在身上的），但构图不可见时仍按可见性省略。持续区间属 N6 状态进度条物证，非普通道具。
- **L1 手持/悬挂道具（剑/怀表/十字架/戒指/法杖）**——**只在必要页出场**（首次获得/被夺/觉醒/终态回收，或该页动作确实需要），其余页整个省略。**H 动作页禁在场**（双手归动作：撑床/被按/迎合；实测做爱页手里抓十字架）——禁写手持/贴身/滑过身体短语，负面也禁写具体手持短语（负面写 cross in hand 反而注入概念、渲染成抓在手里——两头都不写最稳）。道具叙事留到非 H 页回收。
- **L2 环境道具（L3 背景舞台）**——可省略；叙事触发物（觉醒/看剑/回收）**禁落 L2**，落背景必配"镜头+空间锚+光线或动作交互"三件套，或分版图 inset / 省略。

**道具材质/形态（公理 2：被场景强先验替换）**：写**材质+形态**+负面排除替换体——white marble pillar / golden ceremonial rope with tassels + 负面 wooden pillar, hemp rope, brown rope；与性器同框的独立道具加分离句+否定穿透。**深色贯穿道具被场景主色场拉浅**：色+材质每页逐字重复（deep purple silk stole / black nun habit，禁裸泛词）+ 负面排除浅色漂移；头位道具写材质+颜色+系戴方式 + 负面排除头巾替换体（wimple, white headscarf, veil）。
**发光道具要光感不能只写 glowing（公理 2/3）**：光标签只锁定"发光"事实，光感靠 NL 光效句（a soft golden glow radiating from the cross hairpin, light spilling onto her hair）；发光态禁过程动词（flickering），用冷暖光对比表达。小面积身体标记写死位置+负面排除漂移位。**发光→熄灭禁只写否定状态词**（gem gone dark/unlit 被发光先验无视照旧发光）：改**暗态写实词+环境替代光源在场**（dark unlit sapphire on the staff + wall torches cast the only light），"熄灭"由"暗物 vs 别处有光"的对比读出；或负面压 glowing, light emanating from the staff。**光效量级控制**：光效是氛围不是主体——small glow, held in his hand，禁 filling the foreground/bathing the whole frame 级描述。
**小面积色是低注意力维度**：关键色（油彩/印章/签名墨）写**全词色名+材质名词**（a thick stroke of vermilion red oil paint，禁裸 vermilion）+ 负面排除漂移色相。**小物件换不易误会的颜色+简单形状+简单刻印**（印章/徽章/胸牌/吊坠）：金色圆片=必读成金币（实测），改深色/非金色（dark bronze/black）+简单几何形状（round/crescent/flat disc）+简单刻印（engraved with a flying eagle）。
**无定形材质（雾/烟/影）被实体化替换（公理 3）**：动作需求逼出实体。写**动作挂雾+质感词同句**（wispy black mist coiling around her wrists, semi-transparent, her skin visible through the vapor）+ 负面排除实体替换体（solid leather straps, metal chains, black gloves, solid claws）。雾躯人形写"凝聚点+散逸边界"。
**可数道具数量不保真**：模型按"合理值"取整。数量是叙事点才写死（≤3 相对可靠），纯氛围数量用约数（a ring of black candles/several）。
**破损/非默认状态是低注意力维度（公理 2）**：broken 弱形容词=模型回默认完整态。写**结构句**（blade snapped in two, the hilt still in her hand）+ 负面排除默认态。集合先验压个体：指定集合位置里的具体物时，类别先验会替换个体指定，负面压该集合默认成员。高显著性吸附：小面积发光标记/前景道具会被性器/嘴/胸吸附，位置写死+吸附位负面+归属句。身体小痕（吻痕/红印）写**位置+闭合无创口** + 负面 wound, scar, cut, bleeding（吻痕画成刀疤是失败模式）。
**道具状态转换双锚定**：掉落/滑落/拾起写"**原位已空 + 落点/悬停位在场**"：掉落 her hair bare now, no hairpin, the red hairpin fallen on the tatami + 负面 hairpin in her hair；半脱写悬停位 the stole slipped down to hang around her neck from her collar；拾起 her hand lifting the bell off the floor, the floor bare where it lay。**道具时间线纪律**：道具不能早于"首次出现页"出现在角色状态增量槽——按道具引入拆多个角色块版本，分镜表道具链列标注首次页，落脚本按页选对应版本。
**道具接触点句（空间歧义必用）**：保留 tag 栈 + **一条 NL 句钉死四问**——锚（哪只手/哪部位）、接触（什么碰什么）、遮挡（前/后/环绕）、禁令（无穿透）。**身体接触点+排除默认位**：模型把接触点默认塞回构图惯例位，身体接触句用四问且必须**反面排除默认接触位**（his gloved hand resting on her collarbone, not touching the contract）。
**持具动作必须"工具握在手中+接触点触对象"**（her right hand gripping the white quill, its nib pressed to the parchment）；"正在书写/落款"=执行者身位+持笔手+落笔部位三件套同帧；同一角色两个持具节拍禁同帧。**递物/交接+脸区接触双接触点禁同帧**：落**完成态姿态**——持方双手显式在道具上 + 接收方手出画或空手；脸区互动改无手接触（俯身凝视/贴近）或拆页；负面压错误持握 staff in his hand, staff held by him。**血液/体液流动写"源→轨迹→目标"链**（her right palm cut open, blood dripping from her fingertips into the ritual circle），禁只写结果态。**传递/接手类**：双方手同时在物上+物在两手之间（both her hands cupping the folded garment, his gloved hands beneath hers supporting it mid-transfer）。
**道具可见性裁决（操作规则）**：机位使必含道具/部位不可见时，三选一：①省略不画（主手段）；②分版图 inset（背面属性需展示时）；③换机位/正面朝向。**禁挪位**——把道具从原位置挪到可见处破坏位置要素。
**分版图 inset 写法**：主格写主导画面 + 小格写 inset 内容（主画面+inset 小格）；落脚本保留 multiple views、负面去 comic；小格只承载新信息，格数写死。分镜表需分版图时在"单帧事件"写主画面+inset 小格。
**画中次人物（照片/画像/屏中像）**：介质标注（a woman inside the small photograph）+ 特征与主角**显式相反**（发色/眼色反向）+ 主角特征重申（her own pink hair and red eyes unchanged）+ 负面防融合（merged face, same face, duplicate person）。**画中画内容与完成度双锚定**：早期态 mostly blank white canvas with a single faint charcoal contour line + 负面 finished portrait, detailed painting；成品态 a fully finished portrait of her in the exact same reclining pose + 负面 blank canvas, empty canvas；真人模特与画布画像双体共存写"空间在场"锚（the model standing before the canvas, her body apart from the painted surface）。

# 视线纪律（集中；源头：原 anima 公共场景/多角色/NSFW + 原 doujin N3 视线条）

**视线逐页单选（落脚本必查；实测：全本默认 looking at viewer 导致该看镜头的页反而没看）**：每页视线**恰好一个目标**（朝镜头 XOR 看人 XOR 看物），按页型定：①**必须朝镜头**（looking at viewer/toward the camera）：POV 服务页她仰头、ahegao/heart 符号页（facing camera）、终态空洞朝镜头页；②**必须看人**：对视页（双方朝向互指）、胁迫/对峙页（看向施动者）；③**必须看物**：觉醒页锁触发物（+not at the camera）、沉浸/余韵页（gazing at X rather than the camera）。**禁默认 looking at viewer**（非镜头页写了=摆拍污染全本）；**禁互斥视线并存**（模型随机取一）；**该指定的页禁留空**（留空=模型自由发挥，符号页常丢 facing camera）。负面 looking at viewer 只用于②③类页。
**无法对视时用头位三态**（faceless 男/背对/远处目标，对视无落点=回落看镜头）：写 looking down/up/straight ahead，禁写"对视/看他"式指向无脸者的短语。
**视线-关系双向因果**：①对峙页显式排除镜头（not at the camera）；②对视=双向视线互指；③**诱导道具引入页必须"她看到道具"**（道具在视线路径上+目视因果）；④排空人群覆盖所有两人页。男方剪影无眼时给朝向/近景可渲染表述或改可露脸处理。
**跨空间视线交互写完整视线链（禁只写"被看见"）**：谁+朝向+看谁+对视+可识别身份（an elderly vendor below looking up toward her window, his gaze meeting hers, apron, steam rising from the wok）——依赖"被看见"的叙事节拍必须把观察者反应落进画面（抬头/顿住/手按胸口/后退），否则节拍在画面空白。
**POV 归属必须明确**：pov 裸写有歧义。**读者 POV**：pov, first person view + 前景出现"读者自己的手"；**角色 POV**：her point of view, from behind her shoulder / looking at her own reflection，且**禁止前景出现读者手**。写 POV 必须含该视角主体的**前景部件**，否则模型直接第三人称化。**POV 页"仰头看他"="抬头朝镜头"**：支配者 POV 下她抬头对视方向就是镜头方向，直接写 looking up toward the camera。
**观察/窥视类页空间排布**：只写 watching him 不够——模型把观察者画前景、被观察者画背景，层叠导致视线不成立。必写**空间排布三要素**：①谁前景谁背景；②视线方向穿过空间；③空间关系一帧难成立时用分版图（主格=观察者表情特写 + inset=观察者 POV 里的被观察者）或改观察者背对镜头构图。

# 多角色/多人纪律（集中；源头：原 anima 多角色场景/公共场景 + 原 doujin N3/执行纪律 prompt 条）

**每角色必须单独描述外观+动作（否则串角）**：每角色独立四件套 + `girl:`/`boy:` 前缀锚定描述块；完整外观写在**一个连续段**内不散落；**不要在同一行混写两角色标签**；solo focus 时非聚焦角色只写局部。**防串角纪律**：同发色用长度/发型/配件显式区分——双女主/母女/姐妹同框**禁只差一种发色深度**，至少拉发型轮廓/眼型/体型/服装剪影 2 维（实测栗棕 vs 深棕同框必串）；负面加 cloned face, extra limbs, extra arms。
**多人原创角色难区分→用知名角色锚定（实测有效）**：Anima 对多人原创角色特征区分有限，但对知名动漫角色记忆强（名字即锁特征）。多人页优先替换成高人气知名角色（女仆位=蕾姆 Re:Zero/玛修 Fate；骑士位=Saber；法师位=晓美焰）；写法：锚点用角色名 (作品名) 起头 + 保留核心特征词；选角标准=题材适配+人气高+外观差异大（至少两维反向）。跨作品混搭可行，同作品更稳。单人/双人原创效果好时不必借。
**男主默认最简配色+无脸（降低注意力压力）**：男主（faceless male/男方）无特别说明时统一最简配色（solid black outfit, dark cloak）+ `1boy, faceless male, his face in shadow`，不堆发色/五官/服装细节——男主细节越多，模型注意力越分散、越易把特征串给女主或漏画男主。角色有特殊设定再单独写。
**第三人最小特征集（闯入/见证/旁观，非主体）**：分镜给最小特征集（2-3 维可辨识特征）+ 位置/朝向/动作钉死，**禁写完整五件套**（发/眼/体态/服装/武器全写）——完整多特征块在含双人主体的帧会被模型**特征分家**裂成多个身体（实测捉奸页：base 给剑士完整块被渲成 4 人，最小特征+背对=3 人正确）。落脚本负面压 a fourth person, an extra person。**silhouette 可见性（通用）**：silhouette 只用于亮背景/背光，暗场景禁剪影，改 figures in torchlight, backlit。
**faceless 执行者主动作绑定**：执行主动作的角色看不见脸时（咬颈/喂饮/压制类贴身接触）归属最脆弱——模型可把主动作挂到任意未绑定角色、甚至生成替身（实测）。写法：①执行者即使 faceless 也把**可辨身份件留在动作接触位**（his ringed pale hand / the black cloak collar against her throat）；②接触点钉**执行者→受动者**（his lips pressed to the side of her neck / biting her nape），禁泛写"有人咬她/她被按在窗边"不指定施力者；③主画面**禁第二个未绑定男角**，负面压 another person, a second man。
**faceless 传达分姿态+机制强度层级**：禁删字面 faceless 锚词。**机制强度层级**：物理遮挡（头出画/被主角身体或头发盖住）> 剪影/背光 > 字面 faceless tag > 单一弱句（face in shadow/covered by hair）> 纯负面。**支撑/仰卧/朝镜头/背景位 faceless**（身体朝观众、姿态不保证无头）要**强机制+弱句叠加**，禁只交弱句兜底（实测朝镜头的 faceless 只写 face in shadow 脸仍浮现）；负面仍压 visible male face, his face revealed。
**受动者"朝向"显式 + 禁 hand visible（可见性遮蔽≠朝向）**：只写"脸被蒸汽遮、只露肩颈手"（可见性）模型仍把被遮脸者当正向面对、把持具手/主动作归给他、动作方向整个反转（实测浇背整页被画成他持勺往她手心倒水）。受动者块**写朝向**（back turned/facing away/head bowed away）+ 轮廓只露背/后颈/肩，**禁 head/face 措辞与 hand/hands visible**（"only his head and shoulders seen from behind"式会给模型一个头、脸随之显形）。显式声明"双手空"可写（hands empty 守住被动位）。**作用对象钉归属**：被破坏的敌方持物/门/容器，事件句写成"主体+主动词+对象后果"（she smashes through an enemy shield, the shield bursting into splinters），禁泛对象状态描述（"被劈碎的敌盾"不写谁持=模型把盾挂到主角身上、动作反转成主角持盾格挡）。
**特殊动作条款**：
- **液流方向独立钉死**：浇/倒/舀/泼类，受动者朝向显式只保"谁服务谁"，保不住"液体流向谁"。分镜"单帧事件"列把水流结果写死（水沿他背流下/溅到他肩上），落脚本按"动作结果钉受动者体表+负面液流反向排除"双锚写。
- **支撑/搀扶/接住双端同写**：接触点**同一实体部位双端同写**（his hand supporting her shoulder + her shoulder against his hand）；受动者朝向写**朝执行者躯干或体态崩倒**（slumping, knees giving way），**禁写"朝他的手"**（头部是强触控先验区，写"头转向他的手"把执行者的手拉向头部、肩部支撑被劫持成"抚头/整理头发"）；接触点只落**非先验实体部位**（肩/臂/腕/腰），负面压 hands on head, touching hair, hand in hair。
- **双人交缠相对位置写双向锚**：双方各带方位参照（her back against the pool wall, facing him, his chest against her front），禁只写"他贴近她"。
- **交叠/骑乘体位第二角色防丢**：模型强先验把"两人交叠"压成单人女体（实测）。被压/被骑的一方给**同规格外观块**（boy:/man: 前缀四件套+接触手+朝向），禁只写动作句；负面压 solo, 1girl, single person。
**局部角色碎片（只露手/下巴/袍裾/性器）**：二级角色只露局部时模型不可靠（过度补全/部件替换/悬浮）。五原则：①写**连续连接链+显式裁切入画**，禁裸部件词（a black-gloved hand and the hem of a black robe cutting into the frame from the lower right edge, sleeve and glove connected）；②性器/身体局部给归属句（his erect penis emerging from the parted opening of his black robe, attached to his body, not detached + 负面 disembodied penis, floating penis）；③碎片需求与主角关键状态同帧竞争必丢其一——主角关键状态前置+高权，碎片收敛到单锚点；④动作执行者绑持具手+朝向，受动者绑被动姿态+空手排除，执行者外观至少两维与受动者反向；⑤双手各写死一个明确职责+接触对象（her right hand resting on the glowing sigil, her left hand turning the page）；空闲手显式写空（her left hand loose at her side）；对称双手动作写 both her hands reaching up to receive the stole, her right and left hands together, both visible。
**投影/倒影介质**：人物以影子/剪影投影在介质表面时，模型误读成"透过门/窗看到真人"。写**介质表面标注+真人不在场显式+负面排除透窗/门内真人**（two dark human silhouettes cast as shadows on the sliding paper screen, the people themselves out of frame + 负面 people outside the window, figures in the doorway）。**倒影成精/镜像不同步禁裸反射词**：当"镜/水面/玻璃里的倒影"是单帧核心（倒影是另一表情/时序/微差）时，`mirror`+`reflection`+微差描述是模型渲不出的微差——倒影被画成另一个女人（实测）。改可渲介质（画中像/屏中像/屏风影/卷轴内：介质标注+特征反向+主角特征重申+负面防融合），或"主格+残迹"（主画面当前态+倒影残迹当辅证）。确需镜子只做氛围的独处页眼神反光用 caught in her eyes/glint，禁裸 reflection。**反射纪律**：反射性/重复性物件（镜面墙/玻璃/水面/透明材质）在含第二角色（faceless male）或多手的页会引发视觉重复——反射元素只用于**无第二角色的页**；H 动作页去掉反射，或负面压 mirror, reflection, duplicate, extra hands, multiple people, cloned face。
**公共场景与人群叙事纪律**：场景类型靠**视觉签名元素**钉死，不靠地点名（street→crosswalk, traffic light, wet asphalt reflections；便利店→glass storefront, checkout counter, aisles of shelves；深夜电车→subway interior, handrails, overhead lights）。**"无人察觉"写个体动作禁抽象疏离词**（head down, looking at phone, eyes closed 可渲染；ignoring her, nobody looking at her 全败）——给每个可见路人一个可渲染动作。**人群/阵型**：以空隙/轴线为视觉载荷；重要角色与群众剪影分明度/材质；顺序/轮替：把已完成与待进行两态同时画进一帧。**双人站位关系写空间排布禁抽象关系词**（standing guard/protecting her 不落位）：写谁前景谁后景+双方朝向+身体遮挡关系。
**环境词库纪律**：每本新开环境词库，禁止跨本复用。环境行强制"**地点+2-3 道具+光源+色调**"（dark mansion bedroom, four poster bed, silk sheets, candlelight, warm golden light）。

# 文字与 UI 元素

**字幕实操（实测）**：长句字幕易渲染成乱码/大字占版，叙事信息优先用视觉表达；必须字幕时保持短句（≤5 词）+ english text "..." + 底部小字定位。禁一段式对白。
**防文字烘焙（压"占版乱码"不压"纸上有字"，实测）**：模型烤的伪字笔划密集、观感像能读的日语其实不可读；但**逼成纯白空纸/纯亮无字屏比不可读的字更出戏**——宁可纸上有模糊字迹也不要空纸。做法：①负面压的是破坏性烘焙——large text, watermark text, english text, chinese text, korean text, japanese text（按需加权）——不要把纸/屏压成纯白；纸张/便条/账本保留字迹（faint ink strokes, smudged handwriting, barely legible scribbles）；②对话/交谈页"字幕先验"极强，光负面压不住，配合让画面"不像对话页"（非正面并排对坐，改侧位/错落机位）或接受烤字；③屏幕/纸张是核心道具且剧情靠上面的字时，模型渲不出可读文字——用"屏泛光+模糊UI字迹"/"纸上糊字"表达；④图表/名单无法承载具体数值，剧情信息改台词/旁白/表情传达，但表/纸本身保留字迹。
**文字标签**：english/japanese/chinese/korean text、speech bubble/sound effects/onomatopoeia/moaning、text/title/typography。UI/排版：magazine cover/doujin cover/bust chart/height difference/logo/white border/recording photo interface。

# 构图/视角/光线

**方法论**：视角按气质/用途（立绘=upper body/looking at viewer，场景=wide shot，NSFW=按体位，动态=dutch angle/pov）；构图按主体（角色=shallow depth of field/portrait/centered，动作=dynamic pose/foreshortening/perspective，多角色对比=bust chart/height difference）；光线按气质（暗黑/治愈/热血/神秘见需求拆解气质表，另加 rim light/backlighting）。
**高频 8 tag**：dutch angle、from above/below/side/behind、pov、shallow depth of field、dynamic pose、looking at viewer、cinematic lighting、rim light。动态效果：motion blur/motion lines/impact lines/afterimage/midair/contrapposto/ass ripple/bouncing breasts。
**时间基调光线写光源方向+色温对比+受光元素，禁裸时间词**：morning light/moonlight 裸时间词是弱信号，模型回落默认光线。写光源方向+色温对比+场景受光元素（warm golden morning light slanting through the stained glass windows, long soft shadows, light pooling on the stone floor）+ 负面排除时间错位（晨光页 night, moonlight；月光页 daylight, sunlight）。
**时段承载页（时段本身是叙事锚）加"环境暗度基线+时段排除句"**：光源色温对冲只给色温对比、不给绝对暗度——写 cold blue moonlight clashing with warm orange glow 模型按亮环境默认，深夜被读成黄昏（实测同 wording 两份独立读图均命中）。写法：①显式锚环境暗度（dark indigo night sky, a sliver of crescent moon, scattered stars / near-total darkness lit only by X）；②否定式定调排除相邻时段（not dusk, no sunset glow, no daylight）；③局部暖光仍可留。
**前景遮挡关系（笼子/栏杆/窗框/玻璃后）**：角色处于前景物体之后时，模型常把挡在前面的物体透明化/凭空消失。不能只写"她在 XX 里"——要明确遮挡：①透过敞开的门/开口看（seen through the open cage door, bars behind her）；②栏杆/栅栏放角色后方（cage bars behind her, none across her body）；③前景物体只框边缘（cage frame at the edges framing her, open center）；④或负面加 transparent bars, missing cage bars, bars across her body。

# NSFW 专用指导

> NSFW 时，用 anima-nsfw-prompt 的解构方法优化本 skill 模板里的 NSFW 片段——是补充/优化，不是路由到另一套模板。结构/模板/负面/标签库/安全等级/bar censor 全部仍用本 skill。长篇 doujin 先用 anima-doujin-plan 做剧本+分镜。

**构图模式**：①分段式（模板 B）最常用；②faceless male 搭配：1boy, faceless male, hetero + (faceless male:0.7), (male is nude:0.7), (small penis:0.5)；③POV：pov/pov hands/pov chest/pov doorway；④只需女方身体时 1girl + faceless male hands 比 1boy 更安全；⑤bar censor 见安全节；⑥x-ray + cross-section 效果不稳定慎用。
**动作标签按事件分层选**（体位/动作/效果/特殊玩法/身体细节，完整词表查 Gelbooru）：体位 missionary/cowgirl position/reverse cowgirl position/doggystyle/spoon position/paizuri/fellatio/deepthroat/buttjob；动作 deep penetration/penetration/creampie/oral/groping/skirt lift/panty pull；效果 ass ripple/bouncing breasts/jiggle/trembling/arching back/stomach bulge；身体 nipples/erect nipples/puffy areolae/large areolae/cameltoe/skindentation；特殊玩法 restrained/tape gag/arms behind back/chained/mass orgy/mmm threesome。表情 ahegao/ohogao/fucked silly/rolling eyes/uneven eyes/heart eyes/bedroom eyes/embarrassed。
**表情三档字面库（字面库；选档规则归 doujin-plan N10）**：抵抗/羞耻档 teary eyes, biting lip, heavy blush, gritting teeth, furrowed brows；沉醉档 half-closed eyes, blush, open mouth, tongue out, drooling, heart-shaped pupils；高潮档 ahegao, rolling eyes, tongue out, heart-shaped pupils, drooling, sweaty, convulsing。**终局空洞配方**：empty smile, half-closed eyes, heart-shaped pupils, light blush + 自相矛盾收尾（no expression/no resistance）。
**核心体液**：cum/cum in mouth/cum on face/cum on body/cum on hair/excessive cum/saliva/drooling/pussy juice/pussy juice trail/sweat/shiny skin/very sweaty/oiled。
**进阶技法**：分段填充顺序（模板 B）= censor 顶行→动作/朝向→角色+身体→表情→体液簇→HCG 特效块→系列→质量尾。HCG 特效子词库：2way-afterimage, afterimage, motion blur, motion lines, impact lines, onomatopoeia, sound effects, speech bubble, japanese text + 压制非日文 (english text, engrish text, chinese text, korean text:-1)。**tags+NL 澄清**：空间关系模糊时保留 tag 栈+追加一句 NL 锁定接触点。**NSFW 专属负面**：disembodied penis, floating penis, barbie doll anatomy, minor。
**吊缚/悬吊页防"勒颈/自缢"误读**：绳的附着点必须钉死+否定排除颈部：the rope attached to her wrists bound together above her head, none of the rope around her neck + 负面 hanging by neck, noose, suicide, rope around neck, strangulation。失神吊缚页补气色对冲词（blush on her cheeks, warm skin tone）。
**NSFW 常见坑（实测精华）**：
1. **手部多手**：避免 NL 描述双手做不同动作（one hand... the other hand...），用标签式 hands clawing at sheets（双手同动作），负面加 extra hands, multiple hands。
2. **提示词过长**：遵守 clip 预算，NSFW 页尤其容易叠堆——氛围标签选 2-3 条配合，调味料不是主菜。
3. **场景/时间不一致**：分阶段套图统一场景和时间，靠角色状态变化推进，不要每张换光线色调。
4. **服装漂移**：外观模板逐字复制到每张，禁止改写或省略；服装状态只能按行为轴显式递进（着衣→敞开→褪），每阶段写死——不能 S2 敞开 S3 又拉上。
5. **歧义标签**：frozen 会被理解为冰冻（冰块），时间停止用 time stop, motionless + NL（body locked in place as if time stopped）。spiral 非入控页写会触发圈圈眼——光源/光效用 swirl, concentric glow, curved light trails；spiral eyes 只留给入控页本尊（详见 nsfw 意识状态轴）。**5.3 职业服饰默认配件**：nun/knight/priest 自带标志性配件（wimple/头盔/白领）——不需要时负面压 headdress, wimple, veil, hood 或正面 no head covering。
6. **外部手的归属**：描述外部角色手操作（脱衣/摆位/触碰）时明确写 faceless male hands，且不要同时给女主手部动作标签，否则模型画出 4+ 只手。**6.1 动作动词强度**：groping 渲染弱（揉胸画成托胸）、lips around penis 画成闭嘴露牙——关键动作加具象动词+状态：揉胸 faceless male hands squeezing her breasts, fingers pressing into her flesh；口交 mouth open wide, lips sealed around him, cheeks hollowed, sucking。**接触判定类动作**：①接触点命名显式身体部位禁代词（her mouth around his penis 不写 around him），补相对位置 kneeling between his legs + 目视 looking up at him + 体位指向 his hips forward；②接触物必须露出（penis exposed, pants pulled down/male nude，不写=模型按穿衣态画成隔裤吸）；③判定机位必做——低位服务默认 pov, from above 俯视+她仰头回应（from the side 旁侧平铺最易歧义）；④规范动作标签 fellatio, deepthroat 必写；⑤负面压 kiss, french kiss, lips pressed together；⑥**同部位一状态**——嘴/眼/手每部位只能一个状态（biting lip 禁与 tongue out/drooling 共存，互斥词混入=人体畸形）。**6.2 口交页禁用 gritting teeth**（"画出牙齿"的直接诱因），用 humiliated expression, furrowed brow, tears, drool at the corner of her mouth 替代。**6.3 体型比例显式化**：异种族/配角体型用显式相对高度（waist-high to her, reaches her hip）禁 small/tiny 笼统词。
7. **冻结态表情锁定**：时间停止下表情锁定在定格瞬间，不能新增（眼泪必须是定格前就有的）。
8. **临床标签去色气**：medical examination 作为动作 tag 会过于临床。用 setting 标签（clinic interior, examination table）+ 具体动作 tag（spread legs, restrained）替代。
9. **自定义道具一致性**：叙事核心依赖非现实道具时用简单可描述的形状（silver ring, round purple gem），五要素锁定（形状+大小+位置+颜色+表面细节各定一个固定词），逐字复用；发光/状态词只用一个；归属双侧编码+发光色锁定（见道具纪律）。
10. **表情用 tag 不用文学描述**：a vacant serene half-smile 无法解析，用 empty smile, half-closed eyes, light blush。生僻文学词（serpentine/vacant/serene/etched）效果不确定。
11. **双人行为无男方处理**：含双人行为 tag（cowgirl/fellatio/sex/missionary 等）时必须明确男方处理方式（1boy, faceless male 或 faceless male hands+接触位置 或 pov 或 silhouette），否则模型自行生成完整男性角色。

# 特殊风格

- **像素风**：pixel art, limited palette, retro game cg, pc-98 style, flat shading, dithering, 16-bit。建议小画布 768x1024。空负面。
- **水彩/手绘风**：watercolor, hand-drawn, freehand, loose brushwork, soft brush aesthetic, traditional media, muted palette, flat_color。配模板 C 的 NL 句式骨架（开头定调 A [vibrant/serene] ... rendered in [watercolor/freehand] style with [palette]；光线作用 natural diffused sunlight casting sharp shadows across [surface]；情绪收束 evoking a [mood] atmosphere of [emotion]）。
- **Superflat/装饰风**：superflat, flat_color, vector art, city pop, maximalist decoration, hand-drawn texture, non-symmetrical composition。
- **线稿风**：lineart, simple background, loose lineart, sketch。
- **Dataset tags（非动漫，罕用）**：ye-pop/deviantart 开头+换行 → pop/abstract/数字绘画。
**Pose 锚定气质（实测验证，重要）**：角色有招牌动作时，用 **pose tag 锚定气质 >> 表情 tag**（由乃的 hand on own cheek, head tilt, crazy smile, empty eyes 比笼统的 yandere, smile 有效得多）。有招牌动作的角色优先写 pose tag，放在 clothing 之后、expression 之前。

# LoRA 使用指导

本节只给通用决策思路，不附具体 LoRA 清单（文件名/触发词以你本地实际为准，照搬他人清单前必须实测）。
**决策流程**：质量不够→通用质量增强型 LoRA（权重 0.5-1.0 起步）；色彩平淡→色彩风味 LoRA（0.6 起）；NSFW 质量不够→NSFW 增强型 LoRA（0.8 左右）；要画师风格→画师 LoRA + 对应画师 tag；要特定动漫画风→对应风格 LoRA；Turbo 加速→Turbo 专用加速 LoRA + 减步（**仅 Turbo 可用，AES CFG 4 下会崩**）；不需要→不加。
**使用限制**：8GB 级显存最多叠 2-3 个 LoRA，1-2 个最稳；画师风格 LoRA 必须配对应画师 tag 否则效果很弱；跨模型变体（AES/Turbo）混用前先同 seed 实测。

# 画师策略

## 何时用/不用
- **高人气/经典/近年热门角色 → 无画师**（Anima 训练充分，画师反而干扰还原）
- **长篇 doujin（>10p）→ 先按题材判断本子适合的画师组合（冷门还原/特定风格/NSFW 特化），无明确对应时选高人气通用风格画师兜底；多本同批时各本用不同组合防跨本同质化。同本内画师链全本统一、不可中途更换（经验：H 动作页加画师后质量对齐叙事页、画风统一；模板差异只保留在 NL vs tag-stack 结构）**。
- 冷门角色/Anima 学不准 → 画师辅助还原；需要特定风格 → 对应风格画师；NSFW → 可用 NSFW 画师；无明确风格要求 → 无画师。
## 核心画师表（不随分享版发布）
画师-气质映射、画师组合配方、题材→画师速查属**作者个人实测数据**，本分享版不收录。建议自建：固定角色 + 固定 seed，批量试画候选画师，记录画风表征后按题材归档（方法与纪律见下）。可借助社区画师风格数据库（如 Anima Style Explorer 类资源）选候选。
**毒点警告（通用）**：部分画师 tag 在特定模型变体上存在表征崩坏（毒点），且任何来源的画师清单都未必标注适用变体——照抄前必须先用同 seed 实测。
## 画师串纪律
画师串 **≤2 优先**；三画师及以上风格先验抢占背景渲染权重（经验：纯白背景概率上升）。组合画师=画师名逗号连接。四画师组合一律不用（实测收益普遍一般）。
## 关键结论（实测精华）
1. 高人气角色优先无画师，且不要降画师权重"修"还原度（降权 destabilize 风格锚点滑向 2.5D，保持 1.0，用 feature tag 补被画师覆盖的特征）。
2. 画师 tag 不一定忠实于画师本人风格（表征偏差）。不能凭本人风格预判，必须实测。
3. 画师-角色匹配要到气质+配色级别——没有坏画师 tag，只有放错地方的画师 tag（反例：灰调画师会把金发角色配色带偏）。
4. 系统风格特征无法靠 seed-rolling 修复（画师签名特征是必然）——用相反 tag 补偿但注意 whack-a-mole，接受小瑕疵。
5. 多画师配方必须拆解对比验证（逐个去掉画师验证是否真有贡献）。
6. 病娇/微表情气质靠 pose tag 锚定 >> 表情 tag。
7. 画师链用随机 seed——永远不从一个 seed 判画师好坏。
8. 避免半写实/油画风画师（半写实 tag 在 Anima 动漫域易翻车）。

# ComfyUI parameters

默认 **Aesthetic fp16**；其他模型只改 model + steps + cfg + 质量词，TE/VAE/sampler/scheduler/size/negative 结构全不变：
- **Aesthetic fp16（默认）**：model anima-aesthetic-v1.1.safetensors → UNETLoader (weight_dtype: default)；TE qwen_3_06b_base.safetensors → CLIPLoader (type: stable_diffusion)；VAE qwen_image_vae.safetensors → VAELoader；Sampler er_sde|euler；Scheduler simple；**Steps 30 (30-50)；CFG 4.0 (4-5)**；负面不用 score_*。
- **Turbo fp16（需明确指定）**：model anima-turbo-v1.0-fp16.safetensors；Sampler er_sde|euler；**Steps 10 (8-12)；CFG 1.0**（CFG-free，>1 崩）；质量词加 score_9,8,7，负面可加 score_1/2/3。
- **Base**：model anima-base-v1.0.safetensors；Steps 30；CFG 4.0；加 score_*。
- **GGUF 量化（仅 VRAM 不足时）**：Q4_K_M/Q5_K_M/Q8_0 → UnetLoaderGGUF。注意反量化有额外开销，低显存设备上未必比 fp16 快；Q5 档位可能出现质感偏硬（木刻感）。仅 fp16 OOM 时考虑。
**Pitfalls**：①CLIPLoader type=stable_diffusion 正确（ComfyUI 自动识别 Qwen3-0.6B 路由到 anima.te/AnimaTokenizer，下拉无 anima 选项不要找；TE 是 safetensors，用原生 CLIPLoader，不是 GGUF CLIP loader）；②Turbo CFG 必须 1.0（AES 用 4.0，不要混淆），>1 崩；③Sampler 不唯一（er_sde 官方默认/euler Turbo pick，差异是风格不是对错）；④Agent 不要 Read 输出图验证（多数文本模型非多模态，读 PNG 可能失败）——用文件大小 + PNG tEXt prompt 块验证，最终由用户看图；⑤画师-角色配色冲突可负面针对性加颜色 tag，但根因是画师选错，换画师优于硬补偿；⑥分段式提示词的换行会原样传入 ComfyUI——提交时确保换行符保留（CLI 传参注意 \n 转义或用 stdin 管道）。

# Naming convention (mandatory)

ComfyUI 默认 ComfyUI_00001_.png 无用。设 filename_prefix：`anima_<subject>[_variant][_batch]`。subject=1-3 个小写英文词下划线连接；variant=画师或版本；**batch 在对比轮次必加**（_b2/_b3/_0724）。总长 <30 字符，别放长画师链。例：anima_elfmage_v2_b2。
**Compare runs**：A/B 测画师/参数时用统一子文件夹（如 `_compare/<subject>`）集中输出对比轮，测试图与生产图分目录管理，互不干扰。可选：用输出整理脚本按前缀/mtime 自动归档到日期文件夹。

# Generate via local ComfyUI

**执行方式**：用任意封装 ComfyUI API 的提交脚本（推荐自写一个：封装 submit+poll+verify，CLI 参数 --prompt --negative --width --height --seed --prefix），或直接用 ComfyUI 官方 Anima workflow 模板。API 队列格式：`{"prompt": {node_id: {class_type, inputs}}}`。
流程要点：
1. **Probe**：GET http://127.0.0.1:8188/system_stats；down 则启动 ComfyUI（--listen 127.0.0.1；低显存设备加 --lowvram 防 VAE decode OOM；先清理僵尸 python 进程释放 VRAM）。
2. **Submit**：POST /prompt {"prompt": graph, "client_id": "anima"}。成功 = {"prompt_id":..., "number":N, "node_errors":{}}——空 node_errors = 验证通过，非空才是真失败。
3. **Poll**：GET /history/{prompt_id} 直到 status.completed；读 outputs[*].images[*]。
4. **Locate**：文件先落 output/；若配置了自动整理脚本，可能已被移到日期子目录——根路径找不到则 glob output/**/<filename>。
5. **Verify（纯文本）**：stat 文件大小 + 解析 PNG tEXt prompt 块确认参数。不要 Read 图片。
6. **Report**：最终路径+参数给用户，让他们看图。

# Output structure (for every Anima request)

1. **Request** - restate what the user wants.
2. **Prompt** - 完整 Danbooru-tag 正面 prompt 一个可复制块；负面独立块。说明用了哪个模板（A-C）和哪个负面档位及原因。
3. **Params** - size（含比例理由）、sampler/steps/CFG、seed、画师链（含理由）、filename prefix。
4. **Generate** - ComfyUI 在跑则提交并报告保存路径；否则告知如何启动。没有文件落盘就不算成功。
