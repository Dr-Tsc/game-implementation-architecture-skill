# Game Implementation Architecture Skill

A reusable Codex skill for turning game design documents into a practical, modular implementation architecture.

## Purpose

This skill helps Codex analyze an existing game design before writing large amounts of code.

It focuses on gradually converting design rules into a maintainable software architecture through:

Design  
→ Architecture Hypothesis  
→ Runtime Scenario  
→ Validation  
→ Revision  
→ Implementation

The goal is not to create a perfect architecture immediately, but to continuously refine it as the project becomes clearer.

## What It Helps With

The skill can help identify and define:

- Modules and shared systems
- Data versus runtime logic
- System responsibilities and boundaries
- State Ownership
- Source State and Derived State
- Persistent, Run, Combat, and Attack runtime states
- Dependency Maps
- Interface Contracts
- Combat effects and execution phases
- Flow coordinators
- UI Flow
- Save System boundaries
- Minimum Context and module dependencies

## Core Principles

### Single Responsibility

Each module should have a clear responsibility and should not control unrelated systems.

### State Ownership

Important mutable state should have one clear owner.

Avoid storing multiple independent copies of the same game state.

### Separate Permanent and Runtime State

Permanent character progression should not be repeatedly modified to represent temporary combat effects.

Prefer:

`Permanent State + Runtime Modifiers = Effective Runtime State`

### Explicit Interfaces

Modules should communicate through clear contracts instead of freely modifying each other's internal state.

### Minimal Dependencies

A module should depend only on the systems and information it actually needs.

Do not load unrelated project documentation simply because it may become relevant in the future.

### Progressive Disclosure

Start with the minimum required context.

Load additional documentation only when a real dependency is discovered.

### Validate Through Real Scenarios

Architecture should be tested against actual gameplay flows such as:

Equipment Change  
→ Attribute Update  
→ Combat  
→ Temporary Effects  
→ Monster Death  
→ Rewards  
→ Progression

If the scenario reveals duplicated state, unclear ownership, circular dependencies, or excessive coupling, revise the architecture before implementation.

## What This Skill Does Not Contain

This skill should not contain project-specific game rules, balance values, item lists, skills, monsters, or progression data.

Those belong in the project's official documentation and remain the project's Single Source of Truth.

The separation should be:

- **Skill** — reusable development methodology
- **Project Documentation** — game rules and project facts
- **AGENTS.md** — project-wide Codex working rules
- **Prompt** — the specific task being performed

## Example Usage

A task can simply say:

> Use the `game-implementation-architecture` skill.  
> Analyze the equipment system using the current official project documentation.  
> Identify responsibilities, state ownership, dependencies, and required interfaces before implementation.  
> Do not invent undefined game rules.

## Recommended Workflow

1. Read the minimum required official documentation.
2. Identify the relevant systems.
3. Define responsibilities and non-responsibilities.
4. Determine State Ownership.
5. Build the Dependency Map.
6. Define Interface Contracts.
7. Test the architecture with real gameplay scenarios.
8. Revise problems before coding.
9. Define Acceptance Criteria.
10. Implement and verify with tests.

## Main Goal

Enable Codex to develop large games incrementally, modularly, and safely without requiring the entire project to be loaded into context for every task.
