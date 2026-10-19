```
╔═══════════════════════════════════════════════════════════╗
║                                                           ║
║       HyprGlass Studio — Liquid Glass for Hyprland        ║
║                                                           ║
╚═══════════════════════════════════════════════════════════╝
```
# Hyprglass Studio - Native Lua Support (Hyprland 0.55+)

This fork adds full compatibility for Hyprland's native Lua configuration parser. 
The original script was designed for the legacy `.conf` (hyprlang) format, which is deprecated in Hyprland 0.55+.

## 🛠 Changes made

### Backend (`src/server.py`)
- **Config Path:** Changed `CONFIG_PATH` to write to `~/.config/hypr/HyprglassStudio.lua` (instead of the legacy `.conf`).
- **Validation:** Replaced the old shell-based `.conf` validator with a native Lua syntax checker using `luac`.
- **Live Reload:** Updated `reload_hyprland()` to use `hyprctl eval 'dofile(...)'` instead of just `hyprctl reload`, allowing Studio to apply changes live without restarting Hyprland.

### Frontend (`src/app.js`, `src/schema.js`, `src/bundle.js`)
- **Format Selection Bug:** Fixed a bug in `liveConfigText()` (`app.js`) where the UI was always sending `.conf` data to the server, even when "Lua" was selected.
- **Default Format:** Changed the default `output_format` from `'conf'` to `'lua'`.
- **Lua Syntax:** Fixed `formatLua()` in `schema.js`:
  - Replaced non-existent `require("hyprglass")` with the correct native API `hl.plugin.hyprglass`.
  - Removed quotes around `tint_color` (it now exports a valid numeric literal `0x...` as expected by the plugin).

## 📝 User Setup Note
For this to work, the user's main `Hyprglass.lua` must load the generated Studio file. Add this line inside the `if hl.plugin.hyprglass then` block, after `hg.config({...})`:

```lua
pcall(dofile, os.getenv("HOME") .. "/.config/hypr/HyprglassStudio.lua")
