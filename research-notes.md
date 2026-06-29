# Research Notes — Deep Analysis for spacewar-unlocked-ai

This file records the fetch/scrape/analysis performed (using web searches, page browsing, GitHub inspection, and cross-referencing). "Tested in mind" for feasibility on rooted Spacewar with OrangeFox + Evolution base.

## 1. Qualcomm AI on Nothing Phone 1 (Snapdragon 778G+)
- Chip: SM7325-AE, Hexagon 770 NPU rated ~12 TOPS.
- Android support (usable on custom Evolution X):
  - QNN SDK / Qualcomm AI Engine Direct
  - ONNX Runtime QNN EP
  - LiteRT + Qualcomm delegate
  - ExecuTorch Qualcomm backend
  - llama.cpp has Hexagon experimental support (FastRPC + direct HVX in some forks). On 7-series often falls back gracefully to Adreno OpenCL (recent improvements in main llama.cpp).
- On postmarketOS / mainline Linux:
  - NPU: Effectively unavailable. No open mainline driver. Proprietary Android blobs only.
  - Practical path: CPU (highly optimized llama.cpp) + Adreno GPU via freedreno/Turnip + Vulkan/OpenCL.
- Implication for "not locked AI enterprise":
  - Android layer = speed when NPU/QNN works.
  - pmos layer = maximum freedom, full open source stacks, enterprise self-hosting, arbitrary experimentation.
- Recommendation: Support both. Document realistic expectations clearly.

## 2. postmarketOS (pmos) on Spacewar
- Wiki: "nothing-spacewar" — testing category, close to mainline, users running it (Phosh, Plasma).
- Kernel: Mainline or testing branches (shared platform work).
- Dualboot projects exist (examples on GitHub).
- Feasibility: High for the "open AI enterprise" goal. Root everywhere, full Linux toolchain.
- Challenges observed in community: Storage/USB status can be partial in pure mainline; use hybrid or proven configs.
- Action in project: Integrate pmbootstrap configs + recovery-assisted install/dualboot.

## 3. Evolution X Sources
- Device tree repo: Evolution-X-Devices/device_nothing_Spacewar (active for Android 16).
- Contains: device.mk, BoardConfig.mk, rootdir, sepolicy, proprietary-files.txt, camera/sensors/vibrator overlays, evolution.dependencies.
- Perfect base for AOSP-derived daily driver.
- Edit plan: Fork, extend for AI tooling, add de-bloat, performance, pmos hooks.

## 4. OrangeFox Recovery for Spacewar
- Primary device tree: QCerberusQ/OFRP-device_nothing_Spacewar (recent R12 builds, includes FRP Reset addon).
- Structure: fox_Spacewar.mk, twrp variant, recovery/root, Android.mk/bp, vendorsetup.sh, prebuilts.
- Current strengths: Magisk patcher built-in, addons, backup, decryption, fastboot tools.
- Build methods: GitHub releases (sideload), CI builders, manual from OrangeFox source.
- Customization opportunities (high):
  - Add Magisk module manager UI (directly inspired by SkyHawk/SHRP success).
  - Custom addons for debloat + "AI Enterprise Setup".
  - Enhanced recovery terminal + full filesystem/root access.
  - pmos flash helper.
  - Persistent configuration.
- Make it the permanent main recovery image.

## 5. Bootloader Flashing
- Unlock: `fastboot flashing unlock` (device confirmation with volume keys).
- Critical (for abl, xbl, bootloader parts): `fastboot flashing unlock_critical`.
- Flashing: Stock firmware flash_all scripts are the reference. Selective `fastboot flash <partition>`.
- Recovery installation: Sideload in OrangeFox or fastboot flash boot (test first).
- Critical safety: Full backup before any critical partition work. Never lock with modified critical parts.

## 6. Other Recoveries — Feature Harvest
- TWRP: Baseline reliability and tools.
- SkyHawk (SHRP): Best-in-class in-recovery Magisk module management (install, toggle, delete). Theme support.
- PitchBlack: Modern UI, themes, decryption improvements, flexibility.
- Strategy: Keep OrangeFox base (good Magisk integration already) and layer on the best ideas:
  - Module manager experience from SHRP.
  - UI/UX and OTA ideas from PBRP.
  - Scripting and reliability from TWRP.
- Practical result in custom build: User can do almost everything from recovery without booting the main OS first.

## 7. AI Services Execution Paths
**In recovery / early post-flash**:
- Scripts can prepare Termux + Magisk modules.
- Pre-download small models or setup instructions.
- Mount full data for aggressive debloat before first boot.

**Android layer (rooted Evolution)**:
- Use QNN where available.
- llama.cpp with OpenCL fallback.
- Full Termux/proot Linux userland.

**pmos layer**:
- Build llama.cpp from source with best available flags.
- Run production-grade open stacks (Ollama, anything-llm, self-hosted inference servers).
- Containers, Jupyter, full dev environment.

**"Enterprise" angle**: No forced Google account, FOSS models preferred, run your own services, full control.

## 8. Repos to Clone/Fork/Edit
1. https://github.com/Evolution-X-Devices/device_nothing_Spacewar
2. https://github.com/QCerberusQ/OFRP-device_nothing_Spacewar
3. postmarketOS nothing-spacewar device package + dualboot examples
4. nothing-phone-1-tools (flash scripts)
5. https://github.com/ggerganov/llama.cpp (backends)
6. Magisk + relevant modules
7. Stock firmware references (SourceForge Nothing Phone 1)

Each will be forked into the project with clean, documented, purpose-built patches.

## Mind-Tested Feasibility Notes
- Custom OrangeFox as persistent main recovery: Proven pattern on Spacewar.
- Heavy debloat + full root in recovery: Standard on rooted + custom recovery devices.
- Dual Android + pmos: Existing community projects prove concept.
- NPU on pmos: Not worth fighting; document CPU/GPU path clearly.
- GitHub orchestration repo: Very achievable and high value for reproducibility.

This research was performed via targeted searches, direct page opens of wikis/GitHub/device trees, and cross-referencing community reports (2025-2026 timeframe).

Next: Cloning commands for the user + first concrete patches/scripts.