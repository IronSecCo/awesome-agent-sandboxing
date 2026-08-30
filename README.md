# Awesome Agent Sandboxing [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of sandboxing and secure code-execution tools for AI agents.

Autonomous AI agents run code, call tools, and act on your data. Prompt injection, a
poisoned tool result, or a compromised dependency can turn a helpful agent into a hostile
one. **Sandboxing is the boundary that decides what happens next.** This list collects the
runtimes, isolation technologies, and platforms that people use to contain untrusted or
agent-generated code, so you can compare the real options honestly.

It is maintained by the [IronClaw](https://github.com/IronSecCo/ironclaw) project. IronClaw
is on the list among its peers; entries are curated on merit, not on affiliation.
Contributions welcome, see [Contributing](#contributing).

## Contents

- [Self-hosted agent runtimes](#self-hosted-agent-runtimes)
- [Hosted agent sandbox platforms](#hosted-agent-sandbox-platforms)
- [Kernel-level and container isolation](#kernel-level-and-container-isolation)
- [MicroVM isolation](#microvm-isolation)
- [WebAssembly sandboxing](#webassembly-sandboxing)
- [Language and runtime sandboxes](#language-and-runtime-sandboxes)
- [Hardening building blocks](#hardening-building-blocks)
- [Benchmarks and learning](#benchmarks-and-learning)
- [Contributing](#contributing)

## Self-hosted agent runtimes

Run agents on infrastructure you control, with the sandbox as part of the runtime.

- [IronClaw](https://github.com/IronSecCo/ironclaw) - Security-first, self-hosted runtime for autonomous AI agents. One gVisor-backed sandbox per conversation with `network=none`, a read-only rootfs, a host-side proxy so provider keys never enter the box, and a human-approval gateway. AGPLv3 + commercial. `#agent-sandboxing`
- [OpenHands](https://github.com/All-Hands-AI/OpenHands) - Autonomous software-development agent that executes actions inside a per-session Docker sandbox runtime. Open source (MIT).
- [SandBase Harness](https://github.com/sandbaseai/sandbase-harness) - Apache-2.0 local-first TypeScript runtime with MCP/tool governance, persistent sessions, approvals, audit/replay, and Local, Docker, Kubernetes, and self-hosted worker sandbox backends; local path isolation and stronger backend properties must be evaluated separately.
- [gVisor + Docker sandbox recipes](https://gvisor.dev/docs/user_guide/quick_start/docker/) - Reference for wiring the `runsc` runtime under Docker to sandbox untrusted workloads, a common building block for DIY agent runtimes.
- [Open Interpreter](https://github.com/OpenInterpreter/open-interpreter) - Runs LLM-generated code locally; offers a `--os` / offline mode and documents containerized execution for isolating that code. Open source (AGPLv3).

## Hosted agent sandbox platforms

Managed cloud sandboxes: you call an API, the vendor runs the isolated environment.

- [E2B](https://github.com/e2b-dev/E2B) - Open-source SDK and cloud for spinning up isolated cloud sandboxes to run AI-generated code. Firecracker-based per their docs. (Apache-2.0 SDK, hosted platform.)
- [Modal](https://modal.com/docs/guide/sandbox) - Serverless cloud with Sandboxes for running untrusted and agent code in gVisor-isolated containers, billed per second of compute.
- [Daytona](https://github.com/daytonaio/daytona) - Fast, ephemeral sandboxes for executing AI-generated code, with a focus on sub-second spin-up. Open source (AGPLv3) with a hosted offering.
- [Runloop](https://www.runloop.ai/) - Managed "Devboxes": cloud sandboxes aimed at coding agents and long-running agent tasks.
- [Northflank](https://northflank.com/) - Self-serviceable platform that can run isolated containerized workloads and jobs, used to host agent execution environments.

## Kernel-level and container isolation

The isolation technologies that back most of the above.

- [gVisor](https://github.com/google/gvisor) - Application kernel written in Go that intercepts a sandboxed process's syscalls and reimplements the Linux ABI in user space, shrinking the host kernel attack surface. Ships the `runsc` OCI runtime. (Apache-2.0.)
- [Kata Containers](https://github.com/kata-containers/kata-containers) - OCI-compatible runtime that wraps each container in a lightweight VM for hardware-backed isolation while keeping the container UX. (Apache-2.0.)
- [Sysbox](https://github.com/nestybox/sysbox) - Container runtime that enhances isolation (user-namespace by default, no privileged mode) and lets containers run Docker/systemd inside, a rootless-friendly hardening layer. (Apache-2.0.)
- [Nabla containers](https://github.com/nabla-containers/runnc) - Unikernel-style containers that drastically reduce the host syscall surface a container can reach. (Research / ISC.)
- [runc rootless](https://github.com/opencontainers/runc) - The reference OCI runtime; rootless mode plus user namespaces is the baseline hardening step for shared-kernel containers. (Apache-2.0.)

## MicroVM isolation

Hardware-virtualization boundaries that are still fast to boot.

- [Firecracker](https://github.com/firecracker-microvm/firecracker) - Minimal VMM from AWS that boots microVMs in ~125 ms with a tiny device model, built for multi-tenant serverless and sandbox isolation. (Apache-2.0.)
- [Cloud Hypervisor](https://github.com/cloud-hypervisor/cloud-hypervisor) - Rust VMM focused on modern cloud workloads, another strong microVM foundation for sandboxes. (Apache-2.0 / BSD.)
- [QEMU microvm](https://www.qemu.org/docs/master/system/i386/microvm.html) - QEMU's stripped-down `microvm` machine type for fast-booting, minimal-surface VMs.

## WebAssembly sandboxing

Capability-based, deny-by-default execution for untrusted code.

- [Wasmtime](https://github.com/bytecodealliance/wasmtime) - Fast, secure WebAssembly runtime with WASI; no host access unless explicitly granted. (Apache-2.0.)
- [WasmEdge](https://github.com/WasmEdge/WasmEdge) - Lightweight, high-performance WebAssembly runtime for edge and serverless, with a growing story for running AI workloads. (Apache-2.0.)
- [Wasmer](https://github.com/wasmerio/wasmer) - Universal WebAssembly runtime and registry for running sandboxed code across languages and platforms. (MIT.)
- [Extism](https://github.com/extism/extism) - Framework for making applications extensible with sandboxed WebAssembly plugins, useful for constraining agent tool code. (BSD-3.)

## Language and runtime sandboxes

Isolation enforced at the language or interpreter level.

- [Deno](https://github.com/denoland/deno) - JavaScript/TypeScript runtime that is secure by default: no file, network, or environment access unless granted by explicit permission flags. (MIT.)
- [Pyodide](https://github.com/pyodide/pyodide) - CPython compiled to WebAssembly, so Python runs inside the browser/WASM sandbox rather than on the host, used to execute untrusted Python. (MPL-2.0.)
- [RestrictedPython](https://github.com/zopefoundation/RestrictedPython) - Defines a restricted subset of Python for executing untrusted source, a language-level (not OS-level) guard. (ZPL.)
- [nsjail](https://github.com/google/nsjail) - Lightweight process-isolation tool using Linux namespaces, seccomp-bpf, and cgroups to jail untrusted programs. (Apache-2.0.)

## Hardening building blocks

Primitives you compose into a sandbox regardless of runtime.

- [seccomp](https://man7.org/linux/man-pages/man2/seccomp.2.html) - Linux syscall filtering; a seccomp-bpf allowlist shrinks the kernel surface a sandboxed process can reach.
- [AppArmor](https://apparmor.net/) - Mandatory access control that confines a program to a defined set of files and capabilities.
- [bubblewrap](https://github.com/containers/bubblewrap) - Unprivileged sandboxing tool (the engine behind Flatpak) that builds namespace-based sandboxes for untrusted apps. (LGPL-2.0.)
- [Landlock](https://landlock.io/) - Linux LSM that lets an unprivileged process restrict its own filesystem access, useful for defense in depth inside a sandbox.

## Benchmarks and learning

- [IronClaw: Breaking our own sandbox](https://ironsecco.github.io/ironclaw/breaking-our-own-sandbox/) - A reproducible escape-attempt suite run against raw Docker, hardened runc, and gVisor, with honest scoring.
- [gVisor: Security Model](https://gvisor.dev/docs/architecture_guide/security/) - How a user-space kernel changes the container escape calculus.
- [Firecracker design](https://github.com/firecracker-microvm/firecracker/blob/main/docs/design.md) - Why a minimal device model matters for multi-tenant isolation.
- [OWASP: LLM and agentic security](https://genai.owasp.org/) - Threat framing for agents that act, including the injection paths a sandbox has to contain.

## Contributing

Contributions are welcome. Please keep the list honest and useful:

1. One entry per line: `[Name](link) - one-sentence, factual description.`
2. Describe what a project **is**, not marketing claims. Note the license or hosting model where relevant.
3. Put it in the category that best matches how it isolates code.
4. Do not assert third-party capabilities you cannot verify; link to the project's own docs.

Open a pull request or an issue. See [CONTRIBUTING.md](CONTRIBUTING.md) for the short version.

## License

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](LICENSE)

To the extent possible under law, the contributors have waived all copyright and related
rights to this work (CC0-1.0).
