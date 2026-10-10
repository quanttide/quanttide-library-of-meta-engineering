# qtcloud-delib studio 本体结构

这份结构录自 qtcloud-delib 的 studio 与其服务端 provider，字段与状态照源码原样，逐条可回查到标注的文件。studio 的定位是「基于罗伯特议事规则管理议事和决议的 SaaS 平台」（`docs/index.md`）。

## 议题 Topic

议题是从动议到决议的完整生命周期载体，状态机按罗伯特议事规则的五流程走：

```text
proposed（动议）→ seconded（附议）→ debated（辩论）
        → voted（表决）→ resolved（决议） | rejected（否决）
```

```text
Topic ::= ID           : UUID
          Name         : slug，唯一
          Title        : 标题
          Content      : 正文
          Category     : 分类（治理、审计、档案、技术）
          Status       : proposed | seconded | debated | voted | resolved | rejected
          ProposerID   : 动议人，账号用户 ID
          SeconderIDs  : 附议人
          Votes        : VoteResult（赞成、反对、弃权）
          ResolutionID : 通过后关联的决议
```

状态转移是强制的：未附议不能辩论，未辩论不能表决，未表决不能归档，违者报 `ErrBadState`（`internal/topic/service_test.go`）。端点已全套挂出：`GET/POST /topics`、`/second`、`/debate`、`/vote`、`/close`（`internal/topic/transport.go`）。

## 决议 Resolution

决议是表决通过后落档的决策记录，五个字段：

```text
Resolution ::= id       : UUID，主键
               name     : slug，取自文件名，唯一索引
               title    : 概括「决定了什么」
               content  : 决议陈述，纯文本（依据、表决情况、执行安排）
               category : 分类（治理、审计、档案、技术）
```

建模原则写在源码注释里：「结构从实际议事档案标本中长出，不预设执行字段」（`docs/resolution.md`）。studio 的 `models/resolution.dart` 与 provider 的 `internal/resolution/model.go` 同构；种子数据取自真实决议标本（`docs/dev-guide/seed-data.md`）。

## 关系与空位

议题与决议是一对零或一：表决通过时回填 `ResolutionID`，议题的过程到此封存，决议只存结果。提案与附议记的都是账号用户 ID，模型里没有机构与代表；议题不挂在任何议程条目下，没有议程容器，也没有跨机构交换的挂点。studio 界面目前只有决议列表与详情两页，议题页是占位（`src/studio/lib/screens/topic_list.dart` 注释称等待服务端 API，实际 provider 已挂全套端点，该注释已过时）。
