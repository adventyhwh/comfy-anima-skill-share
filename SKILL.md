---
name: anima-nsfw-prompt
version: share
description: NSFW特化补充 for anima-prompt skill. ONLY triggers when generating NSFW/sensitive/nsfw/explicit/erotic/色情/黄色/R18/勾人/露骨/成人/H场景/黄油/eroge/hentai content. Optimizes the NSFW fragment already inside anima-prompt (does NOT route to a separate template - templates/negative/tags/censorship all stay in anima-prompt). Provides what anima-prompt lacks, deconstruction-focused: NSFW event deconstruction method (erotic-core analysis: find-core -> core-to-visual -> core-nature-posture -> dual-axis -> differentiate -> innovate, generalizes to ANY event not just hypnosis), event-to-visual-anchor derivation (core-nature -> anchors, non-exhaustive), atmosphere tips, common pitfalls, and a few net-new NSFW artists. Lean supplement, not a competing template.
---

## 定位

以 `anima-prompt` 为主，本 skill 只补它没有的 NSFW 特化内容，不路由、不另起模板；结构/模板A-C/负面词/一致性/标签库/安全等级/bar censor 全用 anima-prompt 的。以解构为主：①事件解构（色气核心分析法，会推理任何事件怎么画）②氛围tips ③事件→视觉锚点推导 ④常见坑 ⑤画师补充。**分阶段套图级别**：按安全等级 sensitive 起步逐级递进到 explicit，最后 nsfw 余韵收尾。

## NSFW 事件解构（色气核心分析法）

写提示词前先想清色气核心——核心决定视觉选择，不是反过来。五步按需用，不拘顺序：
**① 找色气核心** -- 这个事件的兴奋点是什么心理/关系机制，不是"画什么"是"为什么色"。核心 = 去掉就没色的那个东西；道具/服装是载体不是核心本身。例：催眠=意志被绕过后的恍惚 trance（不是怀表本身）。
**② 核心转视觉** -- 区分"核心视觉"（直接表现核心，去掉就看不出主题）与"装饰视觉"（锦上添花）。例：奴隶化=支配关系建立+意志屈服 -> 核心视觉 `empty eyes`(意志消失)/`collar`(身份符号)/低头(服从姿态) -> 装饰视觉 `chains`/`leash`；只画装饰不画核心视觉 = 看不出主题（奴隶化只画 chains 不画 collar/empty eyes = 只像束缚）。
- **状态轴**：事件是动态过程，每状态有对应眼神，选错=叙事崩。奴隶化：反抗(glaring)->屈服(teary)->崩溃(hollow/blank，精神消失)->奴役(empty)。崩溃态用 hollow 不用 half-closed（后者暗示身体疲惫；但 half-closed 在催眠语境=梦游感，按语境取义勿混档）。
**③ 核心性质定姿态** -- 三种性质决定身体姿态语言（避免画错事件）：
- **精神性核心**（催眠/恶堕/常识改变）-> 姿态看意志去哪：抽离（催眠/洗脑，精神离场）-> 瘫软/无力/头歪手垂；反转迎合（恶堕，精神仍在但转向主动）-> 主动/渴求/弓身/手迎；身体冻结（时间停止类）-> 僵直/外力定格/无法动弹。不能一律瘫软——瘫软只对"精神抽离"成立，恶堕用瘫软会抹掉"主动堕落"核心、读成催眠。
- **身体性核心**（发情/强制高潮/苗床）-> 紧绷/抽搐/弓背（身体被冲击）；**关系性核心**（奴隶化/束缚/强制服从）-> 端正跪服/低头/固定姿态（主动服从或被固定）。
- **滑坡警示**：精神性最易滑向身体性视觉（催眠画成 ahegao+convulsing = 像强制高潮）。精神性用"松弛+恍惚眼+梦幻表情"，不用"紧绷+抽搐+高潮表情"。催眠恍惚眼是 `glazed eyes`（梦游感），不是 `empty eyes`（洗脑终态）。
**④ 双轴（避免只画精神不画H）** -- 核心轴（为什么色）× 行为轴（怎么色：前戏->口交/手交/足交->插入->高潮）。很多核心本身不含性行为（催眠核心是 trance 不是性），只画核心轴 = 只有精神没有 H。核心轴定表现方式、行为轴定画什么：同一 `fellatio` 配 `glazed eyes`（催眠=恍惚梦幻执行）和配 `heart-shaped pupils`（发情=主动渴求）画出来完全不同。若核心本身即性行为（如发情），两轴重合不必硬拆。
**行为轴两原则**：①**程度匹配阶段**——强迫/控制类事件早期用轻行为（抚摸/摆姿势/窥视/言语羞辱），意志被摧毁后才到重行为；催眠初级阶段（刚入控）用轻的人偶感（命令摆姿势、机械手交），深度恍惚才到口交/插入。②**行为贴合核心**——哪个行为最能放大该核心色气就用哪个，不一定是插入：人偶感配机械手交/摆姿势命令，屈服感配跪式口交/足底舔，身份坠落配当众使用/日常化，失控感配自慰/求欢。乳交/腿交/足交/股间/乳首玩弄都可作主体或前奏。
**⑤ 写进模板** -- 解构结果落进 anima-prompt 模板 A-C：色气核心->模板C情绪收尾层（`evoking a chilling atmosphere of complete subjugation` 而非泛泛 `sexy`）；核心视觉->角色四件套表情/姿态（`empty eyes, kneeling, head bowed`）；装饰视觉->外观/道具标签；状态轴->分阶段套图每阶段选对应眼神不跳级。
### 叙事结构

分阶段 CG 推进按事件性质选：**转折型**（日常->触发->抵抗->★抵抗失败★->沉沦->终态，张力在溃败，适合奴隶化/女战斗员化等事件型，抵抗须显颓势）或**程度加深型**（同一状态持续加深无转折，张力在不可逆滑落，适合催眠/恶堕/淫纹等过程型）。可混用：女战斗员化=战败(转折)+改造(程度加深)。
**套图类型先分清**：①**同一事件差分**=同一场景同一时刻的变体（换表情/体位/角度），适合"一个H场景内逐步升级"；②**多次触发事件CG**=游戏剧情中不同时间点独立触发的H事件，整体构成角色弧线，适合"跨剧情的堕落/变化弧线"。两者结构不同，别混；设计多次触发时想：这是第几个触发点、剧情推进了什么。
**阶段过渡要自然**：尤其多次触发套图，阶段间是剧情跳跃（可能跨天/跨场景），过渡要有剧情逻辑支撑，不能光靠"程度加深"硬跳。问自己：上个事件结束后什么剧情条件触发了下一个？角色心理状态怎么变的？过渡不自然 = 感觉像差分硬凑阶段数。
### 创新

创新 = 核心轴不变下，在行为轴做变奏（不是换核心）。四方向按需组合：①**非常规行为配核心**放大色气（催眠+命令摆姿势；清醒play中途解除的反差；奴隶化+日常化当众）；②**混合核心**叠加（催眠+奴隶化=洗脑奴役，两锚点都要有）；③**行为递进变奏**（前奏/主体选最放大该核心的，见行为轴②）；④**贴合人物最重要**（傲娇的催眠比温顺更有张力，强角色的奴隶化比弱角色更勾人；贴合人物 > 追求新奇）。
### 相邻区分

标签重叠的事件靠**核心差异**区分，不靠标签差异。差异常落在某条**连续轴上的位置**——找到那条轴，相邻事件就分开了：
- **意识状态轴（催眠三态，最易混）**：**洗脑/脑死**=意识丧失（empty 人偶，没人在家，终态无递进）；**意识操控**=意识清醒、惊恐旁观身体被劫持（"我不想但停不下来"）；**催眠**=意志被绕过后的恍惚 trance（意识在场但被改变，梦幻顺从）。催眠色气在"恍惚中顺从"：意识一没（->洗脑）就没人感受失控；意识太清醒（->意识操控）又变惊恐旁观。眼神：洗脑=`empty`，意识操控=震惊睁大，催眠=`glazed`/半闭眼（梦游感）。
  - **scope**：三元组只覆盖催眠/失控簇。身体冻结类（时间停止：意识清醒但身体定格，区分轴是"身体冻结 vs 被劫持行动"，不是意识轴）或意志重对齐类（恶堕终态、卖淫沉溺：意识在场且转向主动）落在外面——按"找区分轴"原则另找轴，别当穷举图。
- 奴隶化（身份转换）vs 束缚（物理限制）vs 强制服从（行为层面）：看"自我"是否被摧毁重建。奴隶化有身份符号（collar）+ 状态演变；束缚可无身份转换。
- 恶堕（主动堕落）vs 常识改变（认知重构）：看"自我"是否在场。恶堕=自我选择放弃（有挣扎->放弃的过程）；常识改变=自我仍在但认知变了（觉得异常正常）。区分不清 = 标签混搭 = 画面主题模糊，先定核心再选标签。

## NSFW 氛围 tips

每张选 2-3 条配合，不堆砌：
1. **眼神>身体** -- 眼睛是 NSFW 画面最强色气锚点，选对眼神往往比身体更重要（眼神 tag 见主 skill 表情标签库 + 本 skill 状态轴）。
2. **循序渐进 + 终态标签一次性** -- 分阶段套图从正常状态逐步加深，终态/符号标签只出现一次：催眠 `spiral eyes` 只在入控格用一次，之后靠 `glazed eyes` 承托；`ahegao`/`heart-shaped pupils` 是高潮格的一次性符号，不是持续状态。
3. **触感与代入** -- `skindentation`（压痕/勒痕）是最有效触感标签；`pov, pov hands` 第一人称让观者"在场"。
4. **日常反差** -- 医院/超市/教室等非色情场景 + NSFW 元素，比直接色情更勾人。
5. **留白勾人** -- 特定场景下不露比露更勾人（`covered nipples, areola slip, cleft of venus, see-through`）。
6. **情绪递进** -- 羞耻 -> 觉醒 -> 沉沦 -> 余韵，递进比直接展示更有张力。
7. **视线-关系双向因果** -- 反摆拍只是防"看镜头"，不保证角色-角色/角色-道具关系成立。①**对话/对峙页必须显式排除镜头**：`her gaze directed at the merchant, not at the camera`（光写 "glaring at the merchant" 仍会画成看镜头）；②**对视=双向**：双方视线互指（`his gaze fixed on her, her gaze meeting his`），只给女方视线会画成错位；③**诱导道具=同框可见因果**：道具引入页必须"她看到道具"（`her gaze drawn to the glowing pendant, pupils dilating`，道具在她视线路径上）；④多角色逐角色指定视线，剪影无眼给朝向/近景表述（`leaning down toward her, his gaze fixed on her face`）；⑤**排空人群覆盖所有两人页**：判据"本页允许第三个角色吗"——不允许即 `no other people` + 负面 `strangers, bystanders, customers, crowd`，不只私密/服务页；群交/列队页才保留群众。
8. **道具生命周期** -- 诱导类道具（怀表/钟摆/仪式道具）完成叙事功能后即退场；只有持续性身份道具（collar/leash/淫纹）才全程保留。
9. **分阶段混用模板** -- 套图前段（设定/氛围/诱导）用模板 C 叙事 NL 更有气氛；后段（明确 H 行为）用模板 B 分段 tag-stack 更干净。

## 事件 → 视觉锚点推导

事件画不画得出主题，取决于核心视觉锚点是否到位（去掉就看不出主题的关键元素）。本节能用解构方法推任何事件，不是背标签库；动作/体位/体液标签查主 skill，不重复。锚点按核心性质类推：**关系性核心 → 身份符号**（项圈/标记等身份标识）、**身体性核心 → 体液+表情**（湿润/咬唇等身体反应）、**精神性核心 → 眼神+反差场景**（失神/空茫眼等）；无法从核心性质直接推出的非显然锚点，按具体事件显式补充（如战损、纹样）。

### 常见误区（三错型）

1. **缺锚点** -- 只写核心没写视觉：事件只靠载体道具（去掉道具看不出事件）-> 必须配核心视觉锚点（眼神/姿态/身体反应/场景元素，按核心性质推导）。
2. **直接上终态** -- 符号类标签（如 spiral 眼/夸张表情）全程使用 -> 事件被推成终态，且模型渲染不稳定（远镜头放大怪眼、颜色乱跳）——一次性符号仅用于对应阶段一瞬，之后靠稳定的承托标签（如 `glazed eyes`）；堕落过程类事件直接从终态开始 -> 没有递进感，要从羞耻/抗拒开始。
3. **姿态与核心性质不符** -- 用错姿态抹掉核心：主动迎合类事件用瘫软 -> 读成被动抽离，应主动/弓身/手迎（见核心性质③）。

**诱导道具呈现**：链式诱导道具必须写链（`pocket watch on chain`，不写链子会被读成粉盒/盒子）+ 朝向角色（`watch face toward her, watch back toward viewer`）+ 晃动用 `watch at apex of swing, motion lines`；完成诱导功能后退场（见氛围 tip8）。

## NSFW 常见坑

1. **手部多手** -- 避免自然语言描述双手做不同动作（"one hand... the other hand..."），用标签式 `hands clawing at sheets`（双手同动作），负面加 `extra hands, multiple hands`（详细手部负面查主 skill 问题预测表）。
2. **提示词过长** -- 氛围标签选 2-3 条配合，控制 700-800 字符；调味料不是主菜。
3. **场景/时间不一致** -- 分阶段套图统一场景和时间，靠角色状态变化推进，不要每张换光线色调。
4. **服装漂移 / 分阶段一致性** -- 外观模板（发/眼/肤/体型/服饰）逐字复制到每张，禁止改写或省略；服装状态只能按行为轴显式递进（着衣->敞开->褪），每阶段写死——不能 S2 敞开 S3 又拉上。眼部标签别为凹眼神把人推远硬塞：远镜头下 `spiral eyes`/大瞳孔会被放大成怪眼。
5. **歧义标签** -- `frozen` 会被理解为冰冻（冰块），时间停止用 `time stop, motionless` + NL（"body locked in place as if time stopped"）。能用更精确的 tag/NL 替代就别用有歧义的（NL 替换仅限消歧上下文句；关键特征（表情/姿态/道具）一律用 tag，见坑10）。
  - **5.1 `spiral` 歧义**：非入控页写 `spiral`（光效/图案）会触发圈圈眼——spiral → spiral eyes 是强关联。非入控页光源/光效用 `swirl, concentric glow, curved light trails`；`spiral eyes` 只留给入控页本尊。负面压 `spiral eyes, swirly eyes`。**光效量级控制**：光效/发光是氛围不是主体——写 `small glow, held in his hand`，禁 `filling the foreground`/`bathing the whole frame` 级描述（会把持道具行为挤出焦点、glow 渲染成全幅金圈）。
  - **5.2 明喻字面化**：叙事段比喻（`like a puppet`）会被模型字面渲染成真丝线。护栏：喻体不得落在角色身体/衣饰/肢体上（`like a trophy` 在角色之外安全）；禁 `invisible, string, glass` 类字面诱发词；明喻只放模板C情绪收尾层不放 tag 层。
  - **5.3 职业服饰默认配件**：`nun`/`knight`/`priest` 等职业词自带标志性配件（wimple 头巾/头盔/白领）——不需要时负面压 `headdress, wimple, veil, hood` 或正面 `no head covering`。"不要X"必须显式编码，不能只靠剧本口头说。
  - **歧义词/明喻的负面抑制**：正面用了可能被字面渲染的歧义/文学词，推测模型会画出什么（丝线/头巾/圈圈眼）主动压掉：**AES（CFG 4.0）直接加负面提示词**（负面节点有效）；**Turbo（CFG 1.0）必须用 NegPip 负权重放正面**（`(wimple:-1.5), (veil:-1.5), (puppet strings:-1.5)`，`extract_negative_weights` 自动拆到负面）——低 CFG 下负面节点几乎无效，只有正面负权重有效。
6. **外部手的归属** -- 描述外部角色手操作（脱衣/摆位/触碰）时，明确写 `faceless male hands`，且不要同时给女主手部动作标签，否则模型画出 4+ 只手。
  - **6.1 动作动词强度**：`groping` 渲染弱（揉胸画成托胸）、`lips around penis` 画成闭嘴露牙——关键动作加具象动词+状态：揉胸 `faceless male hands squeezing her breasts, fingers pressing into her flesh`；口交 `mouth open wide, lips sealed around him, cheeks hollowed, sucking`（cheeks hollowed 是口交标准姿态标签）。**接触判定类动作**：①**接触点命名显式身体部位，禁代词**——`her mouth around his penis` 不写 `around him`（代词被读成吻/舔衣服），补相对位置 `kneeling between his legs` + 目视 `looking up at him` + 体位指向 `his hips forward`；②**接触物必须露出**——口交/舔/手交写 `penis exposed, pants pulled down`/`male nude`（不写 = 模型按穿衣态画成隔裤吸/隔衣舔）；③**判定机位必做**——低位服务默认 `pov, from above` 俯视 + 她仰头回应（`from the side` 旁侧平铺最易歧义）；④规范动作标签 `fellatio, deepthroat` 必写；⑤负面压 `kiss, french kiss, lips pressed together`；⑥**同部位一状态**——嘴/眼/手每部位只能一个状态：`biting lip`（咬唇闭口）禁与 `tongue out/drooling`（吐舌张口）共存，互斥词混入 = 人体畸形；表情词性须与档位字面库对齐（`biting lip` 是抵抗档词，禁入沉醉/高潮行）。
  - **6.2 口交页禁用 `gritting teeth`**：它是"画出牙齿"的直接诱因。口交期牙齿情绪用 `humiliated expression, furrowed brow, tears, drool at the corner of her mouth` 替代。
  - **6.3 体型比例显式化**：异种族/配角体型用**显式相对高度**（`waist-high to her, reaches her hip`）或参照物，禁 `small`/`tiny` 笼统词（被字面执行成迷你）。多角色页写 `height difference` 时同样用相对高度句锁定。
7. **冻结态表情锁定** -- 时间停止下角色表情锁定在定格瞬间，不能新增（不能 mid-freeze 皱眉/流泪；眼泪必须是定格前就有的、冻在脸上）。
8. **临床标签去色气** -- `medical examination` 作为动作 tag 会产生过于临床的结果（像真看病）。用 setting 标签（`clinic interior, examination table`）+ 具体动作 tag（`spread legs, restrained`）替代，保留色气。
9. **自定义道具一致性** -- 叙事核心依赖非现实道具时，用简单可描述的形状（`silver ring, round purple gem`）而非复杂描述（`serpentine curves with etched runes`）；复杂道具跨阶段会漂移或消失，生僻词不在编码器词表内。**五要素锁定**：形状+大小+位置+颜色+表面细节各定一个固定词（`small round pink gem` 大小+形状+颜色 + `on a cord around her neck` 位置 + 表面细节如表盘 `roman numeral dial`），逐字复用；发光/状态词只用一个（`glowing soft pink`），不写 bright/swinging/bouncing 波动词、布袋词（sachet）——都会导致大小/位置/颜色失控。**归属双侧编码 + 发光色锁定**：多人页道具/武器归属写"正面归属句 + 反面排除句"（`his hand raising it, her hand off the chain`——faceless 剪影无持物手时模型把道具归给画面唯一有手的人）；同帧发光物 ≤1、发光色独立于环境光（`pink gem glowing pink against warm golden light`），神圣误读负面 `halo, divine glow, holy aura, golden gem`。
  - **9.1 道具可见性裁决**：机位使必含道具/部位不可见时（背面看不到胸前饰品）——三选一：①省略不画（看不到没必要画，主手段）②分版图 inset（模型只擅长正面，背面属性需展示时）③换机位；**禁挪位**（破坏位置要素）。
10. **表情用 tag 不用文学描述** -- `a vacant serene half-smile` 模型无法解析，用 Danbooru tag `empty smile, half-closed eyes, light blush` 才稳定。生僻文学词（serpentine/vacant/serene/etched）效果不确定，编码器可能不认识。原则：**角色关键特征（表情/姿态/道具）一律用 tag，NL 只补氛围/情绪/关系**。
11. **双人行为无男方处理** -- 含双人行为 tag（cowgirl/fellatio/sex/missionary 等）时必须明确男方处理方式（`1boy, faceless male` 或 `faceless male hands` + 接触位置 或 `pov` 或 `silhouette`），否则模型自行生成完整男性角色，导致串角和画面失控。

## 多页制作 checklist（生成前逐条扫）

- [ ] 全质量标签 + 画师（`very aesthetic, amazing quality, anime coloring, intricate details`）
- [ ] Template B setting 段充实（不只 "bedroom, warm light"）
- [ ] 背景人物特征不与主角冲突（用 silhouette 或不描述外观）

## NSFW 画师决策

不确定时 -> 无画师，Anima 自身 NSFW 能力已足够。**氛围/叙事/终态段（模板C）优先加画师锚定风格**（`@mika pikazo, @redjuice` 效果优于无画师；H 动作段模板B 无画师即可）。主 skill 已收录 NSFW 画师（@cowani/@sogushstyle/@spd/@c0ff1ng/@shexyo）及配套 LoRA，查那里。本 skill 仅补充几个特化方向：
| 需求 | 画师 |
|---|---|
| 场景叙事 HCG | `@x1p4early2026` |
| 表情特写/失神 | `@k4nz4r1n` |
| 日常色气/透视 | `@haoni` |

社区多画师串配方**不可照搬**，使用前必须逐个去掉画师做拆解对比验证贡献（详见主 skill 画师策略）。
