# Agent URI 协议结构草案

## 1. 基本原则

Agent URI 必须以域名作为第一层命名空间。

推荐格式：

```text
agent://<domain>/<namespace>/<agent>[.<skill>][?key=value&key=value]
```

其中：

```text
agent://域名/领域/智能体.能力?参数
```

示例：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/support/customer.reply
agent://example.com/sales/lead.score
agent://example.com/hr/resume.screen
```

这样设计的核心原因是：

1. 域名天然具备全球唯一性；
2. 域名可以和现实组织、公司、个人品牌绑定；
3. 域名可以通过 DNS、HTTPS、证书体系完成身份验证；
4. 域名所有者可以维护自己的 Agent Registry；
5. 不同组织可以使用相同的 Agent 名称而不冲突；
6. 未来可以通过域名解析 Agent Card、Agent Manifest、Agent Trust 信息。

---

## 2. 推荐 URI 结构

### 2.1 标准格式

```text
agent://<domain>/<namespace>/<agent>.<skill>
```

示例：

```text
agent://example.com/legal/contract.review
```

含义：

```text
domain: example.com
namespace: legal
agent: contract
skill: review
```

表示：

> 调用 example.com 这个主体下，legal 领域中的 contract review Agent。

---

### 2.2 带参数格式

```text
agent://<domain>/<namespace>/<agent>.<skill>?scope=<scope>&mode=<mode>&approval=<approval>
```

示例：

```text
agent://example.com/legal/contract.review?scope=readonly&mode=analyze
```

含义：

```text
domain: example.com
namespace: legal
agent: contract
skill: review
scope: readonly
mode: analyze
```

表示：

> 以只读权限调用 example.com 的合同审查 Agent，并以分析模式运行。

---

### 2.3 带版本格式

```text
agent://<domain>/<namespace>/<agent>.<skill>@<version>
```

示例：

```text
agent://example.com/finance/invoice.audit@1.2.0
agent://example.com/support/customer.reply@stable
agent://example.com/sales/lead.score@latest
```

推荐：

```text
@1.2.0   固定版本
@stable  稳定版本
@latest  最新版本
```

企业生产环境建议优先使用固定版本或 `@stable`，避免 `@latest` 带来不可控行为变化。

---

### 2.4 带组织路径格式

大型组织可以在域名后加入组织、团队或产品路径。

```text
agent://<domain>/<org>/<namespace>/<agent>.<skill>
```

示例：

```text
agent://example.com/acme/legal/contract.review
agent://example.com/internal/finance/invoice.audit
agent://example.com/product/support/customer.reply
```

也可以用于多租户场景：

```text
agent://agenttop.com/acme/legal/contract.review
agent://agenttop.com/contoso/legal/contract.review
```

但推荐第一版规范中保持简单，优先使用：

```text
agent://<domain>/<namespace>/<agent>.<skill>
```

---

## 3. 组件定义

### 3.1 Scheme

固定为：

```text
agent://
```

表示这是一个 Agent URI。

它不是普通网页 URL，而是一个指向 AI Agent、Agent Skill、Agent Workflow 或 Agent Service 的语义地址。

---

### 3.2 Domain

`domain` 是 Agent URI 的第一层主体标识。

示例：

```text
agenttop.com
example.com
company.com
openai.com
anthropic.com
microsoft.com
```

推荐：

```text
agent://agenttop.com/legal/contract.review
```

不推荐：

```text
agent://legal/contract.review
```

原因是后者缺少归属主体，无法判断这个 Agent 由谁提供、谁负责、谁认证、谁维护。

---

### 3.3 Namespace

`namespace` 表示 Agent 所属领域、业务域或能力分类。

常见 namespace：

```text
legal
finance
sales
support
hr
marketing
data
calendar
email
procurement
commerce
product
project
security
it
education
health
travel
personal
```

示例：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/support/customer.reply
```

---

### 3.4 Agent

`agent` 表示任务对象、业务对象或智能体主体。

示例：

```text
contract
invoice
expense
lead
email
ticket
customer
resume
interview
content
campaign
report
meeting
vendor
order
refund
feedback
task
access
device
```

示例：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/sales/lead.score
```

---

### 3.5 Skill

`skill` 表示 Agent 对任务对象执行的具体动作。

推荐使用动词：

```text
review
audit
score
draft
classify
reply
screen
summarize
generate
analyze
schedule
compare
track
prioritize
troubleshoot
verify
extract
translate
recommend
approve
```

示例：

```text
contract.review
invoice.audit
lead.score
email.draft
ticket.classify
customer.reply
resume.screen
meeting.schedule
```

完整 URI：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/sales/lead.score
agent://example.com/email/reply.draft
agent://example.com/calendar/meeting.schedule
```

---

## 4. 推荐命名方式

### 4.1 标准模式

```text
agent://<domain>/<business-domain>/<object>.<action>
```

对应中文：

```text
agent://域名/业务领域/对象.动作
```

示例：

```text
agent://agenttop.com/legal/contract.review
agent://agenttop.com/finance/invoice.audit
agent://agenttop.com/support/ticket.classify
agent://agenttop.com/sales/lead.score
agent://agenttop.com/hr/resume.screen
```

这种结构清晰表达了：

```text
谁提供：agenttop.com
属于什么领域：legal
处理什么对象：contract
执行什么动作：review
```

---

### 4.2 为什么使用 object.action

推荐使用：

```text
contract.review
invoice.audit
lead.score
customer.reply
```

而不是：

```text
review.contract
audit.invoice
score.lead
reply.customer
```

原因是：

1. 先对象，后动作，更接近业务理解；
2. 方便同一对象下扩展多个能力；
3. 更适合形成能力矩阵；
4. 更适合人类阅读和目录分类。

例如：

```text
agent://example.com/legal/contract.review
agent://example.com/legal/contract.summarize
agent://example.com/legal/contract.extract
agent://example.com/legal/contract.redline
```

表示同一个 `contract` 对象下的不同能力。

---

## 5. Query 参数结构

Query 参数用于表达权限、模式、风险、信任、审批、预算等调用约束。

推荐格式：

```text
agent://<domain>/<namespace>/<agent>.<skill>?scope=<scope>&mode=<mode>&approval=<approval>&risk=<risk>
```

示例：

```text
agent://example.com/legal/contract.review?scope=readonly&mode=analyze&risk=R2
```

---

## 6. 推荐 Query 参数

### 6.1 scope

表示权限范围。

```text
scope=none
scope=readonly
scope=suggest
scope=dryrun
scope=readwrite
scope=execute
scope=admin
```

示例：

```text
agent://example.com/legal/contract.review?scope=readonly
agent://example.com/support/customer.reply?scope=dryrun
agent://example.com/calendar/meeting.schedule?scope=execute
```

---

### 6.2 mode

表示执行模式。

```text
mode=chat
mode=analyze
mode=dryrun
mode=execute
mode=shadow
```

示例：

```text
agent://example.com/support/customer.reply?mode=dryrun
agent://example.com/calendar/meeting.schedule?mode=execute
agent://example.com/sales/lead.score?mode=shadow
```

---

### 6.3 approval

表示审批要求。

```text
approval=none
approval=optional
approval=required
approval=mfa
approval=multi-party
```

示例：

```text
agent://example.com/email/reply.send?approval=required
agent://example.com/finance/payment.approve?approval=mfa
```

---

### 6.4 trust

表示信任等级。

```text
trust=unknown
trust=self
trust=verified
trust=audited
trust=enterprise
trust=certified
```

示例：

```text
agent://example.com/legal/contract.review?trust=verified
agent://example.com/security/access.review?trust=certified
```

---

### 6.5 risk

表示风险等级。

```text
risk=R0
risk=R1
risk=R2
risk=R3
risk=R4
risk=R5
```

示例：

```text
agent://example.com/email/inbox.summarize?risk=R2
agent://example.com/calendar/meeting.schedule?risk=R3&approval=required
agent://example.com/finance/payment.approve?risk=R5&approval=mfa
```

---

### 6.6 budget

表示本次调用允许消耗的最大预算。

```text
budget=10
currency=USD
```

示例：

```text
agent://example.com/data/report.analyze?budget=5&currency=USD
```

---

## 7. 域名验证机制

由于 Agent URI 以域名作为第一层主体，因此必须支持域名验证。

推荐通过以下方式验证某个 Agent URI 是否属于该域名主体。

### 7.1 Well-known Agent Manifest

推荐路径：

```text
https://<domain>/.well-known/agent-manifest.json
```

例如：

```text
https://example.com/.well-known/agent-manifest.json
```

其中声明该域名下支持的 Agent：

```json
{
  "domain": "example.com",
  "agents": [
    {
      "uri": "agent://example.com/legal/contract.review",
      "manifest_url": "https://example.com/agents/legal/contract.review/manifest.json",
      "card_url": "https://example.com/agents/legal/contract.review/card.json"
    },
    {
      "uri": "agent://example.com/finance/invoice.audit",
      "manifest_url": "https://example.com/agents/finance/invoice.audit/manifest.json",
      "card_url": "https://example.com/agents/finance/invoice.audit/card.json"
    }
  ]
}
```

---

### 7.2 Agent Card

每个 Agent 应该有一个 Agent Card。

推荐路径：

```text
https://<domain>/agents/<namespace>/<agent>.<skill>/card.json
```

示例：

```text
https://example.com/agents/legal/contract.review/card.json
```

Agent Card 示例：

```json
{
  "uri": "agent://example.com/legal/contract.review",
  "name": "Contract Review Agent",
  "description": "Review contracts, identify risky clauses, summarize obligations, and suggest revisions.",
  "owner": {
    "name": "Example Inc.",
    "domain": "example.com"
  },
  "category": "legal",
  "default_scope": "readonly",
  "risk_level": "R2",
  "trust_level": "verified",
  "audit_log": true,
  "data_retention": "none"
}
```

---

### 7.3 DNS TXT 验证

域名所有者可以通过 DNS TXT 记录声明支持 Agent URI。

示例：

```text
_agent.example.com TXT "agent-uri=v0.1; manifest=https://example.com/.well-known/agent-manifest.json"
```

也可以针对单个 Agent 声明：

```text
_contract-review._agent.example.com TXT "uri=agent://example.com/legal/contract.review; trust=verified"
```

---

## 8. URI 解析规则

给定 URI：

```text
agent://agenttop.com/legal/contract.review?scope=readonly&mode=analyze
```

解析结果：

```json
{
  "scheme": "agent",
  "domain": "agenttop.com",
  "namespace": "legal",
  "agent": "contract",
  "skill": "review",
  "query": {
    "scope": "readonly",
    "mode": "analyze"
  }
}
```

给定 URI：

```text
agent://agenttop.com/support/customer.reply@stable?scope=dryrun&approval=required
```

解析结果：

```json
{
  "scheme": "agent",
  "domain": "agenttop.com",
  "namespace": "support",
  "agent": "customer",
  "skill": "reply",
  "version": "stable",
  "query": {
    "scope": "dryrun",
    "approval": "required"
  }
}
```

---

## 9. 推荐示例

### 9.1 合同审查

```text
agent://agenttop.com/legal/contract.review?scope=readonly&trust=verified
```

含义：

> 调用 agenttop.com 提供的合同审查 Agent，只读权限，身份已验证。

---

### 9.2 发票审核

```text
agent://agenttop.com/finance/invoice.audit?scope=readonly
```

含义：

> 调用 agenttop.com 提供的发票审核 Agent，只读取发票信息并给出审核结果。

---

### 9.3 客服回复草稿

```text
agent://agenttop.com/support/customer.reply?scope=dryrun&approval=required
```

含义：

> 调用 agenttop.com 提供的客服回复 Agent，只生成草稿，发送前必须人工确认。

---

### 9.4 销售线索评分

```text
agent://agenttop.com/sales/lead.score?scope=readonly
```

含义：

> 调用 agenttop.com 提供的销售线索评分 Agent，只读取线索资料并输出评分。

---

### 9.5 会议安排

```text
agent://agenttop.com/calendar/meeting.schedule?scope=execute&approval=required
```

含义：

> 调用 agenttop.com 提供的会议安排 Agent，可创建日程，但执行前需要用户确认。

---

### 9.6 付款审批

```text
agent://agenttop.com/finance/payment.approve?scope=execute&risk=R5&approval=mfa
```

含义：

> 调用 agenttop.com 提供的付款审批 Agent，属于高风险操作，需要多因素确认。

---

## 10. 不推荐的结构

### 10.1 缺少域名

不推荐：

```text
agent://legal/contract.review
```

原因：

```text
无法确认提供者
无法验证归属
无法解析 Agent Card
无法建立信任体系
不同组织之间容易命名冲突
```

推荐：

```text
agent://example.com/legal/contract.review
```

---

### 10.2 名称过于抽象

不推荐：

```text
agent://example.com/ai/helper
agent://example.com/tools/bot
agent://example.com/general/assistant
```

推荐：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
agent://example.com/support/customer.reply
```

---

### 10.3 动作不清晰

不推荐：

```text
agent://example.com/legal/contract
agent://example.com/finance/invoice
```

推荐：

```text
agent://example.com/legal/contract.review
agent://example.com/finance/invoice.audit
```

因为 Agent URI 应尽量表达“对象 + 动作”，而不是只表达对象。

---

## 11. 设计哲学

`agent://域名/领域/对象.动作` 的核心思想是：

```text
domain      谁提供
namespace   属于哪个业务领域
object      处理什么对象
action      执行什么动作
query       以什么权限、模式和约束执行
```

例如：

```text
agent://agenttop.com/legal/contract.review?scope=readonly
```

可以自然理解为：

```text
由 agenttop.com 提供的 legal 领域 Agent，
对 contract 执行 review 动作，
调用权限为 readonly。
```

这使 Agent URI 同时具备：

```text
可读性
唯一性
可验证性
可解析性
可治理性
可扩展性
```

---

## 12. 最小可用规范

第一版 Agent URI 至少应支持以下结构：

```text
agent://<domain>/<namespace>/<object>.<action>
```

必须包含：

```text
scheme: agent
domain: 域名
namespace: 业务领域
object: 任务对象
action: 动作
```

可选包含：

```text
version: 版本
scope: 权限
mode: 执行模式
approval: 审批要求
trust: 信任等级
risk: 风险等级
budget: 预算
currency: 货币
```

最小示例：

```text
agent://example.com/legal/contract.review
```

完整示例：

```text
agent://example.com/legal/contract.review@1.0.0?scope=readonly&mode=analyze&trust=verified&risk=R2
```
