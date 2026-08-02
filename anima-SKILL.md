---
name: anima-prompt
version: share
description: Activates when the user wants to generate anime/illustration/character/art image assets, or mentions the Anima model (circlestone-labs/Anima). Turns a Chinese/English description or rough prompt into an Anima-optimized Danbooru-tag prompt, picks one of 3 prompt structures by scenario (standard / segmented / narrative), auto-selects canvas size/aspect ratio by content, handles NSFW/explicit content with proper tag structure and a curated action-tag library, decides whether to use artist chains (and which, by style-fit analysis), and submits to local ComfyUI (Aesthetic fp16 default, Turbo fp16 for fast iteration when explicitly requested) to produce the image. Route anime/illustration/characters to Anima; route realism/scenes/photos to Krea 2 (krea2-prompt skill). Includes model info, prompt templates, intent-based negative-prompt system, NSFW guidance, composition tag library, artist strategy, naming convention, and ComfyUI graph.
---
## When to Activate

Activate when the user asks to **generate art/image assets** (美术资源) and:
- The subject is anime / illustration / character / 2D art / game art (not photorealistic)
- The user names `anima`, `Anima`, `circlestone-labs/Anima`, or wants a character portrait/sheet, anime scene, stylized illustration

**Routing vs Krea 2** (`krea2-prompt`): **Anima** ← anime/illustration/characters/2D art; **Krea 2** ← realism/photos/landscapes/rich interiors. Explicit Krea 2 / realism → defer to `krea2-prompt`; ambiguous + character/anime → Anima. Do not activate for video/3D models or pure text tasks.
## 需求拆解（从模糊输入到完整提示词）

用户输入通常是简短/模糊的（如"画个修女"、"某角色名"）。**自动理解需求并补全**，直接输出完整 Anima 提示词。核心是**理解用户想要什么样的画面**，而非关键词查表。
### 1. 理解意图（这张图用来做什么？）
- **角色展示**（立绘/肖像/全身）-> 聚焦角色，简洁背景，3:4 或 2:3
- **场景氛围**（环境/世界观/概念图）-> 纯场景用 `anima-scene-prompt` skill；角色+场景互动用模板 A/C
- **游戏/产品资产**（加载界面/封面/UI）-> 按用途定比例，氛围匹配产品调性
- **NSFW/成人内容** -> 重在 H 动作的场景用模板 B（分段 tag-stack）；重在氛围/感觉塑造、H 内容是附带时用模板 C（叙事）；分阶段套图常混用（前段 C 建氛围，H 动作段 B，终态氛围段回 C）
- **黄漫/长篇套图（>10p）** -> 剧本与分镜设计用 `anima-doujin-plan` skill（产出分镜表：幕/页码/单帧事件/镜头/模板）。**职责边界**：doujin skill 管"画什么"（分幕/事件链/波折/镜头语言/节奏），本 skill 管"怎么写"（模板 A-C/负面/标签库/安全等级全部仍用本 skill），把分镜表逐页转成提示词。doujin skill 不抢模板层，本 skill 不越权构思层
- **测试/对比**（画师/参数 A/B）-> 固定 seed，简化变量，用对比子文件夹，模板 A 快速迭代
- **自动化批量 / 高质量成品** -> 默认模板 C（叙事级，分号分层+四件套）

**模板复杂度选择：** 默认模板 C（叙事）；NSFW 有 H 行为时默认模板 B（不必用 C）；对话中简短测试用模板 A 快速迭代，定稿后升级 C。纯场景/背景图转 `anima-scene-prompt` skill。
### 2. 理解主体（画什么？气质是什么？）
主体气质是核心推断轴——构图/光线/表情/画师都围绕它决定。

- **已知角色** -> 查用户偏好记忆（如有）获取外观+气质标签。角色名后必须补外观（发色/眼睛/服装/招牌 pose），否则串角。**根据角色性格理解气质，推断匹配的构图/光线/表情**（种族属性如精灵/魔女/兽耳不是气质，需看实际性格）：
  - 有锋芒/傲娇/腹黑 -> 强势：cinematic lighting, confident expression, sharp focus
  - 温婉/清冷/治愈 -> 柔和：soft lighting, gentle expression, portrait
  - 暗黑/哥特/克苏鲁 -> 暗调：chiaroscuro, dark background, low saturation, intense/empty eyes
  - 热血/张扬 -> 动态：dynamic pose, dutch angle, dramatic lighting
  - 日常/萌系 -> 明亮：soft daylight, smile, warm palette, outdoors
  - 神秘/氛围 -> 氛围：atmospheric, volumetric lighting, portrait
  - 气质不明确 -> 默认柔和，或按上下文推断
- **原创/无角色** -> 从描述提取气质按上表匹配；无描述默认 anime style, 1girl, looking at viewer
- **场景描述** -> 提取情绪基调（孤独/热闹/神秘/压抑），情绪决定光线和色调
- **多个主体** -> 每个单独描述外观+动作，判断主体间关系（互动/对立/无关）

**上下文线索很重要**：注意对话上下文——如用户在聊暗黑/克苏鲁题材，"修女"就应理解为暗黑克苏鲁修女而非普通修女；如用户偏好有锋芒角色，"修女"默认偏战斗/暗黑。
### 3. 推断缺失（用户没说的，按意图和主体推断）
- **比例**：按用途（角色=3:4, 场景=16:9, 加载界面=21:9, 头像=1:1）+ 内容（全身=2:3）。见 Auto size 表
- **构图/视角**：按气质+用途（立绘=upper body/looking at viewer, 场景=wide shot, NSFW=按体位）
- **光线/氛围**：按气质（暗黑=chiaroscuro, 治愈=soft daylight, 热血=dramatic, 神秘=atmospheric/volumetric）
- **表情**：按气质（温婉=gentle smile, 强个性=confident/smug, 暗黑=intense/empty eyes, NSFW=按动作选 ahegao/ohogao/embarrassed 等）
- **安全等级**：默认 `safe`；含 NSFW 词或语境明确 -> 对应等级；自动化 -> `safe`
- **画师**：高人气角色 -> 无画师；冷门/需特定风格 -> 查画师策略；无要求 -> 无画师
- **seed**：探索/成品 -> 随机；对比测试 -> 固定
- **质量标签**：Aesthetic 默认 `masterpiece, best quality, highres, newest`（**不加 score_***）；Turbo/Base 加 `score_9, score_8, score_7`
- **负面**：按意图预测式选择：简单立绘=A 最简，有手=B 标准，复杂手势=C 手部重，NSFW=D，像素/氛围=空 E
### 4. 常见模糊输入的理解路径
- **只给角色名** -> 角色展示；查记忆补外观+招牌 pose；按气质选构图/光线/表情；3:4；模板 A
- **只给场景**（如"雨夜街道"）-> 场景氛围；提取情绪基调；转 `anima-scene-prompt`；16:9
- **只给情绪/氛围**（如"孤独"）-> 推断主体气质补全构图/光线；可选原创角色或场景
- **用途明确但角色模糊**（如"游戏加载界面"）-> 理解产品调性补匹配角色/场景；按用途定比例
- **只给系列名** -> 按该系列中用户偏好选角色；按气质补全
- **自动化批量** -> safe, 随机 seed, 无画师, 模板 C, 标准负面（B）
### 5. 推断原则
- **优先满足主要意图，次要需求可省**
- **不确定时选合理默认，不反问** - 选一个合理的推断直接生成，用户不满意再调整
- **角色气质是核心推断轴** - 构图/光线/表情/画师都围绕角色气质决定
- **查用户偏好记忆（如有）** - 已知角色必查外观 + 气质标签
- **注意上下文** - 当前对话/项目/用户偏好都影响推断方向

拆解完成后直接进入模板填充和生成流程。除非推断明显矛盾或无法确定主体，否则不向用户确认。
## Model basics

- **Anima** = 2B-param text-to-image DiT, fine-tuned from `nvidia/Cosmos-Predict2-2B-Text2Image`. Created by CircleStone Labs × Comfy Org (2026-01). **Anime/illustration/art-focused - does NOT do realism** (by design).
- **License**: non-commercial for the model itself; **generated images may be used commercially** (selling art, commissions, game assets). Don't host the model behind a paid API / embed in a monetized product.
- **Versions**:
  - **Aesthetic** - aesthetics fine-tune, highest default quality. **CFG 4, 30 steps. No `score_*` tags.** **默认模型（全场景效果优于 Turbo，仅慢约 4.5 倍）**
  - **Turbo** - distilled, fast. **CFG 1, 10 steps.** 仅当用户明确要求快速迭代/测试时使用。
  - **Base** - CFG 4, 30 步, 加 score_*。画质一般不如 AES，仅训 LoRA 等需要中性风格时用。
- **本地安装**：Anima Aesthetic / Turbo / Base 三个模型文件 -> UNETLoader；TE 用 Qwen3-0.6B 文本编码器 -> CLIPLoader；VAE 用 Qwen Image VAE -> VAELoader。模型文件放 ComfyUI 的 models/ 目录（diffusion_models/text_encoders/vae）。ComfyUI 原生支持（`comfy/ldm/anima/`）。
- **GGUF 量化（仅 VRAM 不足时）**: Q4_K_M/Q5_K_M/Q8_0 -> UnetLoaderGGUF。**注意：GGUF 可能比 fp16 更慢**（反量化开销>VRAM 节省，两者通常都能进显存）；Aesthetic Q5 有木刻感。仅 fp16 OOM 时考虑。
## How to write the prompt

Anima 训练在 **Danbooru tags + natural-language captions + mixes** 上，三者都行；tag 风格对角色最可靠。

**Tag rules:**
- Lowercase, **spaces not underscores**（仅 `score_*` 保留下划线）
- Danbooru 与 Gelbooru 拼写差异时取 **Gelbooru** 版
- 画师 tag 加 `@` 前缀（`@mika pikazo`，无 `@` 效果很弱）
- 权重比 SDXL 需要更高：`(chibi:2)`；纯自然语言至少 2 句（很短提示词出垃圾）

**Tag order (mandatory):**
```
[quality/meta/year/safety] [1girl/1boy/1other] [character] [series] [artist] [appearance: hair/eyes/clothing] [pose/composition] [setting/environment] -> natural language supplement
```
- **标签+自然语言混用最好**：标签控制主体（角色/外观/动作），自然语言只补**氛围/情绪/关系**，**不重复标签已有信息**（角色名/外观/姿势），1-2 句即可。默认英文 NL（效果稳定），英文不好表达可用中文，但中文偶有构图/色偏波动需多抽几张。
- **角色名后必须补外观**（发色/眼睛/服装），多人场景尤其重要，否则串角。
- **不要写互斥标签**（from front + from behind、solo + 1boy）。
### 提示词结构（按场景选模板）

#### 模板 A: 标准（角色立绘/肖像/场景互动）

**A-简（纯标签，立绘/肖像/上半身）：**
```
masterpiece, best quality, [safety], [highres], [year], 1girl, [character (series)], [series], [@artist], [hair], [eyes], [body], [clothing], [pose], [expression], [setting], [composition], [lighting]
```
**A-混（标签+NL，角色+场景互动）：** 标签定主体，NL 补场景/氛围细节，不重复标签信息。
```
[quality], [safety], 1girl, [character], [series], [@artist], [核心外观], [核心动作]. [NL: 场景/动作/氛围], [构图]
```
**A-极简（高人气角色/快速测试/LoRA 测试）：** `[character], [series], [@artist or style tag]`（高人气角色训练充分，极简即可还原，详见画师策略）。

**场景路由：** 纯场景/背景（无主角）-> 转 `anima-scene-prompt`；角色+简单背景 -> 模板 A；角色+场景深度互动 -> 模板 A 或 C。判断标准：主角是角色用 A/C，主角是环境用 scene-prompt。

#### 模板 B: 分段式（动态构图/NSFW/多角色）
适用：复杂构图、多角色、NSFW 动作场景。**NSFW 和动态构图最常用此结构。** 用**换行分段**，每段一个语义层（模型训练时见过大量换行分段，能理解层次）：
```
[quality + safety + censor]
[composition: 视角/构图/景深]
[character: 1girl/1boy, 角色名, 外观]
[action: 动作/姿势/体位]
[effect: 动态效果/体液/表情]
[setting: 场景/环境/光线]
[text: 文字/音效 (如需)]
[series]
[quality tail: highres, absurdres, masterpiece, best quality]
```
**setting 段不能省**（不写场景 = 模型默认纯白背景，NSFW 尤其需要：卧室/浴室/走廊/户外等场景标签放这里）。`BREAK` 关键字可代替换行分段（模型训练见过），常用于隔开"角色定义段"和"场景氛围段"。

**六行骨架（长篇 doujin 场景页参考）**：①camera 行 `from {front/side/above/below/pov}, {shot}, {dutch angle|dynamic pose|shallow depth of field|dramatic lighting}` ②角色+服装状态行（人物块固定段 + torn/damaged/removing 状态词头）③动作行（体位/接触点显式）④表情行（三档字面库选档）⑤环境行（**地点+2-3 道具+光源+色调**）⑥回响行 `highres, absurdres, masterpiece, best quality,`。按页面意图增删（如纯表情页可只留 ③④⑤），骨架价值是"每层一个语义"而非固定格式。

#### 模板 C: 叙事模板（成品级/自动化/默认）
适用：高质量成品、自动化美术素材、电影感/叙事感角色图。**默认模板。** 分号分层法：用分号串联画面层次，每片聚焦一个层次，不分句。比换行更连贯，比标签堆叠更精确。
**混合形态（doujin 封面/终章/状态转换页专用）**：tag 头（质量头+档位+完整角色块）→ `{场景标记};`（`Cover page;`/`Wide shot;`/`Medium close-up;`）→ NL 正文（分号分层：姿态动作；表情；环境+光源）→ camera 注 → 情绪收尾 `evoking an atmosphere of X`/`conveying X.`。
```
[quality + safety + year + score], [style tags], [@artist]. [分号串联: 调性->光线->角色四件套->动作/互动->环境->构图/视角->情绪收束], [构图标签]
```
- **角色四件套**：外观（发色/发型/眼色/体型）+ 服装（每件+颜色+材质）+ 动作（具体姿势）+ 表情（眉眼嘴）。单角色 50-80 词，比标签式详细 3 倍。
- **否定式定调**：`rather than`/`avoids`/`not` 排除歧义。如 `gaze directed forward rather than at the camera` 比 `looking away` 精确。
- **"反摆拍"美学**：`characters focused on their own actions rather than looking at the camera, non-staged realism, unposed, candid`——日常/生活感/叙事氛围用，区别于摆拍肖像的 `looking at viewer`。
- **具体 > 笼统**：`limited to 6 hues: electric cyan, burnt orange...` > `vibrant colors`；`hands on own knees, reverse cowgirl` > `sexy pose`。
- **情绪收尾**：最后 1-2 个分号片段必是 mood/atmosphere，视觉元素向情绪收拢。
- **画师/风格前置**：长描述前先 `@画师, style_tag` 锁定画风，后面纯内容。
- **水彩/手绘 NL 句式骨架**（可复用句式，不要只堆标签）：开头定调 `A [vibrant/serene] ... rendered in [watercolor/freehand] style with [palette description]`；光线作用 `natural diffused sunlight casting sharp shadows across [surface]`；空间感 `captured from a [low-angle tilted] perspective enhancing depth`；质感 `loose expressive brushwork, limited palette dominated by [colors]`；情绪收束 `evoking a [mood] atmosphere of [emotion]`。

完整例（C；示例演示写法，实际生成按画师策略与 AES 纪律，勿照抄画师/score）：
```
masterpiece, best quality, score_9, score_8, score_7, safe, highres, newest, anime coloring, @rella. A gentle half-elf girl with very long silver hair and purple eyes, wearing a white and purple dress with cross-lacing, detached sleeves, and a crystal pendant; detailed round eyes with large pupils, pointy ears, a purple flower hair ornament; standing in a sunlit garden with soft diffused light casting gentle shadows; looking at the viewer with a gentle smile, body angled slightly to the right; shallow depth of field blurring the flowers behind her; low-angle portrait composition; rendered with delicate brushwork and soft pastel tones; evoking a serene, ethereal atmosphere of quiet grace.
```
### 质量标签

- **Aesthetic（默认）**：`masterpiece, best quality` 即可（官方推荐不加 score_*），可加 `very aesthetic, amazing quality, ultra detailed, intricate details, highres, newest, professional illustration`。**不要用 `score_*` 标签**（会推过头进 slop territory）
- **Turbo/Base**：加 `score_*`。最简 `masterpiece, best quality, score_7`；多级（更稳）`masterpiece, best quality, score_9, score_8, score_7`；全堆 `... absurdres, newest, highres`；权重 `(score_9, score_8, score_7:1.2)`
- **元标签**：分辨率 `highres`/`absurdres`；时间 `newest`（默认加）/`year 20XX`/`old`/`early`；风格 `anime coloring`/`anime screenshot`/`official art`/`intricate details`/`clean lineart`/`cel shading`；美学 `very aesthetic`/`beautiful lighting`/`ultra detailed`；专业 `professional illustration`/`high-res illustration`/`cinematic lighting`
### 安全等级

四档（替换 prompt 中的 `safe`）：`safe`（全年龄，默认）/`sensitive`（泳装/轻度亲密）/`nsfw`（非 explicit）/`explicit`（露骨成人内容）。

**档位阶梯**：`{safe|sensitive|nsfw|explicit, uncensored}` 常用作质量头槽位，档位与页面内容联动（**explicit 通常配 uncensored**）。**档位按画面可见露点判定**：safe=全年龄；**sensitive**=有性感/情色氛围但**不露**（诱导/催眠/半遮前戏页）；**nsfw**=**露乳房**（揉胸/半裸）；**explicit**=**露性器官**（口交/插入/自慰露点）——按画面可见判断，执行者不用猜。explicit 通常出现在"服装移除后第 1-2 页"（也有故意延迟/提前制造张力的案例）。负面随档位三档联动（见下）。

**三档完整负面模板（可直接抄，意图预测追加见问题预测表）：**
```
NEG_CORE = worst quality, low quality, artist name, blurry, jpeg artifacts, bad anatomy, bad hands, missing fingers, extra digits, fewer digits, fused fingers, watermark, signature, text, 3d, realistic, extra limbs, mirror, reflection, duplicate, futanari, gay, yaoi, shemale, femboy
NEG_SAFE  = CORE + lowres, nudity, multiple people, cloned face, spiral eyes, swirly eyes
NEG_NSFW  = CORE + deformed, disfigured, young, minor, multiple people, cloned face, censored, bar censor, mosaic censoring, covered, spiral eyes, swirly eyes
NEG_EXPL  = NEG_NSFW + extra hands, multiple hands, barbie doll anatomy   # explicit 档正面 uncensored
```
意图预测追加：武器位置页 -> `sword on hip, scabbard, sword on back, duplicate weapon, double swords`（裸/被操页再压本体 `sword, weapon`）；两人/私密页 -> `strangers, bystanders, customers, crowd`；特写页 -> `deformed face, deformed eyes, bad pupils`；非入控页 -> `spiral eyes, swirly eyes`（入控页本尊去掉）。

**bar censor 用法（NSFW 必知）：**
- 正面 `bar censor` = **要求**画面加黑条（日式打码）；正面 `uncensored` = **要求**无码
- 负面 `censored, bar censor, mosaic censoring` = **推开**打码（模型可能不完全听）
- 要无码：正面 `uncensored` + 负面 `censored, bar censor, mosaic censoring, covered`
- 要黑条风：正面 `bar censor`，负面**不加** `bar censor`（否则推开黑条）
- `convenient censoring` = 便利遮挡（用物体/头发等自然遮挡）
### 负面提示词（意图预测式选择）

**核心原则：负面提示词不是固定列表，而是根据正面提示词可能引发的问题预测性选择。** 每个负面组件解决一个具体问题。先选基线模板，再按问题预测表追加。**两套负面归一**：A-F 基线是单图/短篇通用模板；**长篇 doujin（>10p）直接用"三档完整负面模板"（NEG_SAFE / NEG_NSFW / NEG_EXPL）+ 意图预测追加**——三档模板就是 A-F 在长篇的落地版，二者取一不用两套并行。

**A. 官方最简（通用，Aesthetic 默认）：**
```
worst quality, low quality, artist name, blurry, jpeg artifacts, chromatic aberration
```
适用：简单立绘、高人气角色、Aesthetic 默认。（Turbo/Base 追加 `score_1, score_2, score_3`）

**B. 标准扩展（有手可见的角色）：**
```
worst quality, low quality, [score_1, score_2, score_3,] lowres, blurry, jpeg artifacts, bad anatomy, bad hands, missing fingers, extra digits, fewer digits, watermark, artist name, signature
```
方括号 Turbo/Base 加、AES 不加。

**C. 手部高权重（复杂手势/动态构图/多角色，最常见）：**
```
[score_5, score_4, score_3, score_2, score_1,] blurry ugly bad, text, (worst quality, low quality, normal quality:1.4), (lowres:1.2), (sketch, doodle, rough sketch:1.2), (bad hands, extra fingers, missing fingers, more than 5 fingers, fused fingers, claw:1.2), bad anatomy, bad proportions, extra limbs, watermark, text, signature, username, compression artifacts
```

**D. NSFW 专用**（单图/短篇用；长篇直接用 NEG_NSFW / NEG_EXPL）：
```
worst quality, low quality, [score_1, score_2, score_3,] artist name, blurry, jpeg artifacts, bad anatomy, bad hands, missing fingers, extra digits, fused fingers, deformed, disfigured
```
追加（按需）：要无码 `censored, bar censor, mosaic censoring, covered, clothes`；防串味 `futanari, gay, yaoi, shemale, femboy`；防多肢体 `extra limbs, extra arms, extra legs, multiple breasts, extra heads`；防幼态 `young, minor`；要裸体防特定服装 `jacket, bra, panties`（按实际穿着写）。NSFW 负面通常比 SFW 更简——正面足够精确即可。**群交页注（适用于所有含 multiple people 的档位模板）**：显式群交页（正面有 gangbang/multiple goblin silhouettes）**保留** `multiple people`（本职是防克隆/串角），追加 `cloned face, duplicate`；多剪影用 `silhouette` 表达即可。

**E. 空负面（何时留空）：** 像素/复古风、分段式 NSFW（正面够精确）、极简标签式（高人气角色）、水彩/氛围场景、Aesthetic+极简正面。CLIPTextEncode text 留空 `""`。

**F. 自然语言负面（高级，Aesthetic 尤其有效）：**
```
This image suffers from low resolution and poor quality, appearing pixelated and blurry. The character has bad anatomy and six fingers. The image contains watermarks and signatures.
```
Aesthetic 的 CFG 4 会放大 NL 负面影响，适用于 tag 负面不够时。

**问题预测追加（扫正面提示词，按需追加）：**
| 正面特征 | 可能问题 | 追加负面 |
|---|---|---|
| 手可见/握物/复杂手势 | 手指崩坏 | `bad hands, missing fingers, extra digits, fewer digits, fused fingers` |
| 严重手部（持武器/精细动作） | 六指/爪 | `(bad hands:1.6), (extra digits:1.4), (6 fingers:1.5), 4 fingers` |
| 多角色（2+） | 肢体融合/串角 | `extra limbs, extra arms, cloned face` |
| 持武器/道具 | 武器复制 | `duplicate weapon, duplicated sword, double swords` |
| 角色不看镜头 | 模型默认看镜头 | `looking at viewer` |
| 面部特写 | 眼/五官崩 | `deformed face, deformed eyes, bad pupils, wide-eyed` |
| 全身 | 比例失调 | `bad proportions, long neck, long body, missing limbs` |
| 不要文字 | UI/文字乱入 | `text, speech bubble, signature, username, logo` |
| 要彩色 | 褪色/单色 | `monochrome, greyscale, sepia` |
| 保持动漫风 | 3D/写实化 | `3d, realistic` |
| 兽耳角色（非兽人） | 毛茸茸化 | `furry, feral, semi-anthro, mammal, anthropomorphic` |
| 动态构图/分段式 | 分镜/多视图 | `multiple views, comic`（**要分版图 inset 时：负面去掉 `comic`，`multiple views` 可留**）|
| 职业/制服角色（nun/knight/priest 等） | 模型默认带标志性配件（修女头巾/护士帽/神父白领） | `wimple, headdress, veil, hood`（不需要时）|
| 明喻/文学词（like a puppet/trophy） | 比喻字面化（puppet→真丝线） | `puppet strings, marionette strings, mounted head, trophy`（或用具体描述替代）|
| 非入控页写 spiral | spiral 强关联 spiral eyes → 圈圈眼 | `spiral eyes, swirly eyes`（光源改 `swirl, concentric glow`）|
| 面部特写（放大脸） | 刘海/眉/发饰被重画漂移 | `deformed face, deformed eyes, bad pupils` + 发饰抑制 `bandana, white headband, different hairband` + `altered bangs` |
| 异种族/体型差角色 | 比例失真（small→迷你） | 正面显式相对高度 `waist-high to her`；负面 `miniaturization, chibi, tiny` |
| 道具/武器写了位置 | 模型补默认位/复制体（腰剑+断剑） | 正面 `no sword at her hip`；负面 `sword on hip, scabbard, sword on back, duplicate weapon, double swords` |
| 私密页/双人页 | 背景路人/旁观者 | 正面 `empty shop, no other people`；负面 `strangers, bystanders, customers, crowd` |
| 发光/光效元素 | 光效抢主体（全幅金圈）/多发光串色 | 正面限 `small glow, held in his hand`；禁 `filling the foreground`；**同帧发光物≤1、发光色独立于环境光、异色降级不发光**；负面 `halo, divine glow, holy aura, golden gem` |
| 多人页归属模糊（谁持道具/武器） | 道具/武器归错人 | 正面归属句（`his hand raising it`）+ 反面排除句（`her hand off the chain, not holding it`）+ 归属主体给可渲染在场部件 |

**权重进阶：** 严重问题加权 `(bad hands:1.6), (6 fingers:1.5), (censored:1.5)`；风格强推 `(3d:1.5), (realistic:1.5)`；组加权 `(worst quality, low quality, normal quality:1.4)`。**NegPip 语法**：正面写 `(tag:-权重)` 负权重，本地执行器内置 NegPip 预处理（`extract_negative_weights`）自动拆到负面，不需要外部 NegPip 节点。

**决策流程：**
```
1. 确定模型 -> AES 不加 score_*（负面也禁 score_1/2/3）；Turbo/Base 正面加 score_9,8,7、负面加 score_1,2,3
2. 选基线模板 -> A 最简 / B 标准 / C 手部重 / D NSFW / E 空 / F NL
3. 扫正面提示词 -> 按问题预测表追加对应负面
4. 严重问题 -> 加权
5. 检查冲突 -> 要黑条就不能负面加 bar censor；要彩色就不能负面加 watercolor
```

**Fixed prefixes:**
- Positive opener (AES 默认): `masterpiece, best quality, [safe/sensitive/nsfw/explicit], `
- Positive opener (Turbo/Base): `masterpiece, best quality, score_9, score_8, score_7, [safe/sensitive/nsfw/explicit], `
- 负面用 CLIPTextEncode 节点写上述模板；也可在正面写 `(tag:-权重)` 负权重语法。
### 权重语法

- Anima 对低权重不敏感，建议 `(tag:1.5)` 或更高；`(tag:0.8)` 几乎无效
- 多层括号 `(((tag)))` ≈ 强加强；负权重 `(tag:-1)` 压制（如 `(english text:-1)`）
- 画师权重 `(@artist:1.5)` 加强、`(@artist:0.5)` 减弱调色
- 画面太平均/烂大街 -> 加 `anime coloring`，或降 LoRA 强度到 0.7-0.9
### 构图/视角/光线（按气质/用途选）

**方法论：** 视角按气质/用途（立绘=upper body/looking at viewer，场景=wide shot，NSFW=按体位，动态=dutch angle/pov）；构图按主体（角色=shallow depth of field/portrait/centered，动作=dynamic pose/foreshortening/perspective，多角色对比=bust chart/height difference）；光线按气质（暗黑=chiaroscuro，治愈=soft daylight，热血=dramatic lighting，神秘=atmospheric/volumetric/god rays，轮廓=rim light/backlighting）。

**高频 8 tag：** `dutch angle`、`from above/below/side/behind`、`pov`、`shallow depth of field`、`dynamic pose`、`looking at viewer`、`cinematic lighting`、`rim light`。动态效果：`motion blur`/`motion lines`/`impact lines`/`afterimage`/`midair`/`contrapposto`/`ass ripple`/`bouncing breasts`。

**前景遮挡关系（笼子/栏杆/窗框/玻璃后，通用规则）：** 角色处于前景物体之后时，模型常把挡在前面的物体**透明化/凭空消失**（鸟笼正对镜头的栏杆不画）。不能只写"她在 XX 里"——要明确遮挡：①透过敞开的门/开口看（`seen through the open cage door, bars behind her` / `framed by the open door`）；②栏杆/栅栏放角色后方（`cage bars behind her, none across her body`）；③前景物体只框边缘（`cage frame at the edges framing her, open center`）；④或负面加 `transparent bars, missing cage bars, bars across her body`。要"框住她"用②③，要"从门/窗外窥视"用①。
### 多角色场景

**关键：每个角色必须单独描述外观 + 动作，否则串角。** 每角色独立四件套 + `girl:`/`boy:` 前缀锚定描述块。写法 2（官方例）：
```
girl: red hair, long hair, smile, grin, arm up, hand on own face,
girl: silver hair, twintails, outstretched arm, pointing, shouting,
girl: white hair, short hair, clenched hands, ganbaru pose, hands up, open mouth,
[series],
highres, absurdres, masterpiece, best quality,
```
**防串角纪律：** 每个角色完整外观（发/眼/肤/体型/服饰）写在**一个连续段**内，不散落不同行；用 `girl 1:`/`girl 2:` 或 `girl:`/`boy:` 前缀锚定，模型靠前缀区分角色边界；**不要在同一行混写两角色标签**（`girl red hair, boy blue eyes` = 串角高发）；`solo focus` 时非聚焦角色只写局部（`faceless male, pov hands`），不写完整外观；同发色用长度/发型/配件显式区分（`girl: long black hair` / `boy: short black hair`）；负面加 `cloned face, extra limbs, extra arms` 防复制/融合。
常用标签：`2girls`/`3girls`/`4girls`/`multiple girls`, `height difference`, `solo focus`, `interaction`；互动：`arm hug`/`cheek-to-cheek`/`looking at another`/`holding hands`/`kiss`/`cooperative paizuri`/`cooperative fellatio`。
### NSFW 专用指导

> 生成 NSFW 内容时，用 `anima-nsfw-prompt` skill 的解构方法优化本 skill 模板里的 NSFW 片段——**是补充/优化，不是路由到另一套模板**。提示词结构/模板/负面/标签库/安全等级/bar censor 全部仍用本 skill；nsfw skill 只补其没有的：NSFW 事件解构法（色气核心分析）、XP 方向参考、氛围 tips、常见坑。**生成长篇黄漫/套图（>10p）时**，先用 `anima-doujin-plan` skill 做剧本+分镜设计，产出分镜表后再用本 skill 逐页写提示词。

**构图模式：** ①分段式（模板 B）最常用；②`faceless male` 搭配：`1boy, faceless male, hetero` + `(faceless male:0.7), (male is nude:0.7), (small penis:0.5)`；③POV：`pov`/`pov hands`/`pov chest`/`pov doorway`；④只需女方身体时 `1girl` + `faceless male hands` 比 `1boy` 更安全（异性 NSFW 从不使用 2girls，2girls 只用于百合），除非两角色都需要完整身体；⑤bar censor 见安全节；⑥x-ray + cross-section 例子少、效果不稳定慎用。

**动作标签：按事件分层选（体位/动作/效果/特殊玩法/身体细节），完整词表查 Gelbooru。** 常用：体位 `missionary`/`cowgirl position`/`reverse cowgirl position`/`doggystyle`/`spoon position`/`paizuri`/`fellatio`/`deepthroat`/`buttjob`；动作 `deep penetration`/`penetration`/`creampie`/`oral`/`groping`/`skirt lift`/`panty pull`；效果 `ass ripple`/`bouncing breasts`/`jiggle`/`trembling`/`arching back`/`stomach bulge`；身体 `nipples`/`erect nipples`/`puffy areolae`/`large areolae`/`cameltoe`/`skindentation`；特殊玩法 `restrained`/`tape gag`/`arms behind back`/`chained`/`mass orgy`/`mmm threesome`。表情 `ahegao`/`ohogao`/`fucked silly`/`rolling eyes`/`uneven eyes`/`heart eyes`/`bedroom eyes`/`embarrassed`。

**表情三档字面库（长篇 doujin 按堕落进度选档）：** 抵抗/羞耻档 `teary eyes, biting lip, heavy blush, gritting teeth, furrowed brows`；沉醉档 `half-closed eyes, blush, open mouth, tongue out, drooling, heart-shaped pupils`；高潮档 `ahegao, rolling eyes, tongue out, heart-shaped pupils, drooling, sweaty, convulsing`。低级状态不配高级表情（heart-shaped pupils 只在接受/主动阶段后可用）。**终局空洞配方（doujin 终态页编码）**：`empty smile, half-closed eyes, heart-shaped pupils, light blush` + 自相矛盾收尾（`no expression`/`no resistance`）——"坏掉感"编码，可与其他终局单元（明喻/道具回收/光色句/conveying 主题句）自由组合。

**核心体液：** `cum`/`cum in mouth`/`cum on face`/`cum on body`/`cum on hair`/`excessive cum`/`saliva`/`drooling`/`pussy juice`/`pussy juice trail`/`sweat`/`shiny skin`/`very sweaty`/`oiled`。

**进阶技法：** 分段填充顺序（模板 B）= censor 顶行（`bar censor` 或 `(uncensored:3.0)`）-> 动作/朝向（`hetero`+体位+penetration）-> 角色+身体 -> 表情（独立段，眼部可重权 `(uneven eyes:4)`）-> 体液簇 -> HCG 特效块 -> 系列 -> 质量尾。HCG 特效子词库：`2way-afterimage, afterimage, motion blur, motion lines, impact lines, onomatopoeia, sound effects, speech bubble, japanese text` + 压制非日文 `(english text, engrish text, chinese text, korean text:-1)`。**tags+NL 澄清**：物体/身体部位空间关系模糊时，保留 tag 栈 + 追加一句 NL 锁定接触点（谁相对谁在哪、哪只手碰哪、`cheek against wall`/`hands on own knees`），否则模型自由发挥。**NSFW 专属负面**：`disembodied penis, floating penis, barbie doll anatomy, minor`；SFW 提示词应负面 `creampie, pussy juice, vaginal fluid` 防体液误入。
特殊风格触发词（可能是 LoRA 触发词，无对应 LoRA 时效果不确定，谨慎用）：`ar_v02`（动态肉感）/`deyes_v00`（失神眼）/`hentai_studio_quality`/`PosingDynamicsDaal`。
### 文字与 UI 元素

**字幕实操**：长句字幕易渲染成乱码/大字占版，叙事信息优先用视觉表达；必须字幕时保持短句（≤5 词）+ `english text "..."` + 底部小字定位。禁一段式对白/段落文字。
文字标签：`english text "..."/japanese text/chinese text/korean text`、`speech bubble`/`sound effects`/`onomatopoeia`/`moaning`、`text`/`title`/`subtitle`/`typography`。多语言控制：压制非日文 `(english text, engrish text, chinese text, korean text:-1)`；日文优先 `japanese text, sound effects` + 负面 `english text, chinese text`。UI/排版：`magazine cover`/`doujin cover`/`bust chart`/`height difference`/`logo`/`title`/`white border`/`recording photo interface`。
### 特殊风格

- **像素风**：`pixel art, limited palette, retro game cg, pc-98 style, flat shading, dithering, 16-bit` + `@capcom_vs_snk2`/`@motocross saito`。建议小画布 768x1024。空负面。
- **水彩/手绘风**：`watercolor, hand-drawn, freehand, loose brushwork, soft brush aesthetic, traditional media, muted palette, flat_color` + `@sw33t`/`@acky bright`。配模板 C 的 NL 句式骨架。
- **Superflat/装饰风**：`superflat, flat_color, vector art, city pop, @mucha, maximalist decoration, hand-drawn texture, non-symmetrical composition`。
- **线稿风**：`lineart, simple background, loose lineart, sketch` + `@imkay 3`。
- **Dataset tags（非动漫，罕用）**：`ye-pop` 开头+换行 -> pop/abstract；`deviantart` 开头+换行 -> 数字绘画。Anima 强项是动漫，仅特殊非动漫需求用。

**Pose 锚定气质（重要）：** 角色有招牌动作时，用 **pose tag 锚定气质 >> 表情 tag**。例：带贴脸招牌动作的病娇角色用 `hand on own cheek, head tilt, crazy smile, empty eyes` 比笼统的 `yandere, smile` 有效得多——病娇/傲娇/腹黑这类"气质靠微表情"的角色，表情 tag 传达不出，招牌 pose 才是锚点。有招牌动作的角色（贴脸病娇、持镰战斗少女、标志性姿态等）优先写 pose tag，放在 clothing 之后、expression 之前。

**风格提取：** 从已有图反向提取风格可用 WD14 Tagger 自定义节点或 `sorryhyun/anima-tagger`。
### LoRA 使用指导

**常用 LoRA 类别（具体文件按本地实际安装为准）**：通用画质增强 LoRA（AES+Turbo 可用，权重 0.5-1.0）、Turbo 加速 LoRA（**仅 Turbo**，0.8，4 步加速）、色彩风味 LoRA（0.6-1.0）、NSFW 增强 LoRA（0.8）、画师风格 LoRA（需配对应画师 tag，1.0）。

**决策流程：** 质量不够 -> 通用增强 LoRA 0.8；色彩平淡 -> 色彩 LoRA 0.6；NSFW 质量不够 -> NSFW 增强 0.8；要画师风格 -> 画师 LoRA + 对应画师 tag；要 Turbo 加速 -> Turbo 加速 LoRA 0.8 + 4 步（**仅 Turbo**，AES CFG 4 下会崩）；不需要 -> 不加（Aesthetic 自身已够好）。

**使用限制：** 低显存环境最多叠 2-3 个 LoRA（每个 ~100-200MB VRAM），**1-2 个最稳**；画师风格 LoRA **必须配对应画师 tag** 否则效果很弱（权重 0.3-1.0，可多画师各低权 0.3-0.6 混合）；**具体 LoRA 文件名和触发词与本地安装相关，不可直接照搬**。
调用：`本地执行器（如 anima_gen.py） --lora <名字>.safetensors`（多 LoRA 重复 `--lora`；权重默认 1.0，调权重需手动改 graph 或查源码）。
### 人物一致性（一次生图保证角色统一）

同一角色的多张图（立绘集/不同姿势/不同场景/分阶段套图）**靠提示词约束保证一次生图就一致**，不依赖 img2img 补救。核心：**每个外观维度都写具体值，禁止用概括词**。概括词（`beautiful`/`cute`/`elegant outfit`/`school uniform`）模型每次理解不同必偏离；具体值每次执行一致。

**维度封闭清单**：以下维度**逐项核对，每维一个具体值**，落脚本禁概括（缺一 = 该维每张随机）：身高 / 体型胸围 / 肤色 / 发型发色 / 眼色眼型 / 服饰每件 / 袜有无色长 / 鞋靴长型 / 发饰位色 / 眉形 / 武器唯一位置 / 贯穿道具四要素。
- **发型/发色**：`silver hair, very long hair, blunt bangs, side ponytail`（不是 `beautiful hair`）
- **眼色/眼型**：`purple eyes, detailed eyes, round eyes, large pupils`（不是 `pretty eyes`）
- **肤色**：`fair skin`/`pale skin`/`tan`/`dark skin`（不写会随机）
- **体型/胸围**：`slim, small breasts`/`petite, flat chest`/`medium breasts, curvy`（**必须写**，不写=每张随机）+ **身高比例**（`tall`/`petite`/`average height` 或相对参照 `height difference`——不写=每张乱定比例）
- **服饰（每件+颜色+具体款式）**：`white dress shirt, red ribbon, blue pleated skirt, black thighhighs`（不是 `school uniform` 泛称——模型可能理解为水手服/西装/衬衫不同款式）
- **武器（战斗角色必写）**：`wide short sword, silver blade`（**具体款式**，不是 `sword`/`weapon` 泛称）——武器**三态进固定块**（`in hand`/`on her back`/`on the ground beside her`），每页必选一态，事件不需要的页写"背景/角落/省略"而非消失；**唯一位置锁定**：选定一态后显式排除其余默认位——正面写死具体位置（`sword leaning against the counter`）+ 补 `no sword at her hip`，负面加 `sword on hip, scabbard, sword on back, duplicate weapon, double swords`；**状态用静态短句**（`sword leaning against the counter`，不是动作过程句——动作句=单帧多动作，禁时序链）；不写=模型自由补全（如职业角色"一会剑一会锤"）
- **腿足**：靴/鞋写死长短+样式（`ankle-length`/`knee-high`/`lace-up boots`）；**袜子维度显式**（`no socks`/`black knee-high socks`/`white thighhighs`——不写=模型默认补丝袜或漂移）；裸体/半裸页显式 `bare legs` + 负面 `stockings, pantyhose, thighhighs`
- **头部遮盖（修女/兜帽/面纱类）**：无头巾 = 正面 `hair fully visible, no head covering` + 负面 `headscarf, wimple, veil, hood`；有 = 写死具体款式（`white headscarf`）
- **发饰/配件**：锁**款式+颜色+图案**（`brown headband with triangle pattern`，不是 `headband` 泛称——泛称被渲染成白色发巾/额头绑带）；负面加 `bandana, white headband`
- **眉形/面部细部**：眉形入固定块（`thin eyebrows`/`sharp eyebrows`）；特写放大脸页追加**细部增强块**（刘海形状逐字复用 + 眉形 + 发饰位色，靠前放置）+ 面部负面 `deformed face, deformed eyes, bad pupils`
- **氛围/气质**：用具体 pose/表情锚定（`looking at viewer, gentle smile, soft lighting`，不是 `elegant mood`）

**一致性纪律：**
- **角色标准外观存为模板**：完整外观标签固定，每次复用一字不改。改姿势/场景/表情时外观不动。
- **所有维度写满**：即使某张图不强调某维度（如全身图不强调脸部）也要写——不写=随机生成，下一张对不上。
- **固定画师 + 外观标签，seed 随机**：同画师+同外观=风格一致；**不要固定 seed**（固定 seed 可能落在 bad point 导致异常输出，且提示词变化时同 seed 出奇怪画面）。**澄清**："不固定 seed"= 不要全本所有页共用一个 seed；长篇 doujin 的 `seed=base+页序`（每页不同）正是此原则的落地方式。
- **多人场景每角色独立四件套**（见多角色场景）。
- **分阶段套图特别纪律**：外观标签逐字复制，不能改写或省略。外观模板放最前，每张 prompt 从同一模板字符串开始，只改表情/动作/环境/光线部分。

**人物块两段式（长篇 doujin 用）**：第一段固定模板逐字复用（角色名+系列名+发色+发型+瞳色+`detailed eyes`+`fair skin`+体型+胸围+服装，12-15 tag 固定顺序，如 `1girl, [series], red hair, long hair, brown eyes, detailed eyes, fair skin, slim, large breasts`）；第二段"状态增量槽"只允许追加（`nude`/`cum on body`/`metal collar permanent`），禁止改写第一段。罕见角色名兜底：系列名在人物块和页尾各写一遍。

**泛称 -> 具体化对照（高频踩坑）：**
| 泛称（会偏离） | 具体化（一致） |
|---|---|
| `school uniform` | `white dress shirt, red ribbon, blue pleated skirt` |
| `casual clothes` | `white hoodie, denim shorts, sneakers` |
| `elegant outfit` | `black evening gown, lace trim, pearl necklace` |
| `armor` | `silver plate armor, pauldrons, leather belt` |
| `nice body` | `slim, medium breasts, fair skin` |
| `beautiful hair` | `black hair, very long hair, straight, blunt bangs` |
| `headband` | `brown headband with triangle pattern` |
| `small creature` | `waist-high to her`（显式相对高度） |
| `sword` | `wide short sword, silver blade`（具体款式） |
| `leather boots` | `brown leather boots, ankle-length`/`black knee-high boots` |
| `stockings` | `black knee-high socks`/`white thighhighs`/`no socks`（显式有无） |
| 高度 | `tall`/`petite`/`average height`/`height difference` |

**反例（会偏离）：** `a beautiful girl with elegant clothes`（每次发色/眼色/服饰全不同）、`cute anime girl`（纯随机）、`silver hair, purple dress`（漏眼色/肤色/发型细节/体型）、`school uniform`（每张理解不同款式）。
**正例（一次一致）：** `silver hair, very long hair, blunt bangs, purple eyes, detailed eyes, round eyes, large pupils, fair skin, slim, small breasts, half-elf, pointy ears, white and purple dress, ribbon, cross-lacing, detached sleeves, crystal pendant` -> 每个维度锁定，多次生图一致。
## Auto size / aspect ratio

边长必须是 **8 的倍数**（VAE），64 倍数优先。工作范围 512²–1536²。**AES fp16 在 896x1152 峰值 ~7.3GB**（8GB 显存接近上限可跑），768x1024 更安全（~7.1GB）；Turbo 可达 ~1536²。

| 内容 | 比例 | Size |
|---|---|---|
| 角色肖像/上半身 | 3:4 | `896×1152`（默认）|
| 全身角色 | 2:3 | `832×1248` |
| 头像/图标 | 1:1 | `1024×1024` |
| 场景/风景 | 16:9 | `1216×832` / `1280×720` |
| 角色+场景（横） | 4:3 | `1024×768` / `1152×896` |
| 加载横幅/超宽全景 | 21:9 | `1536×640` / `1536×576` |
## 画师策略
### 何时用/不用
- **高人气/经典/近年热门角色 -> 无画师**。Anima 训练充分，画师反而干扰还原
- **长篇 doujin（>10p）推荐双画师 `@mika pikazo, @redjuice`**（此组合在长篇案例中表现最佳；"高人气无画师"未在长篇验证，按该结论覆盖——例外条款，短篇/单图仍按上一条）
- **冷门角色/Anima 学不准** -> 画师辅助还原
- **需要特定风格**（水彩/superflat/像素/线稿）-> 对应风格画师
- **NSFW** -> 可用 NSFW 画师（见下方 NSFW 组）
- **无明确风格要求** -> 无画师
### 核心画师（按气质/风格匹配；其余靠持续补充）
- 温婉/清冷/精灵系 -> `@rella`（+ `detailed eyes, round eyes, large pupils` 眼部强化）
- 强个性/傲娇/腹黑/张扬 -> `@hiten`
- 神秘/氛围/魔女系 -> `@shirabi`
- 深色/哥特/冷调（灰调表征偏差）-> `@mochizuki kei`（Trigger 系，用对角色出彩）
- 热血/红色系/挑染 -> `@modare`/`@namie`
- 萌系 -> `@askzy`；少女向精致 -> `@yoneyama mai`（+ `normal neck, small head` 修正长脖子）
- 水彩/场景 -> `@sw33t`/`@acky bright`；superflat -> `@mucha`；像素 -> `@capcom_vs_snk2`/`@motocross saito`；线稿 -> `@imkay 3`
- NSFW -> `@cowani`/`@sogushstyle`/`@spd`/`@c0ff1ng`/`@shexyo`
### 关键结论
1. **高人气角色优先无画师** - 画师反而干扰还原。**注意：文中各模板示例配了画师 + score_* 是演示写法，实际生成按本策略——高人气角色去画师、AES 去 score_*，不要照抄示例**
2. **画师 tag 不一定忠实于画师本人风格** - 存在表征偏差（`@mochizuki kei` 实际产出 Trigger 系灰调，非本人柔和治愈）。不能凭本人风格预判，需逐例验证
3. **没有坏画师 tag，只有放错地方的画师 tag** - 偏差画师用在对的角色上反而出彩
4. **画师-角色匹配要到气质+配色级别** - 金发在灰调画风下偏色变粉，深色冷调角色才搭灰调
5. **不要降低画师权重来"修"还原度** - 降权 destabilize 风格锚点，滑向 2.5D。保持 1.0，用 feature tag 补被画师覆盖的特征（rella+`detailed eyes, round eyes, large pupils`）
6. **系统风格特征无法靠 seed-rolling 修复** - 脖子长是某些画师的签名特征。用相反 tag 补偿但注意 whack-a-mole，接受小瑕疵而非无限追修
7. **双画师定基调 + 还原画师高权重拉还原度** - 两个风格互补画师调"好看"基调，还原画师高权重（1.0-1.5）拉回。方向相反别混用
8. **低权鲜艳画师调色有效** - 配方 = 还原画师 1.0 + 鲜艳画师 0.5（低权画师提亮颜色不抢戏）
9. **病娇/微表情气质靠 pose tag 锚定 >> 表情 tag**（见特殊风格-Pose 锚定气质）
10. **多画师配方必须做拆解对比** - 逐个去掉画师验证是否真有贡献
11. **画师链用随机 seed** - 永远不从一个 seed 判画师好坏
12. **避免半写实/油画风画师** - `@wlop` 在动漫域效果不佳

Wildcard 文件名转 Anima 格式：去掉 `(name:w)` 权重包装，按需去掉 `(series)` 消歧，加 `@` 前缀（`ask \(askzy\)` -> `@askzy`）。
## ComfyUI parameters

默认 **Aesthetic fp16**；其他模型只改 model + steps + cfg + 质量词（见下），TE/VAE/sampler/scheduler/size/negative 结构全不变：
- **Aesthetic fp16（默认）**：model Anima Aesthetic（文件名按本地安装）-> UNETLoader (weight_dtype: default)；TE Qwen3-0.6B -> CLIPLoader (type: `stable_diffusion`)；VAE Qwen Image VAE -> VAELoader；Sampler `er_sde`|`euler`；Scheduler `simple`；**Steps 30 (30-50)；CFG 4.0 (4-5)**；负面不用 score_*；seed 探索随机/对比固定
- **Turbo fp16（需明确指定）**：model Anima Turbo；Sampler `er_sde`（官方默认）|`euler`（更稳）；**Steps 10 (8-12)；CFG 1.0**（CFG-free，>1 崩）；质量词加 `score_9, score_8, score_7`，负面可加 score_1/2/3
- **Base**：model Anima Base；Steps 30；CFG 4.0；加 `score_*`

**Pitfalls:**
1. **CLIPLoader `type=stable_diffusion` 正确** - ComfyUI 自动识别 Qwen3-0.6B 路由到 `anima.te`/`AnimaTokenizer`，下拉无 `anima` 选项不要找。TE 是 safetensors，用原生 CLIPLoader，**不是** GGUF CLIP loader
2. **Turbo CFG 必须 1.0**（AES 用 4.0，不要混淆），>1 崩
3. **Sampler 不唯一** - `er_sde`（官方默认）和 `euler`（Turbo pick）都行，差异是风格不是对错
4. **用文本方式验证输出** - 验证图用文件大小 + PNG `tEXt` `prompt` 块，由用户看图
5. **画师-角色配色冲突**可负面针对性加颜色 tag（如金发角色在灰调画风下偏粉，负面 `pink hair, red background` 可能缓解），但根因是画师选错，换画师优于硬补偿
6. **分段式提示词的换行会原样传入 ComfyUI** - 本地执行器 `--prompt` 支持多行（stdin 管道或 `$'...\n...'`），确保 `\n` 保留，不要把分段式压成一行——换行是分段式语义结构的一部分
## Naming convention (mandatory)

ComfyUI 默认 `ComfyUI_00001_.png` 无用。设 `filename_prefix`：`anima_<subject>[_variant][_batch]`。subject=1-3 个小写英文词下划线连接（`heroine_portrait`/`forest_scene`）；variant=画师或版本（`mika`/`rella07`）；**batch 在对比轮次必加**（`_b2`/`_b3`/`_0724`，否则每轮覆盖同名文件无法区分）。总长 <30 字符，别放长画师链。例：`anima_heroine_rella_b2`。
**Compare runs**：A/B 测画师/参数时传 `--subfolder _compare/<subject>`，按 `_compare` 前缀路由到 `output/_compare/YYYY-MM-DD/`，生产图不受影响（生产图无 `--subfolder` 落 `output/YYYY-MM-DD/`）。自动归档脚本按 mtime 移动、按前缀切分测试/生产。
## Generate via local ComfyUI

**首选便捷执行器**（如本地的 `anima_gen.py`，封装 submit+poll+verify，CLI：`--prompt --negative --width --height --seed --prefix ...`），缺失时 fallback 官方 workflow（拖 workflow `example.png` 进 ComfyUI 加载）或手建图（节点：UNETLoader / CLIPLoader / CLIPTextEncode x2 / VAELoader / EmptyLatentImage / KSampler / VAEDecode / SaveImage，API 格式 `{"prompt": {node_id: {class_type, inputs}}}`）。

流程要点：
1. **Probe**：`GET http://127.0.0.1:8188/system_stats`；未启动则用便携版启动：`python_embeded\python.exe -s ComfyUI/main.py --windows-standalone-build --listen 127.0.0.1`（低显存环境加 `--lowvram` 防 VAE decode OOM；先杀僵尸 python.exe 清 VRAM）
2. **Submit**：`POST /prompt` `{"prompt": graph, "client_id": "anima"}`。成功 = `{"prompt_id":..., "number":N, "node_errors":{}}` - **空 `node_errors` = 验证通过**（键存在不算错），非空才是真失败
3. **Poll**：`GET /history/{prompt_id}` 直到 `status.completed`；读 `outputs[*].images[*]`
4. **Locate**：文件先落 `output/`，自动归档脚本会移到 `output/YYYY-MM-DD/`；根路径找不到则 glob `output/**/<filename>`
5. **Verify（纯文本）**：`stat` 文件大小 + 解析 PNG `tEXt` `prompt` 块确认参数。**不要 Read 图片**
6. **Report** 最终路径+参数给用户，让他们看图
## Output structure (for every Anima request)

1. **Request** - restate what the user wants.
2. **Prompt** - 完整 Danbooru-tag 正面 prompt 一个可复制块；负面独立块。说明用了哪个模板（A-C）和哪个负面模板（A-F / NEG_*）及原因。
3. **Params** - size（含比例理由）、sampler/steps/CFG、seed、画师链（含理由）、filename prefix。
4. **Generate** - ComfyUI 在跑则提交并报告保存路径；否则告知如何启动。没有文件落盘就不算成功。
