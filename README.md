# Game Implementation Architecture Skill  
# 游戏实现架构 Skill

A reusable Codex skill for turning existing game design documents into a modular and maintainable Implementation Architecture.

一个面向 Codex 的可复用 Skill（技能），用于将已有游戏设计逐步反推为适合长期、模块化开发的 Implementation Architecture（实现架构）。

## Purpose / 用途

Use this skill before large-scale implementation to clarify:

- Module（模块）与 Shared System（共享系统）
- State Ownership（状态所有权）
- Source State（源状态）与 Derived State（派生状态）
- Runtime State（运行时状态）
- Dependency Map（依赖地图）
- Interface Contract（接口契约）
- Combat Flow（战斗流程）
- UI Flow（用户界面流程）
- Save System（存档系统）
- Minimum Context（最小上下文）

Core workflow:

`Game Design → Architecture Hypothesis → Runtime Validation → Revision → Implementation`

核心流程：

`游戏设计 → 架构假设 → 实际运行验证 → 修正 → 实现`

## Core Principles / 核心原则

- Each module should have a clear responsibility.  
  每个模块只负责明确职责。

- Important mutable state should have one clear owner.  
  重要可变状态应有唯一 Owner（所有者）。

- Separate permanent state from temporary runtime state.  
  永久状态与战斗、推进等临时状态分离。

- Prefer:  
  `Permanent State + Runtime Modifiers = Effective State`  
  优先采用：  
  `永久状态 + 临时修正 = 当前有效状态`

- Modules communicate through explicit Interface Contracts.  
  模块之间通过明确的 Interface Contract（接口契约）连接。

- Load only the minimum required project context.  
  只读取当前任务真正需要的最小项目资料。

- Validate architecture through real gameplay scenarios.  
  使用真实游戏流程验证架构，而不是只看静态模块图。

## What This Skill Does Not Store / 不保存的内容

This skill should not contain project-specific:

- game rules
- balance values
- items
- monsters
- skills
- drop rates
- content data

本 Skill（技能）不保存具体项目的游戏规则、数值、装备、怪物、技能、掉落概率或内容数据。

Recommended separation:

- **Skill** — reusable methodology / 可复用方法论
- **Project Docs** — game rules and SSOT / 游戏规则与 SSOT
- **AGENTS.md** — project-wide Codex rules / 项目长期工作规则
- **Prompt** — current task / 当前具体任务

## Example / 使用示例

> Use the `game-implementation-architecture` skill.  
> Analyze the equipment system using the current official project documents.  
> Define responsibilities, State Ownership, dependencies, and interfaces before implementation.  
> Do not invent undefined game rules.

> 使用 `game-implementation-architecture` Skill（游戏实现架构技能）。  
> 基于当前正式项目资料分析装备系统。  
> 在实现前明确职责、State Ownership（状态所有权）、Dependency（依赖）和 Interface Contract（接口契约）。  
> 不得自行补充未定义的游戏规则。

## Goal / 目标

Enable Codex to develop large games incrementally, modularly, testably, and with limited Context（上下文）, without loading the entire project for every task.

让 Codex 能够在有限 Context（上下文）下，对大型游戏进行长期、模块化、可测试、可扩展的开发，而不需要每次都读取整个项目。
