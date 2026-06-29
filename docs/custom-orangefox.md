# Custom OrangeFox for Spacewar — Detailed Enhancements

This document defines the "perfect special" changes we will make to the OrangeFox device tree and recovery to turn it into the ultimate control plane for an unlocked AI enterprise setup.

Base: QCerberusQ/OFRP-device_nothing_Spacewar (and upstream OrangeFox)

## Goals for Custom OrangeFox
- Persistent main recovery (re-flash on every ROM update)
- Full root directory access out of the box
- Built-in Magisk + easy module management (inspired by SkyHawk/SHRP)
- One-tap "AI Enterprise Unlock" workflows
- Debloat automation (remove Google/Nothing locks)
- pmos / alternative OS flashing helpers
- Advanced terminal with chroot and AI tooling prep
- Safety first (backups, confirmations)
- Persistent configuration for your preferred unlocked state

## Specific Enhancements & Implementation Plan

### 1. Magisk Module Manager in Recovery (High Priority)
**Inspired by:** SkyHawk (SHRP)

**What to add:**
- Menu: "Magisk Modules"
- List installed modules from /data/adb/modules
- Actions: Enable/Disable, Remove, Info
- "Install from zip" directly in recovery
- "Unroot" option

**Files to edit/create:**
- recovery/root/system/bin/magisk_module_manager (shell script or binary wrapper)
- Add to recovery UI (fox_Spacewar.mk or custom menu definitions)
- Use existing OrangeFox Magisk integration as base

**Sample starter script (to be placed in recovery/root/system/bin/):**

```sh
#!/system/bin/sh
# magisk_module_manager.sh - Custom for spacewar-unlocked-ai

MODULE_DIR="/data/adb/modules"
MAGISK="/sbin/magisk"   # or correct path in recovery

list_modules() {
    echo "=== Installed Magisk Modules ==="
    for mod in "$MODULE_DIR"/*; do
        if [ -d "$mod" ]; then
            name=$(basename "$mod")
            if [ -f "$mod/disable" ]; then
                status="DISABLED"
            else
                status="ENABLED"
            fi
            echo "$name - $status"
        fi
    done
}

toggle_module() {
    mod="$1"
    if [ -f "$MODULE_DIR/$mod/disable" ]; then
        rm "$MODULE_DIR/$mod/disable"
        echo "Enabled $mod"
    else
        touch "$MODULE_DIR/$mod/disable"
        echo "Disabled $mod"
    fi
}

case "$1" in
    list) list_modules ;;
    toggle) toggle_module "$2" ;;
    *) echo "Usage: $0 list | toggle <modulename>" ;;
esac
```

Integrate this into OrangeFox menus via the device tree.

### 2. AI Enterprise Setup Addon (Core Feature)
**New addon / menu item: "AI Enterprise Setup"**

Actions it should perform:
- Detect if Magisk is installed, install if needed (using built-in patcher)
- Install recommended modules for performance/root (e.g. LSPosed if desired, FDE.AI or similar, Zygisk modules)
- Set up Termux + root access
- On Android side: Prepare llama.cpp build environment or install prebuilt
- On pmos side: Prepare chroot or dualboot
- Download small starter models (e.g. TinyLlama or Phi-3 mini quantized)
- Configure for best available backend (note NPU vs OpenCL vs CPU)

**Implementation:**
- New folder in device tree: `recovery/root/ai_setup/`
- Script: `ai_setup.sh`
- Add to OrangeFox addons or advanced menu via `fox_Spacewar.mk` or `vendorsetup.sh`

**High-level ai_setup.sh skeleton:**

```sh
#!/system/bin/sh
echo "=== spacewar-unlocked-ai Enterprise Setup ==="
echo "This will debloat + prepare AI tooling."

# Example: aggressive but safe debloat
# (run only after user confirmation)
pm uninstall -k --user 0 com.google.android.gms 2>/dev/null || true
# ... more targeted debloat commands for Nothing/Google components

echo "Installing base AI tools..."
# Termux + proot-distro or direct llama.cpp setup commands
# For pmos path: detect /pmos or offer dualboot flash

echo "Setup complete. Reboot and continue in Termux or pmos."
```

### 3. Debloat & Root Unlocking Scripts
- One menu: "Full Unlocked Debloat"
- Removes bloat while preserving hardware features (camera, glyphs, etc.)
- Runs with full root in recovery (mount data/system/vendor as needed)
- Logs everything to /sdcard/ai-unlock.log

### 4. pmos / Alternative OS Support
- Menu option: "Flash pmos Image"
- Helper to detect and flash pmos boot + rootfs (using existing dualboot knowledge)
- Chroot setup script for running Linux AI tools from Android side

### 5. Enhanced Terminal & Root Access
- Ensure busybox is full-featured
- Add `su` wrapper that works reliably in recovery
- Pre-mount all partitions (data, system, vendor, persist) for easy exploration
- Add "Open Root Shell" that drops user into a powerful environment

### 6. Persistent Config
- File: /sdcard/.spacewar-unlocked-ai/config
- Stores user choices (which AI tools, debloat level, preferred recovery behavior)
- Scripts read this on boot into recovery

### 7. Safety & Polish
- Always require backup confirmation before destructive actions
- "Restore Stock" quick path using known good images
- Versioning of the custom recovery (tag builds with "ai-unlocked-vX")

## How to Build the Custom Version
1. Fork `QCerberusQ/OFRP-device_nothing_Spacewar`
2. Apply patches from this repo's `patches/orangefox/` (we will create them)
3. Add the new scripts to `recovery/root/`
4. Update `fox_Spacewar.mk` and menus
5. Build using OrangeFox builder or GitHub Actions (we'll add workflow)
6. Test on device using sideload

## Next Implementation Steps (for this project)
- [ ] Create exact patch files for the device tree
- [ ] Write complete production versions of the scripts above
- [ ] Add sample addon zip structure
- [ ] Document exact menu additions
- [ ] Create build workflow

This will make OrangeFox far more powerful than stock TWRP/OrangeFox for your use case: true enterprise control + AI dev acceleration.

See also: README.md and research-notes.md for context.