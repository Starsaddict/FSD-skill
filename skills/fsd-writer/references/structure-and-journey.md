# FSD 章节骨架与 User Journey 叙事规范

本文件用于在 **Mode 1（大纲整理）** 与 **Mode 2（代码仓生成）** 阶段确定 FSD 的宏观结构、章节落位与范围边界。

---

## 1. 标准 FSD 顶层结构

```text
文档元信息表（Business domain / Function ID / Organization / Date）
# Approval roles and sign-off basis（含 Revision history）
# Contents（目录总览 —— 必须与正文标题 100% 一致）

# 1 Background
  ## 1.1 Business background
    ### Business scenario
    ### Functional goals
    ### Scope of this phase（仅分组范围要点，不放阶段汇总表）
  ## 1.2 Dependencies
  ## 1.3 Assumptions & Related Links（按需选填；无内容则省略）

# 2 Solution design
  ## 2.1 Functionality
    ### 2.1.1 User Journey（端到端旅程图 + 唯一阶段拆解表）
    ### 2.1.2 Feature 1: <核心功能名>
      - （按需）业务总览与参与体（如 Digital Employees）
      - Entry Path（面包屑 + 入口标注图 + 默认落脚页）
      - 按用户操作路径展开的界面/队列/详情/流程/规则子节
    ### 2.1.3 Feature 2: <核心功能名>
      - （同上，独立完整展开）
    ### ...
  ## 2.2 Configuration & Governance
    ### 2.2.1 Access Control and User Roles
    ### 2.2.2 Settings Entry and Global Rules
    ### 2.2.3 <配置模块 A / B / C>
    ### 2.2.4 Maintenance and Destructive Operations（按需）
```

> **专项视图 / 大屏看板的归位原则**：
> 运营分析、数据大屏（如 LCAP Dashboard）等面向业务用户的功能界面，在概念上**一律属于 `2.1 Functionality`** 下的独立 Feature 节。仅当某一独立交付面因项目合同或签收边界需要单独平级呈现时，才可作为特例在 `2 Solution design` 下单列同级章节。

---

## 2. 单 Case 多 Feature 的重复展开法

一个 Use Case 往往包含多个相对独立但协同运作的功能（例如：核心处理工作台、运营分析报告、高管看板等）。禁止把所有功能的入口混在一起或把所有列表堆在一个章节下，**每个 Feature 必须作为一个闭环单元展开**：

1. **定位与边界**：一句话说明本 Feature 解决什么问题，以及它与相邻 Feature/页面的职责边界（防止读者混淆“我在哪、这页和隔壁页什么关系”）。
2. **业务参与体（仅用到时写）**：若本 Feature 涉及专门的自动化角色/数字员工，放在本节开头用一张表写清职责；若已在更上级业务总览中定义，直接引用，严禁重复粘贴。
3. **Entry Path**：明确用户从哪里点击进入本 Feature。
4. **自顶向下 / 从浅到深的界面与流程流**：
   - 先概览（如 Metric Cards）→ 再工作队列（List）→ 再单条详情/处理（Detail Workspace）→ 支撑该环节的后台流程、状态机、时间窗口与外部系统回调**紧跟在触发它们的界面或步骤之后就地写清**。

---

## 3. “哪里用到才在哪里讲”落位表（Just-in-Time Context）

判断某类信息该放哪一章时，严格遵循以下读者心智落位规则：

| 信息类型 | 正确落位 | 错误反例（禁止） |
|---|---|---|
| **阶段拆解汇总表** | 统一放在 `2.1.1 User Journey`（唯一权威表） | 在 `1.1 Scope of this phase` 和 `2.1.1 User Journey` 各放一张重复表 |
| **Digital Employees / 自动化参与体职责** | 放在其所属业务功能总览的开头（用前先认人） | 一半写在总览、另一半散落在后面的扩展章节里 |
| **Access Control & User Roles（RBAC）** | 放在 `2.2 Configuration & Governance` 开头（功能讲解之后） | 堆在文档开头，读者还没看懂有哪些功能就先背权限矩阵 |
| **表单校验 / 报错文案 / 显隐联动** | 就地写在对应表单组件的字段表与交互规则中 | 全部抽离到全篇末尾一个孤立的“校验与错误处理”章节 |
| **状态机 / 终止路径 / 外部工单回调** | 紧跟在“对象处理 / 生命周期”所在的功能节中 | 拆散到“流程图”、“接口扩展”等无关章节 |
| **时间窗口与边界规则** | 紧跟在受该时间规则支配的业务步骤下方 | 只在背景里模糊提一句“6 小时”，不写起算点、是否重置和到期去向 |
| **基线参数（供看板/指标计算用）** | 公式写在对应看板/Metric Card 节中，参数配置表单写在 `2.2 Configuration`，两处通过章节号互链 | 两处各写一套不同的默认值或公式 |

---

## 4. 必填 vs. 选填章节决策表

| 章节 | 状态 | 省略条件 |
|---|---|---|
| 文档元信息 / 审批角色 / 修订记录 / 目录 | **必填** | 不可省略 |
| `1.1 Business background`（Scenario / Goals / Scope） | **必填** | 不可省略 |
| `1.2 Dependencies` | **必填** | 若完全无外部依赖，一行写明“无外部依赖”即可 |
| `1.3 Assumptions & Related Links` | 选填 | 无明确前提假设或参考链接时直接省略，不留空标题 |
| `2.1.1 User Journey` | **必填** | 不可省略（单人简单功能也需给出简化步骤流） |
| `2.1.2+ Feature` 展开 | **必填** | 至少 1 个 Feature |
| `2.2.1 Access Control and User Roles` | **必填** | 若系统无多角色区分，简要声明访问前提与空间隔离即可 |
| `2.2.2+ Settings` 配置页 | 按需 | 若无后台可配置项，可省略具体表单节，仅保留权限治理 |
| `2.2.4 Maintenance & Destructive Operations` | 按需 | 仅在存在数据重置、批量删除等高危操作时必填 |

---

## 5. Anti-Scope：严禁写入 FSD 的工程与测试项

整理大纲或从代码仓逆向生成时，遇到以下内容必须**主动剥离**：

- ❌ 数据库表名、字段物理类型（`VARCHAR(255)` / `BIGINT`）、主键/索引设计
- ❌ 代码架构对象（类名、Controller/Service 名、Hook/Plugin 内部扩展点实现）
- ❌ 内部微服务之间的 RPC/REST API 路径、内部 Request/Response JSON Schema、消息队列 Topic（*例外：对外暴露给外部系统配置的 Webhook/Callback URL 及其**业务级安全校验规则**可保留*）
- ❌ 一次性数据迁移脚本、ETL 任务与底层数仓/分析模型表设计
- ❌ 详细单元测试步骤与 QA Test Cases（包括测试造数和回滚脚本）

---

## 6. 整理大纲时的边界提醒（Mode 1 专用）

- **用户已决定 User Journey**：不要把 Step 1 变成冗长的产品需求调研访谈。
- 仅基于用户提供的素材整理出清晰骨架；如果用户素材里只提到了 3 个阶段，就整理 3 个阶段，不要自行扩充成 6 个阶段。
- 只追问真正阻断 FSD 口径闭环的缺失信息（例如“未提及目标文档语言”、“未说明某破坏性操作是否允许在生产环境执行”）。
