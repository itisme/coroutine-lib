# tenstorrent-coroutine-lib

[English](README.md)

基于协程的协作式调度库，用于 Tenstorrent Blackhole 模拟器。通过轻量级协程管理数百个 RV32IMC 核心在多个 Tensix 节点上的并发执行。

## 概述

Blackhole 芯片有 140 个 Tensix 节点，每节点 7 个核心（共 980 个核心）。本库提供协作式多任务调度，在宿主机上模拟所有核心的并发执行，无需为每个核心分配一个操作系统线程。

主要特性：
- **协作式调度**：核心在自然同步点让出执行（内存访问、NOC 操作、wait 指令）
- **轻量级协程**：极低的上下文切换开销
- **状态机管理**：每个协程跟踪其生命周期（INIT -> RUNNING -> SUSPENDED -> HALTED）
- **分组管理**：节点内的核心分组，支持协调启停

## 文件

| 文件 | 说明 |
|------|------|
| `libcoroutine_lib.so` | 预编译共享库（x86_64 Linux） |
| `coroutine_lib.h` | 公开 API 头文件 |

## API

```c
// 初始化协程调度器
void coroutine_scheduler_init(void);

// 为一个核心创建协程
coroutine_t coroutine_create(coroutine_func_t func, void* arg);

// 让出执行（在协程内部调用）
void coroutine_yield(void);

// 恢复已挂起的协程
void coroutine_resume(coroutine_t co);

// 运行所有协程直到完成或挂起
void coroutine_run_all(void);
```

## 平台

- 预编译平台：x86_64 Linux（glibc 2.35+）
- 已测试：Ubuntu 22.04、Ubuntu 24.04

如果预编译库在你的系统上无法使用，请提 issue。

## 使用

本库由 [tenstorrent-qemu-update](https://github.com/xxx/tenstorrent-qemu-update) 自动使用。使用主项目 [tenstorrent-blackhole-emulator](https://github.com/xxx/tenstorrent-blackhole-emulator) 时无需手动配置。

## 许可

本项目用于研究和教育目的。源代码暂未开放。
