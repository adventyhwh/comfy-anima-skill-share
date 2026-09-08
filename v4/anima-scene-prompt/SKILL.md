---
name: anima-scene-prompt
version: fourth-preview-slim
description: Activates when the user wants to generate scene/background/environment/map art assets with Anima — pure scenes with no main character, or background art where people are only accents (game backgrounds, loading screens, map tiles, environment concept art, atmosphere/mood illustrations). Turns a Chinese/English description into an Anima-optimized scene prompt using layered natural-language or tag-stack structures, and submits to local ComfyUI (Aesthetic fp16 default). Route character-focused art and in-scene NSFW behavior to anima-prompt; route pure scenes/backgrounds here; when characters are only accents but the environment dominates, use together with anima-prompt.
---

> **前置依赖：使用本 skill 前必须同时加载 anima-prompt**（NEG_* 负面基线、ComfyUI 参数、生成流程、出图踩坑都在那里；本文件只写场景特化内容，冲突以 anima-prompt 为准）。

## When to Activate

Activate when the user asks to generate **scene/background/environment art**（场景/背景/环境美术资源）and:
- The subject is the **environment**（landscape, cityscape, interior, architecture, nature）, not a character
- People, if present, are **background accents**（anonymous crowd, distant figures）, not the focus
- Use cases: game background, loading screen BG, map tile, level backdrop, environment concept art, world-building illustration, atmosphere/mood piece

**Routing:**
- **Pure scene / background / environment / map resource** -> this skill（`anima-scene-prompt`）
- **Character-focused**（portrait, character sheet, character in scene）-> `anima-prompt`
- **Character in a detailed scene, but character is the focus** -> `anima-prompt`（用模板 A-混 或 C）
- **Character only as scale accent / environment is the main subject**（宏大废墟中的小人物、氛围背影）-> **本 skill 与 anima-prompt 并用**（场景层次/比例/画师用本 skill，角色块写法用 anima-prompt）
- **Character NSFW behavior in a scene** -> `anima-prompt`（+anima-nsfw-prompt），本 skill 不接
- If ambiguous: if you'd describe the character first -> character art（`anima-prompt`）；if you'd describe the place first -> scene art（here）。

Do not activate for character portraits, character sheets, video, 3D models, or non-Anima requests。

## Model basics

Same as `anima-prompt` — Anima Aesthetic fp16 (default), CFG 4, 30 steps, er_sde/euler, qwen_3_06b_base + qwen_image_vae。See `anima-prompt` skill for ComfyUI parameters, pitfalls, and the full generation flow。This skill only covers scene-specific prompting。

## 需求拆解

用户要场景图时通常给得很简短（如"雨夜街道"、"精灵森林"、"游戏加载背景"）。**不要问问题，自动推断**：
1. **用途**：游戏背景/loading screen/地图 tile/概念图/氛围图 -> 定比例和风格
2. **环境主体**：建筑/自然/室内/废墟/科幻 -> 提取关键元素
3. **情绪基调**：孤独/热闹/神秘/压抑/宁静 -> 决定光线和色调（情绪是场景图的核心）
4. **时间/天气**：日/夜/黄昏/雨/雪/雾 -> 强影响光线
5. **是否有人**：无人物 / 背景点缀人物 -> 决定是否加人物标签

## 场景提示词结构

Anima 场景图有三种可靠写法，按风格选：

### 结构 1: 自然语言分层式（氛围场景 / 水彩 / 插画风）
适用：有情绪、有氛围的场景，水彩/手绘风，概念图。
```
[quality + safety + year], [style tags], [@artist]. [分号串联的画面层次: 调性→光线→环境主体→点缀人物→细节→景深→视角→质感→情绪收束]
```
**分号分层法（核心技巧）：** 用分号串联画面层次，每个分号片段聚焦一个层次，不分句。从 168 个例子提取的层次顺序：
1. 整体调性 + 色板（`A vibrant yet muted palette dominated by soft pastels...`）
2. 光线作用（`natural diffused sunlight casting sharp shadows across...`）
3. 环境主体（建筑/地形/植被，详细描述材质和结构）
4. 点缀人物（如有，匿名/不聚焦：`anonymous figures navigating...`）
5. 环境细节（材质磨损/植被/杂物/招牌）
6. 景深/空间感（`shallow depth of field blurring background...`）
7. 视角/构图（`low-angle tilted perspective enhancing depth...`）
8. 渲染质感（`hyper-detailed textures, delicate brushwork...`）
9. 情绪收束（`overall mood is tranquil, melancholic, dreamlike...`）

**水彩/手绘 NL 句式骨架**：见 anima-prompt 模板 C 的水彩骨架（场景图是水彩风主战场，完整骨架在那里）。

**"反摆拍"美学（从补充例子归纳，同 anima-prompt 模板 C 反摆拍）：** 场景图中人物不看向镜头是高频美学追求，用否定式定调表达：
- `no direct eye contact with the viewer`
- `characters focused on their own actions rather than deliberately looking at the camera`
- `non-staged realism, unposed, candid`
- `natural, unposed movements`
- 适合生活感/日常/叙事氛围场景，区别于"摆拍"的 `looking at viewer`
### 结构 2: 标签堆叠式（元素明确 / 概念图 / 游戏背景）
适用：元素明确、概念图、游戏背景——各元素按层次分组堆 tag，比 NL 更快更稳。
```
[quality + safety + year], [style tags], [environment: 建筑地形植被天气], [lighting], [composition], [depth], [atmosphere]
```
标签按层次堆叠，每类一组。
例（例子 14）：`masterpiece, best quality, absurdres, newest, majestic Japanese mountain shrine, ancient temple on a towering cliff, long stone staircase, torii gate, crystal-clear river, lush forest, cherry blossom trees in full bloom, falling sakura petals, moss-covered rocks, lanterns along the path, distant mountains, golden hour, dramatic clouds, warm sunlight, god rays, volumetric lighting, soft haze, cinematic composition, atmospheric perspective, highly detailed environment, painterly anime style, depth of field`

### 结构 3: 剖面/解剖图（cutaway / cross-section）
适用：需要同时展示建筑内部结构和外观的场景（店铺剖面、房屋截面、地下设施）。`cutaway`/`cross-section` 是标准 Danbooru 标签（从 135 例 base 例子中 2 例观察到，通用结构，非 base 专属）。
```
[quality + safety], cutaway, cross-section, [建筑类型], [外观特征], [内部可见区域: 房间/家具/人物活动], [多层结构: 上层/下层], [材质], [光线对比: 内部暖光 vs 外部环境光], [concept art / 2d illustration]
```
关键标签：`cutaway` / `cross-section` 锁定剖面视角；内部用 `warm lighting, glowing` 突出，外部用环境光（`overcast` / `rain` 等）对比；内部人物作为点缀（`chef, cooking` / `customer, sitting, eating`）点明功能。
例（例33 精简）：`masterpiece, best quality, safe, 3boys, scenery, cutaway, cross-section, outdoors, heavy rain, ramen shop built into steep hillside, stone stairs, moss, hanging lantern, noren, counter, bar stool, chef cooking, customer sitting eating, chimney smoke, wooden deck, lower level metal door, warm lighting vs grey rainy exterior, cozy atmospheric, high contrast, concept art, 2d illustration, flat color, thin lineart`

**结构选择：** 氛围/情绪/水彩风 -> 结构 1；元素明确/概念图/游戏背景 -> 结构 2；剖面/内外同框（建筑内部+外观同画）-> 结构 3。不确定用结构 1（更稳）。

## 背景人物点缀

**无角色纯场景 vs 多人物点缀：**
- 纯环境（无人物）：`no human figures present` / `devoid of characters` / `no central figure`
- 多人物点缀（匿名/不聚焦）：用泛称，不写角色名/外观

人物只是背景点缀时（不聚焦、不描述具体角色）：
- 用泛称：`anonymous figures` / `people in the background` / `distant figures` / `passersby`
- **不写角色名、不写具体外观**（发色眼色等），否则模型会把人物当主角
- 加 `no focus character` / `environment focus` 强调环境为主
- 人物小且模糊：`blurred figures in background` / `small figures in distance`
- 人群：`crowd` / `bustling street` / `people walking`

**场景图人群边界：** 场景图背景人群按"设计人群"处理，不套角色图排空（排空边界见 anima-prompt『负面提示词-排空纪律』）；人群个体动作/视线链写法见 anima-prompt『公共场景与人群叙事纪律』。

## 场景画师

| 画师 | 风格 | 适用 |
|---|---|---|
| `@sw33t` | 水彩/手绘，氛围感强（**仅 base 实测，AES 未验证**——AES 首用必须同 seed 验证 1 张再批量；未验证时水彩场景用 `@acky bright` 或 watercolor/hand-drawn 风格 tag 组兜底，毒点名单见 anima-prompt 画师策略） | 氛围场景、雨景、城市、聚会 |
| `@acky bright` | 水彩+线稿，叙事怀旧 | 怀旧场景、室内、街机厅、商店 |
| `@shinkai_makoto` | 新海诚风，光影通透 | 天空、云、自然光景、黄昏 |
| `@mucha` | 装饰风+superflat | 装饰性背景、海报背景 |
| 无画师 | Anima 默认 | 简洁背景、概念图、地图资源 |

场景画师选择不如角色那么关键——场景图更多靠描述本身。不确定时不用画师。

## 场景构图与视角

场景图比角色图更强调空间感和景深：
- **视角**：`wide shot` / `very wide shot`（极高频）/ `bird's eye view` / `aerial view` / `low-angle` / `vanishing point`
- **空间感**：`depth of field` / `atmospheric perspective` / `vanishing point` / `foreshortening` / `leading lines`
- **构图**：`cinematic composition` / `rule of thirds` / `symmetrical composition` / `framing`
- **光线**：`golden hour` / `volumetric lighting` / `god rays` / `dappled sunlight` / `backlighting` / `atmospheric haze` / `rim light`
- **氛围**：`peaceful atmosphere` / `melancholic` / `mysterious` / `dreamlike` / `oppressive silence` / `nostalgic`
- **天气/时间**：`rain` / `fog` / `snow` / `night` / `dusk` / `dawn` / `overcast` / `clear sky`

## 比例

场景偏横向：
| 内容 | 比例 | 尺寸 |
|---|---|---|
| 场景/风景 | 16:9 | `1216×832` / `1280×720` |
| 桌面壁纸 | 16:9 | `1472×832` |
| 游戏加载界面/横幅 | 21:9 | `1536×640` |
| 超宽全景/卷轴背景 | 21:9 | `1536×576` |
| 地图 tile / 方形背景 | 1:1 | `1024×1024` |
| 竖屏场景/手机壁纸 | 9:16 | `768×1344` |

## 负面提示词

同步源：`anima-prompt` 负面节（NEG_* 基线，冲突以其为准）。**AES 正负面均不加 score_***（score_9/score_1 等仅 Turbo/Base 用）。场景图速查：
- **标准（AES 默认）**：NEG_CORE 基线（`worst quality, low quality, artist name, blurry, jpeg artifacts, bad anatomy, bad hands, missing fingers, extra digits, fewer digits, fused fingers, watermark, signature, text, 3d, realistic, extra limbs, mirror, reflection, duplicate, futanari, gay, yaoi, shemale, femboy`）——**纯场景/雨景/水面删 `mirror, reflection, duplicate`**（反射纪律只防"含第二角色/多手页"的倒影重复肢，雨夜街道倒影/水面镜像是想要的美学，压掉=误杀，同 anima-prompt 删误杀项纪律）
- **背景点缀人物多**：NEG_CORE 基础上加 `cloned face`——**不加 `multiple people`、负面不压 `strangers/bystanders/crowd`**（场景背景人群=设计人群豁免，渲染成匿名剪影，见 anima-prompt 排空纪律）
- **氛围/水彩**：空负面（避免干扰风格，anima-prompt 明确水彩/氛围场景用空负面）或 A 最简（NEG_CORE 头部：`worst quality, low quality, artist name, blurry, jpeg artifacts`）

## 复杂场景模板（成品级 / 自动化美术素材）

高质量成品或自动化生成时，用结构 1 的完整版：9 层齐全，每层 1-2 句。**情绪收束必须有**——所有视觉元素向情绪收拢。完整版=结构 1 的 9 层（见上"结构 1"节）。

触发：自动化美术素材生成、高质量成品、电影感/叙事感场景。对话简短测试时可降级为结构 2（标签堆叠）快速迭代。

## 生成流程

ComfyUI 参数、提交、验证流程同 `anima-prompt`。用你的 ComfyUI 提交脚本提交，`--prefix anima_scene_<subject>`。

**场景图 vs 角色图的关键差异：**
- 场景图不加 `1girl`/`1boy`（除非有点缀人物）
- 场景图强调 `wide shot`/`depth of field`/`atmospheric perspective`
- 场景图画师偏水彩/氛围（`@sw33t`/`@acky bright`），角色图画师偏还原（`@rella`/`@shirabi`）
- 场景图比例偏横向，角色图偏竖向

## Output structure

1. **Request** - 场景需求重述
2. **Prompt** - 正面/负面分块
3. **Params** - 尺寸（横向为主）、结构（1 或 2）、画师、filename prefix（`anima_scene_<subject>`）
4. **Generate** - 提交 ComfyUI，报告路径
