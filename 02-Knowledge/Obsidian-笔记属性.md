---
type: knowledge
status: reference
area: 知识
review: monthly
created: 2026-06-23
updated: 2026-06-23
---

# Obsidian 笔记属性

## 目标

给当前知识库中的 Markdown 文件建立一套统一、轻量、可筛选的 Obsidian 笔记属性。

触发场景：

- 新增笔记属性。
- 编辑已有笔记属性。
- 批量补全笔记属性。
- 调整属性字段、取值或目录映射。

普通内容整理、方案讨论、日志总结不需要读取本规则。

原则：

- 字段少，先能用。
- 以目录默认值为主，不为每篇笔记做过度定制。
- 属性用于筛选、复盘和后续整理，不替代正文内容。
- 批量写入前，先确认字段、取值范围和目录映射。

## 推荐统一属性

```yaml
---
type:
status:
area:
topic:
source:
created:
updated:
review:
priority:
---
```

## 字段说明

| 字段         | 含义     | 推荐取值                                                                                                    |
| ---------- | ------ | ------------------------------------------------------------------------------------------------------- |
| `type`     | 笔记类型   | `inbox` / `project` / `knowledge` / `record` / `archive` / `template` / `prompt` / `resource` / `skill` |
| `status`   | 当前状态   | `todo` / `doing` / `done` / `paused` / `archived` / `reference`                                         |
| `area`     | 所属大类   | `AI协作` / `工程` / `项目` / `工作日志` / `个人成长` / `提示词` / `资源`                                                   |
| `topic`    | 主题标签   | `知识库` / `Codex` / `Obsidian` / `AI Coding` / `日常复盘`                                                     |
| `source`   | 来源     | `self` / `chat` / `second-brain` / `project` / `web` / `book`                                           |
| `created`  | 创建日期   | `2026-06-23`                                                                                            |
| `updated`  | 最后整理日期 | `2026-06-23`                                                                                            |
| `review`   | 复盘周期   | `weekly` / `monthly` / `no`                                                                             |
| `priority` | 重要性    | `high` / `medium` / `low`                                                                               |

## 必须保留的核心字段

先保留 4 个核心字段：

```yaml
type:
status:
area:
review:
```

其他字段可以后续补充。

## 按目录默认归类

```yaml
00-Inbox:
  type: inbox
  status: todo
  review: weekly

01-Projects:
  type: project
  status: doing

02-Knowledge:
  type: knowledge
  status: reference

98-Records:
  type: record
  review: weekly

99-Archive:
  type: archive
  status: archived
  review: no
```

## 批量写入前需要确认

1. 是否所有 Markdown 文件都写入完整 9 个字段，还是先只写入 4 个核心字段。
2. `area` 是否按目录推断，还是按文件内容推断。
3. 已经有 YAML frontmatter 的文件，是否保留原字段并只补缺失字段。
4. `created` 使用文件创建时间、文件修改时间，还是统一用批量处理日期。
5. `updated` 是否统一写入批量处理当天日期。

## 压缩规则

先用 `type / status / area / review` 建立最小可用属性系统；目录给默认值，特殊文件再单独修正。
