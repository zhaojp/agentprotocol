agent uri 协议
# 0. 背景与用途

## 0.1 背景

随着大模型能力的发展，AI 正在从“问答式助手”逐渐演化为“可执行任务的智能体”。

早期的大模型主要用于：

```text
回答问题
生成文本
总结内容
辅助写代码
翻译和改写
```

但下一阶段的 AI Agent 将不再只是被动回答，而是可以主动完成任务，例如：

```text
读取邮件并生成回复
审查合同并标注风险
分析发票并判断是否异常
安排会议并写入日历
分析销售线索并给出优先级
读取客服工单并生成回复草稿
调用企业系统完成审批流
调用外部工具完成数据分析
```

这意味着 AI Agent 会逐渐具备以下特征：

```text
有明确身份
有专业能力
有任务边界
有权限范围
有调用入口
有执行记录
有风险等级
有责任主体
```

在这种情况下，仅仅使用普通网页 URL、API Endpoint 或聊天机器人名称，已经不足以描述一个 Agent。

例如，一个企业内部可能同时存在多个 Agent：

```text
合同审查 Agent
发票审核 Agent
客服回复 Agent
销售线索评分 Agent
会议安排 Agent
报销审批 Agent
数据分析 Agent
招聘筛选 Agent
```

这些 Agent 需要被人类用户、企业系统以及其他 Agent 找到、识别、调用、授权、审计和管理。

因此，我们需要一种新的语义地址格式，用于描述：

```text
这个 Agent 属于谁
这个 Agent 位于哪个业务领域
这个 Agent 能处理什么对象
这个 Agent 能执行什么动作
调用它需要什么权限
调用它是否需要人工审批
调用它是否可信
调用它是否会产生风险
调用它是否会产生费用
调用它之后如何审计
```

`agent://` 就是在这个背景下提出的一种 URI 方案。

它的目标是成为 AI Agent 世界中的基础寻址层。

---

## 0.2 为什么不是普通 URL？

普通 URL 主要用于访问资源，例如：

```text
https://example.com/page
https://api.example.com/v1/invoices
```

它描述的是：

```text
访问哪个网络位置
请求哪个资源
调用哪个接口
```

但 Agent URI 描述的是：

```text
调用哪个智能体
执行什么任务
以什么权限执行
是否需要审批
是否可信
风险等级是什么
```

例如：

```text
agent://example.com/legal/contract.review?scope=readonly&trust=verified
```

这不是简单地访问一个网页，而是在表达：

> 调用 example.com 这个主体下的合同审查 Agent，以只读权限执行合同审查任务，并要求该 Agent 已经过身份验证。

因此，Agent URI 和普通 URL 的区别在于：

```text
URL 面向资源访问
Agent URI 面向任务调用

URL 关注位置
Agent URI 关注能力

URL 描述在哪里
Agent URI 描述谁来做什么

URL 更接近网页和接口
Agent URI 更接近数字劳动力和任务入口
```

---

## 0.3 为什么必须以域名开始？

Agent URI 推荐采用以下结构：

```text
agent://<domain>/<namespace>/<object>.<action>
```

也就是：

```text
agent://域名/业务领域/对象.动作
```

例如：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/support/customer.reply
```

之所以必须以域名开始，是因为域名提供了现实世界中非常重要的基础能力：

```text
全球唯一性
组织归属
品牌识别
DNS 解析
证书验证
责任主体
信任基础
```

如果写成：

```text
agent://legal/contract.review
```

就会产生问题：

```text
不知道这个 Agent 属于谁
不知道谁维护它
不知道谁对结果负责
不知道如何验证它是否可信
不同组织之间容易产生命名冲突
无法自然接入 DNS 和 HTTPS 体系
```

而写成：

```text
agent://example.com/legal/contract.review
```

则可以明确表示：

```text
该 Agent 属于 example.com 这个域名主体
该 Agent 位于 legal 业务领域
该 Agent 处理 contract 对象
该 Agent 执行 review 动作
```

这使 Agent URI 能够天然继承现有互联网的域名、DNS、HTTPS、证书和组织验证体系。

---

## 0.4 Agent URI 解决什么问题？

Agent URI 试图解决以下核心问题。

### 0.4.1 Agent 如何被命名？

未来会存在大量 Agent。
它们不能只叫：

```text
助手
机器人
小助理
AI 工具
智能客服
```

这些名字过于模糊，无法形成标准化生态。

Agent URI 提供了一种清晰命名方式：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/sales/lead.score
agent://example.com/hr/resume.screen
```

每个 URI 都能表达：

```text
谁提供
属于哪个领域
处理什么对象
执行什么动作
```

---

### 0.4.2 Agent 如何被发现？

未来可能存在 Agent Registry、Agent Marketplace、企业内部 Agent Directory。

用户或系统可以通过 Agent URI 查找：

```text
合同审查 Agent
发票审核 Agent
客服回复 Agent
销售线索评分 Agent
会议安排 Agent
```

例如，一个 Agent Registry 可以索引：

```text
agent://example.com/legal/contract.review
agent://example.com/legal/contract.summarize
agent://example.com/legal/contract.redline
```

这样，Agent 不再只是某个平台里的隐藏功能，而是可以像网站、API、应用一样被发现。

---

### 0.4.3 Agent 如何被调用？

Agent URI 可以作为调用入口。

例如：

```text
agent://example.com/support/customer.reply?scope=dryrun&approval=required
```

表示：

```text
调用客服回复 Agent
只生成草稿
不直接发送
发送前必须人工确认
```

这比单纯写：

```text
调用客服机器人
```

更明确、更安全，也更适合系统之间自动协作。

---

### 0.4.4 Agent 如何被授权？

Agent 可能需要访问邮件、日历、CRM、财务系统、工单系统或数据库。

因此必须声明权限。

例如：

```text
agent://example.com/email/inbox.summarize?scope=readonly
agent://example.com/calendar/meeting.schedule?scope=execute&approval=required
agent://example.com/finance/payment.approve?scope=execute&risk=R5&approval=mfa
```

这些 URI 可以表达：

```text
只读
生成建议
模拟执行
真实执行
需要审批
需要多因素确认
```

这让 Agent 调用具备更清晰的权限边界。

---

### 0.4.5 Agent 如何被信任？

未来不是所有 Agent 都能被随便调用。

用户和企业会关心：

```text
这个 Agent 是谁提供的？
它是否通过验证？
它是否被审计过？
它是否会保存数据？
它是否会泄露隐私？
它是否会执行高风险动作？
它的历史表现如何？
```

Agent URI 可以结合 Agent Card、Agent Manifest、Agent Trust Score 使用。

例如：

```text
agent://example.com/legal/contract.review?trust=verified
agent://example.com/security/access.review?trust=certified
agent://example.com/finance/invoice.audit?trust=audited
```

这样，Agent URI 不只是地址，也可以成为信任体系的入口。

---

### 0.4.6 Agent 如何被治理？

企业不可能允许 Agent 随意执行动作。

Agent URI 可以配合 Agent Gateway、Agent Firewall、Agent Audit Log 使用，实现：

```text
权限控制
审批流程
调用审计
预算控制
风险分级
调用限流
工具隔离
数据边界
执行回滚
```

例如：

```text
agent://example.com/finance/payment.approve?scope=execute&risk=R5&approval=mfa
```

这条 URI 本身就提示系统：

```text
这是一个高风险金融操作
需要执行权限
需要多因素确认
必须记录审计日志
```

---

## 0.5 Agent URI 的主要用途

Agent URI 可以用于以下场景。

---

### 0.5.1 企业内部 Agent 目录

企业可以为内部 Agent 建立统一目录。

例如：

```text
agent://company.com/legal/contract.review
agent://company.com/finance/invoice.audit
agent://company.com/hr/resume.screen
agent://company.com/support/customer.reply
agent://company.com/data/report.analyze
```

员工不需要记住每个系统入口，只需要知道要调用哪个 Agent。

这类似企业内部的：

```text
Agent 黄页
Agent 应用商店
Agent 服务目录
Agent 能力中心
```

---

### 0.5.2 Agent Marketplace

未来可能出现专门的 Agent 市场。

每个 Agent 都可以拥有标准 URI：

```text
agent://vendor.com/legal/contract.review
agent://vendor.com/marketing/content.generate
agent://vendor.com/sales/lead.score
```

市场可以展示：

```text
能力说明
价格
评分
信任等级
权限范围
风险等级
调用次数
用户评价
替代方案
```

这使 Agent 可以像 SaaS、API、插件一样被发现和采购。

---

### 0.5.3 Agent-to-Agent 调用

一个 Agent 可以调用另一个 Agent。

例如，企业采购 Agent 可能调用：

```text
agent://vendor.com/procurement/vendor.compare
agent://legalvendor.com/legal/contract.review
agent://financevendor.com/finance/payment.review
```

形成自动化工作流：

```text
采购 Agent 生成需求
供应商比较 Agent 筛选供应商
合同审查 Agent 审查条款
财务审核 Agent 检查付款条件
日程 Agent 安排会议
```

Agent URI 为多 Agent 协作提供统一寻址方式。

---

### 0.5.4 Agent Gateway 路由

企业可以部署 Agent Gateway，根据 URI 将任务路由到不同 Agent。

例如：

```text
agent://company.com/support/customer.reply
```

Agent Gateway 可以执行：

```text
解析 URI
验证身份
检查权限
加载 Agent Manifest
执行安全策略
路由到对应 Agent
记录审计日志
返回结果
```

这类似 API Gateway，但面向 AI Agent。

---

### 0.5.5 权限控制和安全审计

Agent URI 可以作为安全策略的匹配对象。

例如策略规则：

```yaml
allow:
  - agent://company.com/email/inbox.summarize
  - agent://company.com/data/report.analyze

approval_required:
  - agent://company.com/calendar/meeting.schedule
  - agent://company.com/support/customer.reply

deny:
  - agent://company.com/finance/payment.approve
```

这样，企业可以基于 Agent URI 管理权限，而不是依赖零散的 Prompt 或手工配置。

---

### 0.5.6 Agent Card 和 Agent Manifest 发现

通过 Agent URI 的域名，可以发现该 Agent 的元数据。

例如：

```text
agent://example.com/legal/contract.review
```

可以对应：

```text
https://example.com/.well-known/agent-manifest.json
https://example.com/agents/legal/contract.review/card.json
```

这些文件可以描述：

```text
Agent 名称
Agent 能力
权限范围
风险等级
调用方式
价格
审计能力
数据保留策略
维护者
版本
```

这使 Agent URI 不只是一个地址，而是 Agent 元数据发现入口。

---

### 0.5.7 Agent 评测和排行榜

AgentTop 这类平台可以基于 Agent URI 建立排行榜和索引。

例如：

```text
agent://vendor-a.com/legal/contract.review
agent://vendor-b.com/legal/contract.review
agent://vendor-c.com/legal/contract.review
```

可以在同一任务下比较：

```text
准确率
成本
响应速度
用户评分
风险控制
审计能力
数据隐私
企业适配度
```

这使 Agent URI 可以成为 Agent 能力比较的基础单位。

---

### 0.5.8 工作流编排

一个复杂任务可以由多个 Agent URI 组成。

例如“处理客户退款请求”：

```text
agent://company.com/support/ticket.classify
agent://company.com/commerce/order.track
agent://company.com/commerce/refund.review
agent://company.com/support/customer.reply
```

工作流可以表达为：

```text
先分类工单
再查询订单
再审核退款
最后生成客服回复
```

Agent URI 使工作流具备可读、可配置、可审计的结构。

---

### 0.5.9 成本控制和计费

Agent URI 可以作为计费单位。

例如：

```text
agent://example.com/data/report.analyze?budget=5&currency=USD
```

企业可以统计：

```text
每个 Agent 调用了多少次
每个 Agent 花了多少钱
每类任务平均成本是多少
哪个 Agent ROI 更高
哪个 Agent 失败率更高
```

这对企业采购、预算管理和 AgentOps 很重要。

---

### 0.5.10 数字员工管理

如果未来企业拥有大量 AI 员工，那么 Agent URI 可以成为数字员工的身份标识。

例如：

```text
agent://company.com/hr/resume.screen
agent://company.com/finance/invoice.audit
agent://company.com/sales/lead.score
agent://company.com/support/customer.reply
```

每个 URI 对应一个数字员工或一种数字能力。

企业可以围绕 URI 管理：

```text
职责
权限
KPI
成本
风险
版本
审计
负责人
生命周期
```

这使 Agent URI 成为 AgentOps 和数字员工治理的基础。

---

## 0.6 Agent URI 的长期意义

`agent://` 的长期意义不只是提供一种新格式，而是为 AI Agent 世界提供一套基础坐标系。

如果说：

```text
https:// 连接的是网页和服务
mailto: 连接的是邮箱身份
tel: 连接的是电话号码
ssh:// 连接的是远程机器
```

那么：

```text
agent:// 连接的是智能体能力
```

它表达的是：

```text
谁可以完成什么任务
在什么权限下完成
以什么风险等级完成
由谁负责
如何被发现
如何被调用
如何被审计
如何被信任
```

未来的 AI Agent 世界可能会出现大量新基础设施：

```text
Agent Registry
Agent Gateway
Agent Firewall
Agent Trust Score
Agent Marketplace
Agent Wallet
Agent Audit Log
Agent Control Plane
AgentOps Platform
```

而 Agent URI 可以成为这些基础设施之间共同使用的地址语言。

---

## 0.7 一句话定义

Agent URI 是一种面向 AI Agent 的语义化寻址协议，用于描述、发现、调用和治理由特定域名主体提供的智能体能力。

推荐结构：

```text
agent://<domain>/<namespace>/<object>.<action>
```

示例：

```text
agent://example.com/legal/contract.review
```

表示：

> 调用 example.com 提供的 legal 领域中，对 contract 执行 review 动作的 Agent。
