# Assets Directory

## `hud-boot-sequence.gif` — Hero Cinematic Loop

**Specs:**
- **Resolution**: 1200 × 400 px (3:1 aspect)
- **Duration**: 4–5 seconds, seamless loop
- **Palette**: Background `#0A0F1A` | Foreground `#00FF41` | Accent `#003311`
- **Font**: JetBrains Mono / Fira Code (monospace)
- **Frame rate**: 12–15 fps (GIF optimization)

---

### Boot Sequence Script (4.5s)

| Time | Frame Content | Visual Notes |
|------|---------------|--------------|
| 0.0–0.5s | `████████████████████████████████████████` | Full-width scanline sweep (top→bottom) |
| 0.5–1.0s | `BIOS v2.6.1-rc3` ← `MEMORY TEST: 16384MB OK` | Typewriter effect, line-by-line |
| 1.0–1.5s | `GRUB 2.06` ← `BOOTING KERNEL 6.11.0-arch1-1` | Kernel version = your actual `uname -r` |
| 1.5–2.2s | `systemd[1]: INITIALIZING FORENSIC SUBSYSTEMS...` | Progress bar: `[████████░░] 80%` |
| 2.2–3.0s | `MODULE: TSK_LOADED` ✓ `VOLATILITY_LOADED` ✓ `GHIDRA_LOADED` ✓ | Staggered checkmarks, green flash |
| 3.0–3.8s | `AUTHENTICATING OPERATOR...` ← `SIDHARTH.S` ← `CLEARANCE: STUDENT` | Glitch transition on auth success |
| 3.8–4.5s | `HUD v1.0 ONLINE` | `STATUS: OPERATIONAL` | `MISSION: PRECISION ANALYSIS` | Pulsing cursor, hold 0.5s → loop |

---

### Generation Methods

#### Option A: `vhs` (Recommended — Clean, Code-Driven)
```bash
# Install: https://github.com/charmbracelet/vhs
cat > hud-boot.tape << 'EOF'
Output hud-boot-sequence.gif
Width 1200
Height 400
FontSize 14
FontFamily "JetBrains Mono"
Theme "dracula"  # custom: bg:#0A0F1A fg:#00FF41

Type "████████████████████████████████████████"
Sleep 500ms
Clear
Type "BIOS v2.6.1-rc3"
Sleep 200ms
Type "MEMORY TEST: 16384MB OK"
Sleep 300ms
Clear
Type "GRUB 2.06"
Sleep 150ms
Type "BOOTING KERNEL 6.11.0-arch1-1"
Sleep 500ms
Clear
Type "systemd[1]: INITIALIZING FORENSIC SUBSYSTEMS..."
Sleep 200ms
Type "[████████░░] 80%"
Sleep 500ms
Type "MODULE: TSK_LOADED      ✓"
Sleep 150ms
Type "MODULE: VOLATILITY_LOADED ✓"
Sleep 150ms
Type "MODULE: GHIDRA_LOADED    ✓"
Sleep 500ms
Clear
Type "AUTHENTICATING OPERATOR..."
Sleep 300ms
Type "OPERATOR: SIDHARTH.S"
Sleep 200ms
Type "CLEARANCE: STUDENT"
Sleep 300ms
Clear
Type "HUD v1.0 ONLINE"
Type "STATUS: OPERATIONAL"
Type "MISSION: PRECISION ANALYSIS"
Sleep 500ms
EOF

vhs < hud-boot.tape
```

#### Option B: `asciinema` + `agg` (Real Terminal Recording)
```bash
# Record
asciinema rec boot.cast -c "bash boot-script.sh"

# Convert to GIF
agg boot.cast hud-boot-sequence.gif --font-family "JetBrains Mono" --theme "dracula" --rows 12 --cols 60
```

#### Option C: Manual Frame Construction (Python + PIL)
```python
# generate_hud_gif.py
from PIL import Image, ImageDraw, ImageFont
import os

FRAMES = []
BG = "#0A0F1A"
FG = "#00FF41"
ACCENT = "#003311"
W, H = 1200, 400
FONT = ImageFont.truetype("JetBrainsMono-Regular.ttf", 18)

def frame(text_lines, scanline_y=None):
    img = Image.new("RGB", (W, H), BG)
    d = ImageDraw.Draw(img)
    for i, line in enumerate(text_lines):
        d.text((40, 40 + i * 30), line, font=FONT, fill=FG)
    if scanline_y is not None:
        d.line([(0, scanline_y), (W, scanline_y)], fill=ACCENT, width=2)
    FRAMES.append(img)

# Build sequence per table above...
FRAMES[0].save("hud-boot-sequence.gif", save_all=True, append_images=FRAMES[1:], duration=100, loop=0)
```

---

### Deployment

1. Generate `hud-boot-sequence.gif` using any method above
2. Place in `assets/hud-boot-sequence.gif`
3. Commit & push — the README references it via raw GitHub URL:
   ```
   https://github.com/Sidharth-2007/Sidharth-2007/raw/main/assets/hud-boot-sequence.gif
   ```

---

### Alternative: Static Hero (Zero Dependencies)

If GIF creation is deferred, replace the hero `<img>` tag in README with:

```markdown
<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com/?lines=INITIALIZING+HUD...;LOADING+FORENSIC+MODULES...;OPERATOR:+SIDHARTH.S;CLEARANCE:+STUDENT;STATUS:+OPERATIONAL&center=true&width=800&height=60&color=00FF41&background=0A0F1A&font=JetBrains+Mono" alt="HUD Boot" />
</p>
```

This uses the existing typing SVG service with your custom lines — no asset generation needed.