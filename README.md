# devcontainerd

**devcontainerd** is a Rust-first container management platform designed to provide a complete container lifecycle and management layer without depending on the traditional Go-based containerd implementation.

Built with **Rust and C**, devcontainerd is designed for low-level systems work, container lifecycle management, image and content handling, snapshots, tasks, APIs, events, networking, and runtime integration.

> **devcontainerd — Container Management, Rebuilt in Rust.**

---

## Overview

devcontainerd is a standalone container management daemon and platform.

Its architecture separates **container management** from **container execution**:

```text
                    devcontainerd
            Container Management Platform
                         │
                         │
                        Runex
             Container Execution Runtime
                         │
                         │
                   Linux Kernel
```

Where:

* **devcontainerd** manages containers, tasks, images, snapshots, APIs, events, and related platform functionality.
* **Runex** is the execution runtime responsible for creating and running isolated containers.
* The **Linux kernel** provides the underlying kernel primitives required for namespaces, cgroups, filesystem isolation, networking, processes, and other system-level features.

devcontainerd does **not** require the upstream Go `containerd` implementation to operate.

---

## Project Goals

devcontainerd is being developed with several core goals:

* Rust-first systems architecture
* Native low-level control
* Complete container lifecycle management
* Strong separation between management and execution
* Native integration with Runex
* Image and content management
* Snapshot and storage management
* Task and process management
* Networking integration
* API-driven architecture
* Event and state management
* Extensible runtime architecture
* Linux-first implementation with room for broader platform support

---

## Architecture

The high-level architecture is designed around clear subsystem boundaries:

```text
                           Clients
                              │
                              ▼
                     devcontainerd API
                              │
                 ┌────────────┼────────────┐
                 │            │            │
                 ▼            ▼            ▼
            Content Store  Containers     Tasks
                 │            │            │
                 ▼            ▼            ▼
              Images      Lifecycle     Processes
                 │
                 ▼
             Snapshots
                 │
                 ▼
          Storage / Filesystem
                              │
                              ▼
                       Runtime Interface
                              │
                              ▼
                            Runex
                              │
                              ▼
                        Linux Kernel
```

The architecture is intended to keep management responsibilities inside devcontainerd while execution responsibilities remain inside Runex.

---

## devcontainerd vs Runex

devcontainerd and Runex are separate projects with different responsibilities.

| Component         | Role                                           |
| ----------------- | ---------------------------------------------- |
| **devcontainerd** | Container management platform / daemon         |
| **Runex**         | Container execution runtime                    |
| **Linux Kernel**  | Kernel-level isolation and resource primitives |

### devcontainerd

devcontainerd focuses on:

* container lifecycle
* task management
* image management
* content storage
* snapshots
* APIs
* events
* state management
* networking integration
* runtime integration

### Runex

Runex focuses on:

* container execution
* process creation
* namespaces
* mounts
* root filesystem setup
* cgroups
* process isolation
* resource controls
* runtime-level container operations

This separation allows each component to remain focused on its role.

---

## Implementation

devcontainerd is implemented using:

```text
Rust + C
```

Rust is used for the primary systems architecture, concurrency, services, management layers, and core platform logic.

C is used where low-level native system integration and ABI/FFI boundaries are appropriate.

The project is designed around native systems programming rather than a language-specific wrapper around another container platform.

---

## Codebase

The current devcontainerd codebase contains approximately:

```text
~600,000 lines of code
```

across Rust and C.

The codebase is organized as a large systems project with multiple subsystems and low-level integrations.

LOC is treated as a development measurement rather than a claim of feature completeness. Functional correctness is established through compilation, testing, integration testing, and real-world validation.

---

## Runtime Model

devcontainerd is designed to work with Runex as its execution runtime:

```text
Client
  │
  ▼
devcontainerd
  │
  ├── container lifecycle
  ├── image/content management
  ├── snapshots
  ├── task management
  ├── events
  ├── networking
  └── runtime interface
           │
           ▼
         Runex
           │
           ▼
     Linux Kernel
```

This allows devcontainerd to remain a management platform while Runex remains focused on execution.

---

## Core Components

The platform is designed around several major areas:

### API

Provides the control interface used by clients and higher-level systems.

### Content Store

Responsible for storing and managing image-related content and other persistent content objects.

### Containers

Tracks container configuration and lifecycle state.

### Tasks

Manages active container tasks and associated processes.

### Snapshots

Provides filesystem snapshot and storage functionality for container root filesystems.

### Events

Provides event/state propagation for container and task operations.

### Networking

Provides networking integration for container workloads.

### Runtime Integration

Connects devcontainerd's lifecycle management to the execution runtime, including Runex.

---

## Command-Line Tools

The project is designed to provide native command-line tooling alongside the daemon.

Planned/current binaries include:

```text
devcontainerd
devconcli
devcontainerd-stress
devcontainerd-shim-runex-v1
```

### `devcontainerd`

Main daemon and container management service.

### `devconcli`

Native CLI for interacting with devcontainerd.

### `devcontainerd-stress`

Stress-testing and workload-validation utility.

### `devcontainerd-shim-runex-v1`

Runtime integration layer for Runex.

---

## Build

The project uses the Rust toolchain together with the required native C build components.

A typical development flow is:

```bash
cargo check
cargo test
cargo build
```

For release validation:

```bash
cargo test --all
cargo build --release
```

Additional native build requirements may be required depending on the platform and enabled subsystems.

---

## Testing Strategy

devcontainerd is intended to be validated in multiple stages:

```text
Source Validation
       │
       ▼
cargo check
       │
       ▼
cargo test
       │
       ▼
cargo build
       │
       ▼
Integration Tests
       │
       ▼
Runtime Tests
       │
       ▼
Real-World Container Tests
```

Testing is especially important around:

* process lifecycle
* filesystem operations
* namespace isolation
* cgroups
* task management
* snapshots
* networking
* image/content handling
* runtime integration
* concurrent operations

---

## Design Principles

### Rust First

Rust is the primary implementation language for memory safety, concurrency, and systems-level control.

### Native Systems Integration

The platform is designed to interact directly with operating-system primitives rather than hiding the entire system behind another container daemon.

### Separation of Responsibilities

Management and execution are separate layers:

```text
devcontainerd → Management
Runex         → Execution
Kernel        → Isolation primitives
```

### Extensibility

The architecture is intended to support additional runtimes, storage components, networking implementations, and integrations over time.

---

## Relationship to Existing Container Platforms

devcontainerd is inspired by the role played by container-management daemons such as containerd, but it is being developed as its **own implementation and architecture**.

The goal is not to be a thin wrapper around the upstream Go implementation.

Instead:

```text
Traditional model

Container Platform
        │
        ▼
Existing Runtime
        │
        ▼
Kernel
```

devcontainerd's model is:

```text
devcontainerd
        │
        ▼
      Runex
        │
        ▼
      Kernel
```

with the container management responsibilities implemented directly within devcontainerd.

---

## Project Status

devcontainerd is an active systems-development project.

Current development is focused on building and validating the core container management stack, its native Rust/C implementation, runtime integration, and real-world behavior.

Large source volume alone is not considered proof of completeness. Feature maturity will be determined through:

* successful builds
* automated tests
* integration tests
* runtime validation
* real container workloads
* stability testing
* interoperability testing

---

## Ecosystem

devcontainerd is part of a broader systems ecosystem:

```text
                 ┌─────────────────┐
                 │  Higher-Level    │
                 │  Clients / Apps  │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │  devcontainerd  │
                 │ Management      │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │      Runex      │
                 │ Execution       │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │  Linux Kernel   │
                 └─────────────────┘
```

This separation provides a foundation for building larger container and infrastructure systems around the platform.

---

## Repository Structure

The repository contains the implementation of the devcontainerd platform and its supporting components.

A typical top-level layout may include:

```text
devcontainerd/
├── src/
├── c/
├── tests/
├── scripts/
├── docs/
├── Cargo.toml
└── README.md
```

The exact structure may evolve as development continues.

---

## Requirements

Development generally requires:

* Linux
* Rust toolchain
* Cargo
* C compiler/toolchain
* standard Linux development libraries
* required kernel features for container workloads

The exact requirements depend on the enabled components and target environment.

---

## Development Philosophy

devcontainerd is being built as a systems platform rather than as a simple command-line wrapper.

The project aims to provide:

```text
Native
        +
Safe
        +
Concurrent
        +
Low-level
        +
Extensible
        =
Rust-based Container Platform
```

---

## License

See the repository license file for the current licensing terms.

---

## Project Website

**https://devcontainerd.org**

---

## Summary

devcontainerd is a **Rust + C container management platform** focused on providing a native container lifecycle and management stack.

Its architecture separates management from execution:

```text
devcontainerd
      │
      ▼
    Runex
      │
      ▼
Linux Kernel
```

With approximately **600K lines of Rust and C code**, the project is being developed as a large-scale systems platform with its own architecture, native components, testing workflow, and runtime integration model.

**devcontainerd — Container Management, Rebuilt in Rust.**
