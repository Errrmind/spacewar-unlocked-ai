# spacewar-unlocked-ai

**Maximally Unlocked AI Enterprise Open Source OS for Nothing Phone (1) "Spacewar"**

Evolution X (Android base) + postmarketOS (true Linux freedom) + heavily customized OrangeFox Recovery as the control plane + full bootloader mastery + practical AI development services that go far beyond Google/Android restrictions.

This project is the result of deep research + co-building. It collects, analyzes, forks, and edits key open sources into one cohesive, reproducible system.

> **Core Philosophy**: Recovery is god-mode. pmos is the unlocked AI lab. Evolution X is the capable daily driver. Every flash step is deliberate to maximize openness, root access, debloating, and AI tooling.

## Quick Status
- [x] Research complete
- [x] Key repos analyzed & initial forks identified
- [x] Custom OrangeFox enhancements designed
- [ ] Flashing orchestration scripts (before/while/after)
- [ ] AI setup tooling for both layers
- [ ] GitHub Actions for builds
- [ ] Full step-by-step + safety

## Research Summary (Fetched, Read, Analyzed, Memorized)

### Qualcomm AI Hardware (Nothing Phone 1 — Snapdragon 778G+ / SM7325)
- **Hexagon 770 NPU**: ~12 TOPS. Purpose-built for on-device inference (convolutions, transformers, etc.).
- **Android path (Evolution X layer)**: Strong support.
  - Qualcomm AI Engine Direct / QNN SDK
  - ONNX Runtime with QNN Execution Provider
  - LiteRT (TFLite) with Qualcomm delegate
  - ExecuTorch backend
  - NNAPI
- **llama.cpp status**: Experimental Hexagon backends exist (FastRPC + HVX intrinsics, contributions from Qualcomm). Performance varies; often falls back to improved Adreno OpenCL GPU backend on 7-series. Newer chips (X Elite) have better direct support.
- **Beyond Android/Google (pmos / mainline Linux)**: NPU is **not realistically accessible** (proprietary blobs, no open mainline driver). 
  - Realistic: High-quality CPU inference (optimized llama.cpp) + Adreno GPU via open-source drivers (freedreno / Turnip + Vulkan/OpenCL).
  - **Huge advantage**: Full Linux AI ecosystem (Ollama, vLLM, PyTorch, Jupyter, Docker, self-hosted enterprise stacks) with zero Google/Play Services lock-in.
- **Enterprise takeaway**: Use Android side for speed when NPU works. Use pmos side for true open development, arbitrary models, training, serving, and "enterprise" self-hosted AI services.

### postmarketOS on Spacewar
- Wiki entry exists (testing category, "close to mainline").
- Kernel efforts on shared platform code.
- Users successfully running Phosh / Plasma Mobile.
- Dualboot projects available.
- Perfect match for "not locked AI enterprise open source OS".

### Evolution X Sources
- Device tree: Evolution-X-Devices/device_nothing_Spacewar (Android 16 builds active).
- Forkable with device.mk, BoardConfig, rootdir, sepolicy, proprietary-files, etc.
- Plan: Fork → add AI packages/scripts, performance kernel tweaks, de-bloat, dualboot/pmos hooks, post-install AI tooling.

### OrangeFox Recovery (Spacewar)
- Device tree: QCerberusQ/OFRP-device_nothing_Spacewar (and forks). Includes fox_Spacewar.mk, recovery/root, TWRP variant.
- Features today: Built-in Magisk patcher, addons (including dedicated FRP reset), backup/restore, decryption, fastboot tools.
- Buildable via CI / builders.
- **Our enhancements** (fork + edit "perfectly"):
  - Built-in Magisk module manager (inspired by SkyHawk/SHRP).
  - "AI Enterprise Setup" addon (one-tap install of inference tools + models).
  - Advanced terminal with full root dir access + chroot/pmos prep.
  - Automated debloat scripts.
  - pmos image detection + flash helper.
  - Persistent custom configuration.
  - Safety prompts + backup enforcement.
  - Make it **the** main recovery image that you re-flash on every update.

### Bootloader Control
- Easy: `fastboot flashing unlock`
- Critical partitions (abl, xbl, etc.): `fastboot flashing unlock_critical`
- Selective or full: Use stock firmware + flash_all scripts (nothing-phone-1-tools repos, SourceForge dumps).
- Recovery is typically installed to boot or via fastboot boot (test) then persist.
- Always keep full stock backup.

### Other Recoveries — What We Steal & Improve
- **TWRP**: Stability, broad tooling.
- **SkyHawk (SHRP)**: Native Magisk module management inside recovery (install/disable/delete without booting Android).
- **PitchBlack (PBRP)**: Modern UI, themes, OTA, decryption.
- Applied to our OrangeFox fork: module UI, better terminal/root, debloat automation, alternative-OS flashing support, custom AI menus.

### AI Development Services (Unlocked)
**Android/Evolution layer (rooted + Magisk)**:
- QNN/ONNX for NPU where available.
- llama.cpp (CPU + OpenCL Adreno).
- Termux/proot for many Linux tools.

**pmos / pure Linux layer**:
- Build and run anything: llama.cpp (best backends), Ollama, local LLMs, full Python ML, Docker, self-hosted APIs, vector DBs, LangChain, etc.
- GPU acceleration via open Adreno drivers.

**During flashing**:
- Pre-stage tools and small models inside recovery or post-flash scripts.
- Scripts run in recovery (before first system boot) to maximize openness.

## Recommended Architecture
- Daily driver: Customized Evolution X (good hardware support + NPU when useful).
- Real AI power & freedom: postmarketOS (dualboot or chroot/side).
- Control plane: Custom OrangeFox (debloat, Magisk, AI prep, pmos flashing, full root access).
- Bootloader: Documented unlock + selective critical control + easy stock restore.

## Full Step-by-Step (Before / During / After)

See `docs/flashing-guide.md` (to be added) and scripts in `/flashing/`.

High-level phases:
1. **Before**: Enable OEM unlock, fastboot unlock + critical unlock, full backups (OrangeFox + stock), clone this repo + dependencies, prepare custom recovery build.
2. **Flash Bootloader/Recovery (if needed)**: Use unlock_critical only when necessary. Install custom OrangeFox as the persistent main recovery.
3. **During OS flash**: Format Data in custom recovery, flash Evolution X (handle super partition if needed), flash pmos side or prepare chroot.
4. **After (in custom OrangeFox — this is where the magic happens)**:
   - Debloat automation
   - Magisk + curated modules
   - AI Enterprise Setup (llama.cpp / ONNX + models on Android side; full Linux stack on pmos side)
   - Root dir access tools
   - Verification

Re-flash the custom OrangeFox after any major ROM change.

## Repos We Analyze + Edit (Scraped & Forked)
- Evolution-X-Devices/device_nothing_Spacewar
- QCerberusQ/OFRP-device_nothing_Spacewar + OrangeFox upstream
- postmarketOS nothing-spacewar configs + dualboot projects
- nothing-phone-1-tools and stock flash scripts
- llama.cpp (relevant backends)
- Magisk + key modules
- All edited with safety, documentation, AI focus, and "perfect" integration.

See `patches/` and subdirectories for targeted diffs.

## Getting Started (Current)
1. Clone this repo.
2. Follow the research summary above.
3. Run the flashing preparation scripts (TBD).
4. Build or download custom OrangeFox.
5. Execute the phased flashing.

## Safety First
- Full backups before every major step.
- Stock firmware ready for restore.
- Never lock bootloader with modified partitions.
- Test AI inference after each phase.

## Algorithmic Art / Visualization
This project can incorporate the algorithmic-art skill for conceptual visuals (flow of unlocked capabilities, generative representations of NPU/CPU/GPU collaboration, "enterprise emergence").

## Contributing / Philosophy
Everything is designed to be auditable, forkable, and improvable. The goal is the least locked state the hardware realistically allows.

---

**Status**: Plan approved and execution started. This README is the living document.

Next artifacts will include:
- Detailed flashing scripts
- Custom OrangeFox addon descriptions + sample code
- AI setup guides for both layers
- patches/ directory with initial edits

Run with care. Have fun unlocking.