# tenstorrent-coroutine-lib

[中文说明](README_zh.md)

Coroutine-based cooperative scheduling library for the Tenstorrent Blackhole emulator. Manages concurrent execution of hundreds of RV32IMC cores across multiple Tensix nodes using lightweight coroutines.

## Overview

The Blackhole chip has 140 Tensix nodes, each with 7 cores (980 cores total). This library provides cooperative multitasking to simulate all cores concurrently on the host machine without requiring one OS thread per core.

Key features:
- **Cooperative scheduling**: Cores yield at natural synchronization points (memory access, NOC operations, wait instructions)
- **Lightweight coroutines**: Minimal context switch overhead
- **State machine**: Each coroutine tracks its lifecycle (INIT -> RUNNING -> SUSPENDED -> HALTED)
- **Group management**: Cores within a node are grouped for coordinated start/stop

## Files

| File | Description |
|------|-------------|
| `libcoroutine_lib.so` | Pre-built shared library (x86_64 Linux) |
| `coroutine_lib.h` | Public API header |

## API

```c
// Initialize the coroutine scheduler
void coroutine_scheduler_init(void);

// Create a coroutine for a core
coroutine_t coroutine_create(coroutine_func_t func, void* arg);

// Yield execution (called from within a coroutine)
void coroutine_yield(void);

// Resume a suspended coroutine
void coroutine_resume(coroutine_t co);

// Run all coroutines until completion or suspension
void coroutine_run_all(void);
```

## Platform

- Pre-built for: x86_64 Linux (glibc 2.35+)
- Tested on: Ubuntu 22.04, Ubuntu 24.04

If the pre-built library doesn't work on your system, please open an issue.

## Usage

This library is automatically used by [tenstorrent-qemu-update](https://github.com/xxx/tenstorrent-qemu-update). No manual setup is required when using the main project [tenstorrent-blackhole-emulator](https://github.com/xxx/tenstorrent-blackhole-emulator).

## License

This project is for research and educational purposes. Source code is not currently available.
