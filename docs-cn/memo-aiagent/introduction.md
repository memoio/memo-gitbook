# MEMO AI Agent — Introduction

## Overview

MEMO 致力于构建一个可信、开放且可扩展的 Web3 原生基础设施，使身份（DID）、认证（Attestation）、数据可用性（DA）及支付能力能够作为通用服务层提供给各类应用。在 AI Agent 崛起的背景下，MEMO 正在推动下一阶段的发展：
**将高可信度、可验证、可支付、可交互的区块链能力无缝嵌入 AI Agent，让 Agent 成为真正能够处理链上资产、访问链上数据、执行链上任务的自主体。**

在这个背景下，MEMO AIAGENT 文档目录将介绍一整套让 Agent 在区块链上安全地执行交易、调用协议与完成支付的机制，包括：

* x402：AI Agent 的链上支付能力规范

* EIP-8004：Agent 与链上服务间的标准化调用协议

* DID / Attestation / DA：Agent 的可信身份与可信执行基础设施

这份 Introduction 文档是整个 AI Agent 文档体系的入口，将帮助你逐步理解 MEMO 如何构建一个 **可验证、可支付、可协作** 的 Agent 生态。

## Why AI Agent Needs MEMO?

传统 AI Agent 虽然能执行逻辑，但缺乏：

* **可信身份**（无法证明“我是谁”）

* **可信权限**（无法证明“我被允许执行哪些动作”）

* **可验证执行**（无法证明“我真的做了这个动作”）

* **原生支付能力**（无法在链上处理费用、跨链转账、任务支付）

* **可审核的结果证明**（无法提供可验证的执行证据）

MEMO 的基础服务正好对应这些缺口：

| Agent 能力需求 | MEMO 提供 |
| -------------- | ---------- |
| 身份 | DID（EIP-7007） |
| 权限与证明 | Attestation |
| 执行可信度 | TEE + DA |
| 支付 | x402 + Pay Facilitator |
| 调用链上服务 | EIP-8004 |
| 数据源 | DA / Storage |

这样，AI Agent 不再只是“一个能聊天能执行任务的程序”，
而是真正变成 **链上身份 + 链上支付能力 + 可验证执行 + 钱包原生能力 + 服务调用** 的自主体。

## MEMO Architecture for AI Agent

![MEMO Hierarchical Architecture](./Hierarchical-Architecture.png)

上图展示了 MEMO 的分层架构。从底层到上层分别是：

### Infrastructure 层

TEE、Storage、LLM —— 为 Agent 的执行与数据提供底层算力与环境。

### Core Services 层

由 MEMO 的核心模块组成：

* **DID（Authentication）**：为 Agent 提供独立身份

* **Attestation**：为 Agent 的权限、声明和行为提供证明

* **DA（Data Availability）**：保证 Agent 与链上任务间的数据可用性

* **Pay Facilitator（x402 & extensions）**：提供链上支付能力，如 gas 支付、跨链支付

### Access 层

通过 **EIP-8004 标准协议**（有 Python / JavaScript SDK），为 Agent、应用、MCP Tools 提供统一的服务调用接口。

### Application 层

最终由 Apps、Agents、MCP Tools、Launchpad 构成。

这张图展示了：
**AI Agent 是建立在 MEMO 全套基础设施之上的可信执行体，能够通过 EIP-8004 接入 MEMO 的所有链上功能。**

## Trustless Agent Protocol Layer

![Trustless Agents Protocol Layer](./Trustless-Agents-Protocol.png)

上图进一步说明了 **Trustless Agents Protocol Layer** 的核心角色：

* 该层作为 **AI Agent 与区块链服务之间的中间协议层**

* Agent 的每次链上交互（认证、证明、DA、支付）都通过该层进行

* DID、Attestation、DA、x402 支付模块分别通过区块链提供可验证的能力

* Agent 则通过调用该 Layer 实现实际的链上操作，如：

    * 创建与使用 DID

    * 获取/提交 Attestation

    * 发起任务支付（x402）

    * 访问链上数据

    * 与用户、应用、MCP 工具交互

这样，MEMO 构建出的 Agent 不是“中心化 API + LLM 模型”的传统结构，而是：

**一个在链上有身份、能证明行为、能支付费用、能执行智能任务的自主 Agent。**

## x402: Enabling Payment for AI Agents

x402（以及 extensions）是 AI Agent 在区块链上支付的基础。它解决：

* Agent 如何自动为任务支付 gas

* Agent 如何收款

* Agent 如何管理自己的链上余额

* 多任务协作时，费用如何由代理人之间清算

* 跨链费用如何处理

**x402 是 AI Agent 能够“自我执行任务”的关键基础。**

你将在 /x402 目录中看到：

* x402 协议介绍

* 应用场景

* Agent 支付案例

* 与 Pay Facilitator 的关系

## EIP-8004: The Access Layer for Agents

EIP-8004 是 AI Agent 与链上基础设施间的统一接口规范，用于标准化：

* Agent 如何调用 DID 注册/解析

* Agent 如何提交/验证 Attestation

* Agent 如何访问 DA

* Agent 如何执行支付（配合 x402）

* Agent 如何访问 Storage 或 TEE

EIP-8004 为整个 MEMO AIAGENT 构建标准化 SDK 和 Framework，使开发者可以通过统一 API 创建：

* AI Agents

* MCP Tools

* 上层应用（Apps / Launchpad）
