# Project Memory

> **AI 项目记忆系统** - 会话启动即懂项目，意图感知主动推送

## 特性

- **会话启动主动注入** - 自动输出项目概览、最近变更、项目约束
- **向量语义检索** - 70% 向量相似度 + 30% 关键词混合检索
- **意图感知推送** - 识别 7 种意图类型，主动推送相关上下文
- **触发器系统** - 10+ 内置规则，关键词自动触发上下文推送
- **项目关系图** - Python/Java/JS/Go/Rust 多语言依赖分析
- **文件知识库** - 识别 11+ 种项目文件的语义含义
- **深度感知** - 执行流追踪、社区检测、模块质量评估
- **失败分析** - 检索失败、推送失败、错失机会自动分析
- **自动改进** - 根据失败分析自动补充知识库、添加触发规则
- **OpenCode 集成** - 深度生命周期钩子，自动学习决策

---

## 安装

### 方法 1：一键安装（推荐）

```bash
# 1. 下载
git clone https://github.com/coffeeliuwei/project-memory.git
cd project-memory

# 2. 运行安装脚本
python install.py
```

安装脚本会自动：
- 复制文件到 `~/.agents/skills/project-memory/`
- 注册到 OpenCode 的 `.skill-lock.json`
- 完成后重启 OpenCode 即可使用

### 方法 2：手动安装

```bash
# 复制到 skills 目录
cp -r project-memory ~/.agents/skills/
```

然后在 OpenCode 中运行：
```
skill("project-memory")
```

### 方法 3：Skills 市场（待发布）

OpenCode 中搜索 `project-memory` 并安装。

---

## 依赖

```bash
pip install lancedb pyarrow
```

---

## 快速开始

### 最简单的方式

```python
from project_memory import get_pm

pm = get_pm()  # 自动检测项目、自动初始化
```

启动时自动输出：

```
============================================================
🧠 [ProjectMemory] 项目记忆已加载 - AI 已获得项目上下文
============================================================
📁 项目: /path/to/your/project
📊 知识库: 256 条记录
   ├─ 函数: 208
   ├─ 类: 25
   └─ 文档: 23

🔍 [主动推送] 最近索引的关键内容:
   1. [code_function] get_user
      └─ services/user.py:42
   2. [code_class] UserRepository
      └─ repositories/user.py:15

⚠️ [主动推送] 项目约束:
   • 所有数据库操作必须通过 Repository 层
   • API 端点必须有版本号

💡 [提示] AI 已加载项目记忆，可以：
   • 检索项目代码：pm.search('关键词')
   • 检查约束：pm.check_action('操作描述')
   • 学习决策：pm.learn('决策内容')
============================================================
```

---

## 核心功能

### 1. 语义检索

混合检索策略：向量相似度 70% + 关键词匹配 30%

```python
results = pm.search("用户认证流程")

# 返回结果包含详细评分
for r in results:
    print(f"{r['name']} - 总分: {r['score']:.3f}")
    print(f"  向量: {r['vector_score']:.3f}, 关键词: {r['keyword_score']}")
```

### 2. 意图感知

自动识别 7 种意图类型，主动推送相关上下文：

```python
from intent_aware import IntentAwareRetriever

retriever = IntentAwareRetriever(pm)

# 用户消息
intent = retriever.analyze_intent("我要实现一个用户管理系统")
# → IntentContext(intent_type='实现', entity='用户管理系统', action='check_existing')

# 主动推送
push = retriever.proactive_push("我要实现一个用户管理系统")
# → 自动检测已有实现并推送相关代码
```

**支持的意图类型：**
- 实现 (implement) - 检测已有实现
- 创建 (create) - 检测相似组件
- 修改 (modify) - 检测影响范围
- 删除 (delete) - 检测依赖
- 优化 (optimize) - 检测瓶颈
- 修复 (fix) - 检测相关错误
- 设计 (design) - 检查约束

### 3. 触发器系统

关键词自动触发上下文推送：

```python
from trigger_system import ProactiveTrigger

trigger = ProactiveTrigger(pm)

# 检查并触发
results = trigger.check_and_trigger({
    "message": "实现JWT认证",
    "file_ext": ".py"
})
# → 触发 "认证关键词" 规则，推送认证相关实现
```

**内置触发规则：**
- 部署关键词 → 部署流程
- 认证关键词 → 认证实现
- 测试关键词 → 测试模式
- 错误关键词 → 错误处理
- API关键词 → API模式
- 数据库关键词 → 数据库相关

### 4. 项目关系图

多语言依赖分析和影响分析：

```python
# 获取文件依赖
deps = pm.get_dependencies("services/user.py")
# → [{'file': 'models/user.py', 'type': 'import'}, ...]

# 获取被依赖
dependents = pm.get_dependents("models/user.py")
# → 哪些文件依赖这个文件

# 影响分析
impact = pm.analyze_impact("models/user.py")
# → 修改这个文件会影响哪些符号

# 继承链
chain = pm.get_inheritance_chain("UserRepository")
# → ['BaseRepository', 'object']
```

**支持的语言：**
- Python (`.py`)
- Java (`.java`)
- JavaScript/TypeScript (`.js`, `.ts`, `.tsx`)
- Go (`.go`)
- Rust (`.rs`)

### 5. 文件知识库

识别项目文件的语义含义：

```python
# 获取文件知识
knowledge = pm.get_file_knowledge("requirements.txt")
# → {
#     'name': 'Python 依赖声明',
#     'purpose': '列出项目所需的所有 Python 包及版本',
#     'importance': 'critical',
#     'commands': 'pip install -r requirements.txt'
# }

knowledge = pm.get_file_knowledge("pom.xml")
# → {
#     'name': 'Maven 项目配置',
#     'purpose': 'Maven 构建配置、依赖管理',
#     'importance': 'critical',
#     'commands': 'mvn clean install / mvn compile'
# }
```

**支持的文件类型：**

| Python | Java | Node.js | Go | Rust |
|--------|------|---------|-----|------|
| `requirements.txt` | `pom.xml` | `package.json` | `go.mod` | `Cargo.toml` |
| `pyproject.toml` | `build.gradle` | `tsconfig.json` | `go.sum` | `Cargo.lock` |
| `setup.py` | `settings.gradle` | `webpack.config.js` | | |
| `app.py` | `Application.java` | `app.js` | `main.go` | `main.rs` |
| `models.py` | `*Repository.java` | `models/` | | |
| `views.py` | `*Controller.java` | `routes/` | | |

### 6. 学习决策

从对话中自动提取决策和约束：

```python
pm.learn(
    "我决定使用 JWT 认证，因为安全性更高",
    "好的，已记录这个决策"
)
# → 自动提取设计决策

pm.learn(
    "禁止在测试中使用真实的数据库连接",
    "好的，已记录这个约束"
)
# → 自动提取约束规则
```

### 7. 约束检查

执行前检查是否违反项目约束：

```python
is_valid, violations = pm.check_action("在 Controller 中直接写 SQL")
# → (False, ["违反约束：所有数据库操作必须通过 Repository 层"])

is_valid, violations = pm.check_action("创建 /api/v1/users 端点")
# → (True, [])
```

### 8. 文件自动同步

监视文件变化，自动同步知识库和关系图：

```python
pm.sync()  # 手动同步

# 或启动文件监视器
pm.start()  # 自动监视文件变化
```

### 9. 深度感知（执行流 + 社区）

从入口点追踪完整调用链，知道"修改会影响哪些流程"：

```python
# 追踪执行流
process = pm.trace_process("handle_login")
print(f"入口点: {process['entry_point']}")
print(f"深度: {process['depth']}")
for node in process['nodes']:
    print(f"  {node['depth']} -> {node['symbol']}")

# 发现所有流程
processes = pm.find_all_processes()
for p in processes:
    print(f"{p['name']}: {p['total_symbols']} 个步骤")
```

自动发现功能模块，知道"代码有哪些模块"、"质量如何"：

```python
# 检测功能社区
communities = pm.detect_communities()
for c in communities:
    print(f"模块: {c['name']} (内聚度: {c['cohesion']:.2f})")
    print(f"  成员: {c['size']} 个")
    print(f"  文件: {c['files']}")

# 获取低内聚模块（需要重构）
low_quality = pm.get_low_cohesion_communities(threshold=0.6)
for c in low_quality:
    print(f"⚠️ {c['name']} 需要重构 (内聚度 {c['cohesion']:.2f})")

# 查询符号所属模块
community = pm.get_community_for_symbol("validate_user")
print(f"validate_user 属于: {community['name']}")
```

增强版影响分析（结合执行流）：

```python
# 分析修改影响
impact = pm.analyze_impact_enhanced("services/auth.py")
print(f"直接影响: {impact['direct_impact']} 个符号")
print(f"间接影响: {impact['indirect_impact']} 个符号")
print(f"影响模块数: {len(impact['affected_communities'])}")
print(f"风险等级: {impact['risk_level']}")
```

### 10. 失败分析与自动改进

**核心理念：失败才是最关键的** - 通过分析失败发现项目记忆的不足，驱动迭代改进。

#### 失败分析

自动检测三类失败场景：

```python
# 获取失败分析
failures = pm.get_failure_analysis()

# 检索失败 → 知识库盲区
for f in failures['search_failures']:
    print(f"检索失败: {f['query']}")
    print(f"  建议: {f['suggested_action']}")

# 推送失败 → 策略问题
for f in failures['push_failures']:
    print(f"推送失败: {f['context_type']}")
    print(f"  原因: {f['failure_reason']}")

# 错失机会 → 感知问题
for mo in failures['missed_opportunities']:
    print(f"错失机会: {mo['detected_topics']}")
    print(f"  原因: {mo['reason']}")
```

#### 改进建议生成

根据失败自动生成改进建议：

```python
# 获取改进建议
suggestions = pm.generate_improvement_suggestions()

# 知识库补充建议
for s in suggestions['knowledge_gaps']:
    print(f"补充: {s['action']} (优先级: {s['priority']})")

# 触发器改进建议
for s in suggestions['trigger_rules']:
    print(f"添加规则: {s['action']}")

# 新功能建议
for s in suggestions['new_features']:
    print(f"新功能: {s['feature']} - {s['reason']}")
```

#### 一键自动改进

```python
# 建议模式 - 只生成建议
result = pm.auto_improve(mode="suggest")
print(f"生成了 {len(result['suggestions'])} 个建议")

# 自动模式 - 自动执行改进
result = pm.auto_improve(mode="auto")
print(f"应用了 {len(result['applied'])} 个改进")

# 交互模式 - 询问用户后执行
result = pm.auto_improve(mode="interactive")
```

#### 手动改进操作

```python
# 补充知识库
pm.auto_supplement_knowledge("缓存策略")

# 添加触发规则
pm.add_trigger_rule("性能优化", query="性能相关代码")

# 查看待处理建议
pending = pm.get_pending_suggestions()
for s in pending:
    print(f"  [{s['priority']}] {s['title']}")

# 应用单个建议
pm.apply_suggestion(pending[0]['id'])

# 查看改进历史
history = pm.get_improvement_history(days=7)

# 打印改进报告
pm.print_improvement_report()
```

#### 报告输出

会话结束时自动打印失败分析和改进报告：

```python
# 打印失败分析报告
pm.print_failure_report()

# 输出示例：
# ============================================================
# [失败] 失败分析报告
# ============================================================
#
# 【检索失败】知识库盲区:
#   [X] '缓存策略' - query
#      建议: 补充 缓存策略 相关代码到知识库
#
# 【错失机会】感知问题:
#   [X] 用户讨论 '['性能', '优化']' 但 AI 没调用 pm
#      原因: 触发器规则没有匹配到关键词
#
# 【改进建议】:
#   知识库补充:
#     - 补充 缓存策略 相关代码到知识库 (失败 3 次)
#   触发器改进:
#     - 添加触发规则: 性能 关键词 → 推送相关代码
# ============================================================
```

---

## OpenCode 集成

### 生命周期钩子

```python
from opencode_hooks import OpenCodeIntegration

integration = OpenCodeIntegration()

# 会话开始
context = integration.on_session_start(session_id="my_session")
# → 自动初始化项目记忆，注入项目概览

# 消息处理前
injections = integration.on_before_message(
    role="user",
    message="实现一个缓存系统",
    file_ext=".py"
)
# → 意图感知推送 + 触发器检查

# 消息处理后
result = integration.on_after_message(
    role="user",
    message="我决定使用 Redis 作为缓存",
    ai_response="好的，已记录..."
)
# → 自动学习决策
```

### 配置钩子（可选）

在项目的 `.opencode/hooks/` 目录下创建配置：

```yaml
# .opencode/hooks/session_start.yaml
module: project_memory.opencode_hooks
function: on_session_start
```

---

## API 参考

### ProjectMemoryAuto

主入口类，集成所有功能：

```python
from project_memory import get_pm

pm = get_pm()

# 检索
pm.search(query, top_k=10)

# 学习
pm.learn(user_message, ai_response="")

# 检查约束
pm.check_action(action) -> (bool, List[str])

# 同步
pm.sync()

# 关系图
pm.get_dependencies(file_path)
pm.get_dependents(file_path)
pm.analyze_impact(file_path)
pm.get_callers(symbol_name)
pm.get_inheritance_chain(class_name)

# 文件知识
pm.get_file_knowledge(filename)

# 统计
pm.get_stats()

# 生命周期
pm.start()
pm.stop()

# 深度感知
pm.trace_process(entry_symbol, max_depth=10)  # 追踪执行流
pm.find_all_processes()  # 发现所有流程
pm.detect_communities()  # 检测功能模块
pm.get_community_for_symbol(symbol)  # 查询符号所属模块
pm.get_low_cohesion_communities(threshold=0.6)  # 获取低内聚模块
pm.analyze_impact_enhanced(symbol)  # 增强版影响分析

# 失败分析（新增）
pm.get_failure_analysis()  # 获取失败分析
pm.generate_improvement_suggestions()  # 生成改进建议
pm.get_interaction_stats()  # 获取交互统计
pm.get_interaction_history(days=7)  # 获取历史统计
pm.print_failure_report()  # 打印失败分析报告

# 自动改进（新增）
pm.auto_improve(analysis, mode)  # 根据失败分析自动改进
pm.auto_supplement_knowledge(topic, files)  # 自动补充知识库
pm.add_trigger_rule(keyword, query)  # 添加触发规则
pm.get_pending_suggestions()  # 获取待处理建议
pm.apply_suggestion(suggestion_id)  # 应用单个建议
pm.get_improvement_history(days=7)  # 获取改进历史
pm.print_improvement_report()  # 打印改进报告

# 会话保存（新增）
pm.save_session()  # 手动保存当前会话数据
```

### 11. 会话保存触发机制

交互监控数据需要正确保存才能统计命中率。系统提供多种触发方式：

#### 自动触发时机

| 场景 | 触发方式 | 说明 |
|------|---------|------|
| 每次检索后 | 自动 | `record_search()` 后实时保存 |
| 调用 `pm.stop()` | 自动 | 停止时保存并结束会话 |
| Python 进程退出 | 自动 | `atexit` 注册钩子 |

#### 手动触发

```python
# 会话压缩前、切换项目前、退出前
pm.save_session()
```

#### Claude Code CLI 配置（移植备用）

在 `.claude/settings.json` 中配置 Stop Hook：

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python -c \"import sys; sys.path.insert(0, '~/.agents/skills/project-memory'); import __init__ as pm; pm.get_pm().save_session()\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

#### OpenCode CLI 配置（移植备用）

类似配置，检查 OpenCode 的退出事件钩子。

### 全局函数

```python
from project_memory import (
    get_pm,      # 获取单例实例
    reset_pm,    # 重置实例
    search,      # 快捷检索
    learn,       # 快捷学习
    check_action, # 快捷检查
    sync,        # 快捷同步
)
```

---

## 项目结构

安装后，每个项目自动生成：

```
your-project/
├── .project_memory/      # 知识库（自动）
│   ├── chroma.sqlite3    # 向量数据库
│   ├── file_hashes.json  # 文件哈希（增量同步）
│   └── relationships.json # 关系图数据
└── PROJECT_LOGIC.md      # 项目逻辑（自动+手动）
```

### PROJECT_LOGIC.md 示例

```markdown
# 项目逻辑

## 设计决策

### JWT 认证
- **决策**：使用 JWT 认证
- **原因**：安全性更高
- **创建时间**：2024-01-15

## 约束规则

### 数据库操作
- **约束**：所有数据库操作必须通过 Repository 层
- **原因**：保持架构清晰
```

---

## 数据流

```
┌─────────────────────────────────────────────────────────────┐
│                    会话启动                                  │
│                         ↓                                    │
│    ┌─────────────────────────────────────────────────┐      │
│    │  session_start.py                                │      │
│    │  - 自动检测项目根目录                            │      │
│    │  - 初始化 ProjectMemoryAuto                      │      │
│    │  - 输出项目概览                                  │      │
│    │  - 推送最近变更                                  │      │
│    │  - 提示项目约束                                  │      │
│    └─────────────────────────────────────────────────┘      │
│                         ↓                                    │
│    ┌─────────────────────────────────────────────────┐      │
│    │  用户消息                                        │      │
│    │      ↓                                           │      │
│    │  意图识别 (intent_aware.py)                      │      │
│    │      ↓                                           │      │
│    │  触发器检查 (trigger_system.py)                  │      │
│    │      ↓                                           │      │
│    │  语义检索 (core.py - 向量 + 关键词)             │      │
│    │      ↓                                           │      │
│    │  文件知识增强 (__init__.py)                      │      │
│    │      ↓                                           │      │
│    │  AI 获得完整上下文                              │      │
│    └─────────────────────────────────────────────────┘      │
│                         ↓                                    │
│    ┌─────────────────────────────────────────────────┐      │
│    │  AI 响应                                         │      │
│    │      ↓                                           │      │
│    │  自动学习决策和约束 (logic_manager.py)          │      │
│    │      ↓                                           │      │
│    │  更新 PROJECT_LOGIC.md                          │      │
│    └─────────────────────────────────────────────────┘      │
│                         ↓                                    │
│    ┌─────────────────────────────────────────────────┐      │
│    │  文件变化                                        │      │
│    │      ↓                                           │      │
│    │  watch_and_sync.py                              │      │
│    │      ↓                                           │      │
│    │  增量同步知识库                                  │      │
│    │      ↓                                           │      │
│    │  更新关系图                                      │      │
│    └─────────────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

---

## 与 OpenCode 协同

### 协同工作

```
OpenCode Session Memory        project-memory
(会话历史)                        (项目专属)
     ↓                              ↓
记住"你说了什么"              理解"你为什么这么说"
     ↓                              ↓
                 结合使用
                     ↓
              AI 完全理解你的意图
```

---

## 发布到 GitHub

这个技能已经可以直接发布：

1. 创建 GitHub 仓库 `project-memory`
2. 上传整个 `project-memory/` 文件夹
3. 其他人 `git clone` 或直接下载

---

## 许可证

MIT