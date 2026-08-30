---
name: game-implementation-architecture
description: Gradually derive an Implementation Architecture suitable for long-term, modular development with Codex from formal large-scale game design documents. Use it for system identification, module boundaries, shared systems, State Ownership, Dependency Maps, Interface Contracts, runtime state, flow orchestration, UI Flow, Save System design, development indexing, and minimum-context planning. It is intended for projects where the game design already exists but a reliable implementation architecture has not yet been established, or where an existing architecture needs to be reviewed, corrected, or extended. Do not use it to invent game rules that are not defined in the official project documentation.
---

# Game Implementation Architecture

## 目标

将正式游戏设计逐步转换为可供 Codex 长期开发、扩展、测试和维护的 Implementation Architecture（实现架构）。

重点不是一次性设计出“看起来完整”的架构，而是通过：

正式资料
→ 反推
→ 初步架构
→ 场景验证
→ 发现问题
→ 修正
→ 再验证

逐步收敛到足够稳定的实现边界。

不得为了架构形式完整而过度设计。

不得在没有必要时提前固定类名、函数名、文件名或具体 Design Pattern（设计模式）。

---

# 一、资料原则

开始架构分析前：

1. 确认本任务的正式资料来源。
2. 优先读取当前问题真正需要的最小资料。
3. 不因为“以后可能有关”而读取整个项目。
4. 发现真正的新依赖后，再渐进式读取对应资料。
5. 游戏规则只能来自正式项目资料。
6. 资料没有确定的规则必须标记为“待定”，不得自行补充。
7. 已废弃规则不得重新引入。
8. 同一正式规则维持 SSOT（Single Source of Truth，单一事实来源）。

必须区分：

- 正式游戏规则；
- 当前实现架构结论；
- 为分析提出的暂定架构假设；
- 尚未解决的问题。

不得把架构推断伪装成正式游戏规则。

---

# 二、不要从设计章节直接映射程序模块

看到一个游戏概念时，不得因为设计文档中存在独立章节，就自动建立独立 Module（模块）。

首先判断它更接近：

- Module（模块）：拥有明确业务职责、行为或状态；
- Shared System（共享系统）：向多个模块提供公共能力；
- Data（数据）：主要描述定义、内容、数值或配置；
- State（状态）：某个模块拥有的运行数据；
- Presentation（表现）：只负责展示；
- Coordinator（协调器）：只负责流程编排。

Data（数据）是性质，不意味着必须独立成文件。

小而稳定、只属于一个模块的定义数据，可以由该模块直接拥有。

大量、频繁调整、需要独立平衡或内容维护的数据，再考虑拆入 balance/、content/ 或独立配置。

---

# 三、系统识别

对每个候选系统回答：

1. 它真正负责什么？
2. 它明确不负责什么？
3. 它是否拥有独立 State（状态）？
4. 它需要提供什么能力？
5. 哪些系统需要使用它？
6. 如果删除这个系统，它的职责由谁承担？
7. 它是否只是现有系统中的一个字段、定义或子能力？
8. 是否正在产生职责重叠？

如果两个系统长期修改同一核心状态，必须继续检查 State Ownership（状态所有权）。

---

# 四、State Ownership（状态所有权）

每个重要可变状态必须尽量只有一个明确 Owner（所有者）。

优先区分：

## Source State（源状态）

模块真正拥有并负责改变的事实。

例如：

当前装备
强化等级
当前境界
当前经验
当前配置

## Derived State（派生状态）

由一个或多个 Source State（源状态）推导出的结果。

例如：

最终人物攻击力
最终暴击率
当前有效战斗属性

不得让多个模块分别保存同一 Derived State（派生状态）并互相同步。

优先选择：

Source State
→ 统一计算 / 聚合
→ Derived State

而不是：

多个系统
→ 分别直接修改最终结果。

---

# 五、区分不同生命周期的状态

根据游戏实际规则判断是否需要：

## Persistent State（永久状态）

跨战斗、跨推进周期长期保留。

## Run Runtime State（本轮运行时状态）

在一次完整推进、关卡、Run（运行周期）中保留，但本轮结束后清除。

## Combat Runtime State（战斗运行时状态）

当前战斗或当前怪物期间存在。

## Attack Runtime State（攻击运行时状态）

只服务当前一次攻击或更短 Combat Phase（战斗阶段）。

禁止为了临时效果反复改写永久状态再恢复。

优先：

永久基础值
+
对应生命周期 Modifier（修正）
=
当前 Effective Value（有效值）。

---

# 六、属性系统

如果多个长期系统都会贡献人物属性，优先检查是否需要 Character Attribute System（人物属性系统）或 Attribute Aggregator（属性聚合器）。

成长模块只提供自己的 Attribute Contribution（属性贡献）。

统一属性系统负责聚合最终常驻人物属性。

战斗临时 Modifier（修正）不应无理由写回长期人物属性。

如果 UI 存在“基础面板”和“战时面板”，必须区分：

- Attribute State（属性状态）；
- UI Panel（用户界面面板）。

UI 如何展示数据，不应决定底层 State Ownership（状态所有权）。

---

# 七、Dependency Map（依赖地图）

架构初步形成后画 Dependency Map（依赖地图）。

分别检查：

- 谁依赖谁；
- Data Flow（数据流）和 Dependency Direction（依赖方向）是否被混淆；
- 是否出现不必要的双向依赖；
- 是否出现 Circular Dependency（循环依赖）；
- Combat（战斗）等核心模块是否知道过多外围系统；
- 一个模块是否为了完成简单任务读取整个项目。

优先减少无必要依赖，但不得为了“依赖少”破坏真实业务需要。

---

# 八、Direct Call（直接调用）与 Event（事件）

作为初步判断：

必须立刻取得结果才能继续
→ 优先考虑 Direct Call（直接调用）。

已经发生某件事情，需要通知其他系统
→ 优先考虑 Event（事件）或 Result（结果）。

例如：

“给我当前人物属性”
更接近 Direct Call（直接调用）。

“怪物刚刚死亡”
更接近 Event（事件）。

不得机械地把所有系统改造成 Event（事件）驱动。

---

# 九、Interface Contracts（接口契约）

确定依赖以后，再定义模块之间的 Interface Contract（接口契约）。

当前阶段只确定：

- Input（输入）；
- Output（输出）；
- Capability（能力）；
- Event / Result（事件 / 结果）；
- 允许修改的对象；
- 禁止越权修改的对象；
- 生命周期；
- 必要的顺序保证。

不要提前规定所有：

- 类名；
- 函数名；
- 参数类名；
- 文件路径；
- 具体代码实现。

除非这些细节已经对架构正确性产生实际影响。

---

# 十、Effect（效果）系统

对于神通、技能、Buff（增益）、Debuff（减益）、装备效果等会参与多个战斗阶段的系统：

不要让 Combat（战斗）硬编码每个具体效果名称。

优先检查是否可以通过统一 Effect Contract（效果契约）表达：

- Effect Type（效果类型）；
- Combat Phase（战斗阶段）；
- Condition（触发条件）；
- Modifier / Operation（修正 / 操作）；
- Priority / Slot Order（优先级 / 槽位顺序）；
- Lifetime（生命周期）。

具体内容系统定义“效果是什么”。

Combat Pipeline（战斗流水线）定义“什么时候允许结算”。

---

# 十一、流程编排

如果某个游戏存在：

推进
→ 创建目标
→ 开始战斗
→ 战斗结果
→ 奖励
→ 下一目标

这样的长期循环，检查是否需要 Coordinator（协调器）。

Coordinator（协调器）只负责 Orchestration（流程编排）。

可以决定：

现在轮到哪个模块执行。

不能逐渐承担：

伤害计算
骰子计算
奖励公式
属性聚合
装备操作
存档业务规则

等具体业务。

一旦 Coordinator（协调器）开始拥有大量业务数据或计算函数，应检查是否正在形成 God Object（上帝对象）。

---

# 十二、场景反推验证

任何重要架构结论都不得只根据静态模块图确认。

至少选择一个真实游戏场景，从开始到结束走一遍。

例如：

玩家更换装备
→ 属性变化
→ 开始战斗
→ 临时效果触发
→ 怪物死亡
→ 溢出
→ 奖励结算
→ 境界变化
→ 下一怪物

逐步询问：

1. 当前状态由谁拥有？
2. 谁可以修改？
3. 谁需要知道？
4. 数据通过什么边界传递？
5. 是否产生重复状态？
6. 是否产生模块越权？
7. 是否产生循环依赖？
8. 是否因为新增一个玩法而必须修改大量无关模块？

如果发现问题：

不要维护原方案只是为了保持一致。

回到上一级架构假设，修正后重新走场景。

---

# 十三、优先检查的架构坏味道

重点寻找：

- 同一状态多个 Owner（所有者）；
- 模块职责重叠；
- Combat（战斗模块）依赖整个游戏；
- 一个内容效果直接修改多个系统内部状态；
- 为临时 Buff（增益）修改永久状态再恢复；
- Coordinator（协调器）变成业务大总管；
- UI（用户界面）直接拥有业务事实；
- Save System（存档系统）决定业务规则；
- 一个新功能必须修改大量不相关模块；
- 为未来假想需求提前建立大量抽象层；
- 为了 SSOT（单一事实来源）而过度拆文件；
- 为了模块化而把一个简单能力拆成过多系统。

发现这些现象时必须重新检查架构，而不是直接继续编码。

---

# 十四、UI Flow（用户界面流程）

逻辑架构基本稳定后，再分析 UI Flow（用户界面流程）。

明确：

- 主要页面；
- 页面进入与退出；
- 当前运行状态允许打开哪些页面；
- 页面读取哪些状态；
- 哪些操作会改变业务状态；
- 状态变化后哪些 UI 需要刷新。

UI（用户界面）尽量消费正式业务状态。

不要因为方便显示而建立第二套业务事实来源。

---

# 十五、Save System（存档系统）

在 State Ownership（状态所有权）明确以后再设计 Save System（存档系统）。

逐项判断：

- 哪些 Source State（源状态）必须保存；
- 哪些 Derived State（派生状态）可以重新计算；
- 哪些 Runtime State（运行时状态）不应该保存；
- 哪些运行状态根据游戏规则必须支持恢复；
- Save Version（存档版本）如何处理；
- 读档后的初始化顺序；
- 聚合属性何时重建；
- Runtime（运行时）何时重新建立。

Save System（存档系统）负责持久化和恢复。

不得成为游戏业务状态的第二 Owner（所有者）。

---

# 十六、反推 Context Engineering（上下文工程）

只有 Implementation Architecture（实现架构）基本稳定后，才反推：

- 开发索引；
- Core Dependency（核心依赖）；
- Conditional Dependency（条件依赖）；
- Minimum Context（最小上下文）；
- Progressive Disclosure（渐进式加载）规则。

对每个模块确定：

开发它的核心功能时必须读取什么。

只有特定任务才读取什么。

默认明确不需要读取什么。

不得通过“未来可能有关”扩大默认上下文。

---

# 十七、开发前检查

在让 Codex 编码某模块以前确认：

- 模块职责已经明确；
- 不负责范围已经明确；
- State Ownership（状态所有权）已经明确；
- 关键 Dependency（依赖）已经明确；
- 必要 Interface Contract（接口契约）已经明确；
- 正式规则 SSOT（单一事实来源）已经定位；
- 未确定游戏规则已经标记；
- Acceptance Criteria（验收标准）可以描述。

如果这些信息不足以安全开发，应先补架构，而不是让 Codex 自行猜测项目规则。

---

# 十八、实现阶段原则

架构已经足够明确以后，再允许进入编码。

Codex 可以自行决定普通 Implementation Detail（实现细节），例如：

- 局部类名；
- 私有函数名；
- 常规数据结构；
- 普通重构方式。

除非这些选择影响：

- 模块边界；
- State Ownership（状态所有权）；
- Dependency（依赖）；
- Interface Contract（接口契约）；
- Save Compatibility（存档兼容）；
- 游戏规则。

不要把所有纯代码细节重新交给项目负责人决定。

---

# 十九、实现后的验证循环

每完成一个模块或重要接入：

实现
→ Unit Test（单元测试）
→ Integration Test（集成测试）
→ Regression Test（回归测试）
→ Diff（差异检查）
→ Acceptance Criteria（验收）
→ 架构边界检查

如果失败：

优先寻找 Root Cause（根本原因）。

不要仅通过增加临时判断、重复状态或跨模块直接修改来绕过问题。

必要时回退到架构层修正。

---

# 二十、最终文档原则

探索过程中允许存在：

假设
→ 失败
→ 修正
→ 新方案。

但正式架构文档只保留：

- 当前有效架构；
- 当前职责；
- 当前接口；
- 当前依赖；
- 当前状态所有权；
- 必要且仍未解决的待定项。

不要把大量废案和旧讨论长期堆入正式项目文档。

Git（版本控制系统）负责保存历史。

正式文档负责描述当前事实。

---

# 二十一、交互原则

当用户处于学习 / 架构讨论阶段：

使用：

解释
→ 游戏实际案例
→ 少量综合判断
→ 根据回答继续

避免要求不会编程的项目负责人决定纯代码 Implementation Detail（实现细节）。

当用户要求加快进度时：

每轮可以同时处理多个紧密相关的架构问题，不重复已经掌握的基础概念。

当用户要求正式执行开发任务时：

停止教学式小测，按照本 Skill（技能）的架构约束直接分析、计划、实现和验证。

---

# 二十二、最高原则

不要追求第一次就设计出完美架构。

使用：

Game Design（游戏设计）
→ Architecture Hypothesis（架构假设）
→ Runtime Scenario（运行场景）
→ Stress Test（压力验证）
→ Problem Detection（问题发现）
→ Revision（修正）
→ Revalidation（重新验证）

逐步收敛。

目标不是“架构看起来专业”。

目标是：

让 Codex 能够在有限 Context（上下文）下，长期、模块化、可测试、可扩展地实现游戏，并且新功能不会迫使整个项目失控。
