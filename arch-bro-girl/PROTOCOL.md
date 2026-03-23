# Multi-Session Collaboration Protocol

本协议用于在以下目录结构下协作使用 GSD：

- `arch/`：项目根目录，`arch` 会话启动位置
- `arch/bro/`：`bro` 会话启动位置
- `arch/girl/`：`girl` 会话启动位置

## 0. Soft-link rule

为保证 `bro` 与 `girl` 在子目录中也能使用同一套 GSD 项目上下文，允许：

- `arch/bro/.planning -> ../.planning`
- `arch/girl/.planning -> ../.planning`

这些软链接仅用于让子目录会话访问同一套 `.planning/`。
唯一真实真相源始终是：

- `arch/.planning/`

禁止在 `bro/` 或 `girl/` 中创建独立、真实的 `.planning/` 目录。

## 1. 单一真相源

整个项目只有一套共享 planning 文件，位于：

- `arch/.planning/`

包括：
- `arch/.planning/PROJECT.md`
- `arch/.planning/REQUIREMENTS.md`
- `arch/.planning/ROADMAP.md`
- `arch/.planning/STATE.md`
- `arch/.planning/HANDOFF.json`

协作补充文件包括：
- `arch/.planning/phases/N-ARCH-BRIEF.md`
- `arch/.planning/phases/N-BRO-REPORT.md`
- `arch/.planning/phases/N-GIRL-REPORT.md`
- `arch/.planning/phases/N-UI-SPEC.md`
- `arch/.planning/phases/N-UAT.md`
- `arch/.planning/collab/DECISIONS.md`
- `arch/.planning/collab/BRO-TO-ARCH.md`
- `arch/.planning/collab/GIRL-TO-ARCH.md`

## 2. 角色职责

### arch
负责：
- 项目级理解
- phase 规划
- 派单
- 决策
- 验收

不负责：
- 主力后端实现
- 主力前端实现

### bro
负责：
- 后端实现
- 后端测试
- 提交 `BRO-REPORT`
- 提交给 `arch` 的阻塞与建议

不负责：
- 项目级初始化
- 最终决策
- 最终验收

### girl
负责：
- 前端实现
- 前端测试
- 提交 `GIRL-REPORT`
- 提交给 `arch` 的阻塞与建议

不负责：
- 项目级初始化
- 最终决策
- 最终验收

## 3. 命令权限

### arch 可使用
- `/gsd:map-codebase`
- `/gsd:new-project`
- `/gsd:discuss-phase N`
- `/gsd:plan-phase N`
- `/gsd:ui-phase N`
- `/gsd:progress`
- `/gsd:next`
- `/gsd:review`
- `/gsd:verify-work N`
- `/gsd:pause-work`
- `/gsd:resume-work`
- `/gsd:session-report`

### bro 可使用
- `/gsd:execute-phase N`
- `/gsd:review`
- `/gsd:progress`
- `/gsd:next`
- `/gsd:pause-work`
- `/gsd:resume-work`
- `/gsd:session-report`

### girl 可使用
- `/gsd:execute-phase N`
- `/gsd:ui-review N`
- `/gsd:review`
- `/gsd:progress`
- `/gsd:next`
- `/gsd:pause-work`
- `/gsd:resume-work`
- `/gsd:session-report`

补充规则：
- `bro` 与 `girl` 默认禁止 `/gsd:map-codebase`
- `bro` 与 `girl` 默认禁止 `/gsd:new-project`
- `girl` 只有在 `arch` 明确要求时才发起 `/gsd:ui-phase N`

## 4. 开工前读取顺序

### arch
1. `.planning/STATE.md`
2. `.planning/ROADMAP.md`
3. `.planning/REQUIREMENTS.md`
4. `.planning/phases/`
5. `.planning/collab/DECISIONS.md`
6. `.planning/collab/BRO-TO-ARCH.md`
7. `.planning/collab/GIRL-TO-ARCH.md`

### bro
从 `arch/bro/` 目录出发，必须读取：
1. `.planning/STATE.md`
2. `.planning/phases/N-ARCH-BRIEF.md`
3. `.planning/collab/DECISIONS.md`
4. 当前 phase 相关 requirements / contracts
5. 如存在，读取 `.planning/HANDOFF.json`

### girl
从 `arch/girl/` 目录出发，必须读取：
1. `.planning/STATE.md`
2. `.planning/phases/N-ARCH-BRIEF.md`
3. `.planning/collab/DECISIONS.md`
4. `.planning/phases/N-UI-SPEC.md`（如有）
5. 如存在，读取 `.planning/HANDOFF.json`

## 5. 收工后必须写入

### arch
- `.planning/STATE.md`
- `.planning/phases/N-ARCH-BRIEF.md`（如有新安排）
- `.planning/collab/DECISIONS.md`（如有裁决）
- 必要时执行 `/gsd:pause-work`

### bro
从 `arch/bro/` 目录出发，必须写：
- `.planning/phases/N-BRO-REPORT.md`

必要时写：
- `.planning/collab/BRO-TO-ARCH.md`

### girl
从 `arch/girl/` 目录出发，必须写：
- `.planning/phases/N-GIRL-REPORT.md`

必要时写：
- `.planning/collab/GIRL-TO-ARCH.md`

## 6. 验收规则

- `bro` / `girl` 只能声明“实现完成、自测通过、已提交报告”
- `bro` / `girl` 不能声明“phase 最终完成”
- 最终状态只能由 `arch` 更新为：
  - `accepted`
  - `needs-rework`
  - `blocked`

## 7. 冲突升级

以下冲突必须升级给 `arch`：
- API 契约冲突
- 字段语义冲突
- UI 与后端能力冲突
- 范围理解冲突
- 测试标准冲突

正式结论必须写入：
- `arch/.planning/collab/DECISIONS.md`

## 8. 工作流

### arch
1. 在 `arch/` 根目录启动会话
2. 读取 `.planning/STATE.md`
3. 必要时运行项目级命令
4. 产出或更新 `N-ARCH-BRIEF.md`
5. 更新 `STATE.md`
6. 收取 `bro/girl` 报告
7. 裁决冲突
8. 验收并签收

### bro
1. 在 `arch/bro/` 启动会话
2. 通过 `.planning` 软链接读取根级项目上下文
3. 执行实现与测试
4. 运行允许的执行类命令
5. 写 `N-BRO-REPORT.md`
6. 如有阻塞，写 `BRO-TO-ARCH.md`

### girl
1. 在 `arch/girl/` 启动会话
2. 通过 `.planning` 软链接读取根级项目上下文
3. 执行实现与测试
4. 运行允许的执行类命令
5. 写 `N-GIRL-REPORT.md`
6. 如有阻塞，写 `GIRL-TO-ARCH.md`
