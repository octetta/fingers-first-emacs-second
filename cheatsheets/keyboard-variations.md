# Keyboard Variations: Surviving Multiple Laptops

## The Universal Fix: Remap Caps Lock to Ctrl

Do this on every machine before anything else. It is the most important single configuration for Emacs. Without it, your left pinky travels to the bottom-left corner constantly. With it, Ctrl is right above the left pinky's home row position.

**The old advice (`setxkbmap -option ctrl:nocaps`, `~/.Xmodmap`) does not work on Wayland**, which is the default on modern Fedora and many other current distros.

### Fedora / GNOME on Wayland — the dconf method

Recommended for Fedora 38+. Takes effect immediately, survives reboots:

```bash
dconf write /org/gnome/desktop/input-sources/xkb-options "['ctrl:nocaps']"
```

Verify it stuck:
```bash
gsettings get org.gnome.desktop.input-sources xkb-options
# should print: ['ctrl:nocaps']
```

To undo:
```bash
gsettings reset org.gnome.desktop.input-sources xkb-options
```

If you already have other xkb-options (like a compose key), preserve them:
```bash
dconf write /org/gnome/desktop/input-sources/xkb-options "['ctrl:nocaps', 'compose:ralt']"
```

### Fedora / GNOME — GUI alternative

```bash
sudo dnf install gnome-tweaks
```
Open GNOME Tweaks → Keyboard → Additional Layout Options → Ctrl key position → Caps Lock as Ctrl.

### keyd — Works Everywhere Including TTY

`keyd` is a kernel-level remapper that works regardless of display server (Wayland, X11, or bare TTY). Best option if dconf isn't sticking, or if you need the remap outside a desktop session.

```bash
# Install via COPR (not in main Fedora repos yet):
sudo dnf copr enable alternateved/keyd
sudo dnf install keyd
```

Create `/etc/keyd/default.conf`:
```ini
[ids]
*

[main]
capslock = leftcontrol
```

Enable and start:
```bash
sudo systemctl enable keyd
sudo systemctl start keyd
```

**Bonus — dual function (Ctrl when held, Escape when tapped):**
```ini
[ids]
*

[main]
capslock = overload(control, esc)
```
Useful if vi muscle memory is bleeding over and you want Escape on the same key.

### TTY Console Sessions

If you need the remap in a TTY outside any desktop:
```bash
sudo localectl set-x11-keymap us "" "" ctrl:nocaps
# Replace 'us' with your actual layout if different
```

### Other Platforms

| Platform | Method |
|----------|--------|
| **Linux (X11 only)** | `setxkbmap -option ctrl:nocaps` in `~/.bashrc` |
| **macOS** | System Settings → Keyboard → Keyboard Shortcuts → Modifier Keys → Caps Lock → Ctrl |
| **Windows** | [PowerToys Keyboard Manager](https://learn.microsoft.com/en-us/windows/powertoys/) or SharpKeys |

### What Doesn't Work on Modern Fedora

```bash
# X11-only — NO EFFECT under Wayland:
setxkbmap -option ctrl:nocaps        # ✗
~/.Xmodmap                           # ✗ not loaded
```

Check which session type you're in:
```bash
echo $XDG_SESSION_TYPE   # 'wayland' or 'x11'
```

---

## Forcing the Issue: Keyboard Stickers

If you want to make it physically impossible to revert to hunt-and-peck, blank keyboard stickers are more committed than a cloth. You can still feel the keys — the bumps on F and J remain your anchors — but visual lookup becomes impossible.

Search for "blank keyboard stickers" or "black keyboard stickers" — cheap, widely available, fit most laptop key sizes. A practical approach:

1. Start by blanking letters only (leave symbols visible while you build letter memory)
2. Once letters are solid, blank the number row
3. Finally blank the symbol keys as your C symbol muscle memory develops

This is the "point of no return" approach from Hakan Serce's [guide for seasoned hunt-and-peckers](https://blog.hakanserce.com/post/how-to-learn-touch-typing-a-guide-for-seasoned-hunt-and-pecker/). It removes the option to cheat rather than relying on willpower to resist it.

Note: stickers on all your laptops or none. Stickered on one machine and bare keys on another means you're still maintaining the visual lookup reflex on the unstickered machines.

---

## The Laptop Keyboard Problem Map

### Problem: Tiny or Missing Keys

**Right Shift too small** (common on 60%/75% laptops)  
Some laptops put `↑` next to right Shift, making it tiny. 
- Consciously reach for the wider key
- Or remap right Shift in your OS if possible
- Touch typing means you don't see it anyway — trust your finger position

**Missing F-row keys** (common on MacBooks, ultrabooks)  
Fn keys may be virtual or require Fn+key combinations.
- For Emacs, you can avoid F-key bindings entirely (they're optional, not core)
- Or use `M-x` to run any command by name instead of the F-key shortcut
- If you use F5 for recompile, just remap it to something else on problem machines:
  ```elisp
  (global-set-key (kbd "C-c m") 'recompile)  ; works everywhere
  ```

**Missing Home/End/PgUp/PgDn keys**  
These aren't needed in Emacs anyway:
- `C-a` / `C-e` → beginning/end of line
- `C-v` / `M-v` → page down/up  
- `M-<` / `M->` → beginning/end of buffer

**Compact numpad or missing numpad**  
Rarely relevant for Emacs. Ignore.

---

### Problem: Key Position Differences

**Right Alt vs Meta (Alt)**  
Emacs uses Meta (`M-`), which is usually the Alt key.
- On some laptops, only Left Alt works as Meta in terminal emulators
- Workaround: Use Esc as Meta instead (`Esc` then the key, rather than Alt+key)
- Or configure your terminal to send Meta: in iTerm2/GNOME Terminal, enable "Use Alt as Meta"

**Ctrl placement after Caps Lock remap**  
Different laptop form factors mean Caps Lock is in the same physical position across all of them — this is a QWERTY standard. Once you remap it, Ctrl is always in the same relative position.

**Backspace vs Delete**  
On some laptops, the top-right key is `Delete` (forward delete) not `Backspace`.
- Emacs: Backspace = `<DEL>` = delete backward character
- Forward delete = `C-d`
- If your laptop puts a tiny Backspace somewhere odd, just use `C-h` (which also deletes backward in many Emacs contexts) or remap

---

### Problem: Laptop-Specific Annoyances

**MacBook (all models)**

```
; Caps Lock → Ctrl: System Preferences → Keyboard → Modifier Keys
; Option as Meta: In terminal preferences, enable "Use Option as Meta"  
; Or in iTerm2: Profiles → Keys → Left Option Key → Esc+
```

MacBook keyboards have good key feel and consistent layout across generations. The main issue is Option/Meta. The Touch Bar models (2016–2021) make F-keys unreliable — use `C-c` prefix bindings instead.

**ThinkPad**

ThinkPad keyboards are generally excellent for Emacs. The TrackPoint in the middle means your hands stay on home row. Main issue:

```
; Fn key location varies — older ThinkPads put Fn on bottom left
; which can interfere if you're not careful
; In BIOS/UEFI: toggle "Fn lock" or "Legacy F-key behavior"
```

**Surface / Dell XPS / HP Spectre**

Often have shallow key travel. Touch typing is harder on shallow keyboards; slow down more and be precise. The homing bumps on F and J may be subtle — feel for them carefully before each session.

**Chromebook**

No Caps Lock key at all (it's a Search key). Remap Search → Ctrl in ChromeOS settings. The keyboard layout is otherwise close to standard.

---

## Touch Typing Across Different Keyboards

The fundamental principle of touch typing is **relative positioning from home row**, not absolute key memorization. Once your hands find F and J by feel:

- All keys are at defined positions relative to those anchors
- You don't need to see the keyboard
- Layout variations matter less than they seem

What actually varies:
- **Key travel** (depth): Deep keys → more feedback → easier. Shallow keys → need more precision.
- **Key resistance**: Varies a lot. Higher resistance → slower but more deliberate. Adjust your speed accordingly.
- **Keyboard width**: 75%, TKL, full size. The alpha keys (letters) are always the same. Symbol keys on 60%/75% may require Fn layer.

**Practice strategy for multi-laptop users:**  
When switching to an unfamiliar keyboard, spend 2–3 minutes on home-row anchor drills (`fjfjfj`, `asdf jkl;`) before starting real work. This recalibrates your muscle memory to the specific key feel.

---

## Terminal Emulator Configuration

Different terminal emulators affect how Emacs receives key events. Issues to check:

### Common Terminal Issues

**Alt/Meta not working:**
```
; iTerm2: Preferences → Profiles → Keys → Left Option Key → Esc+
; GNOME Terminal: Edit → Preferences → Compatibility → uncheck "Use Alt key"
; Konsole: Settings → Edit Current Profile → Keyboard → Meta key = Alt
; Windows Terminal: Set "altGrAliasing": false in settings.json
```

**Ctrl+Space (set-mark) not working:**
```
; Some terminals intercept C-SPC for input methods
; Workaround: use C-@ instead (does the same thing in Emacs)
; Or configure your terminal to not intercept it
```

**C-/ (undo) not working:**
```
; Use C-x u instead, which works everywhere
; Or C-_ (underscore) which is the same Emacs command
```

### Using Emacs GUI vs Terminal

GUI Emacs (`emacs`) avoids terminal key translation issues entirely. Key events go directly to Emacs.

Terminal Emacs (`emacs -nw`) is more portable, works over SSH, and some people prefer it. Just be aware of the key event limitations above.

**For multi-laptop use:** GUI Emacs is more consistent. But terminal Emacs over SSH is invaluable — learn both.

---

## SSH and Remote Emacs

When working on remote machines you have two options: run Emacs on the remote machine directly over SSH, or use TRAMP to edit remote files from your local Emacs. See `cheatsheets/emacs-shell.md` for a full treatment of TRAMP — it's the more powerful option and worth learning properly.

```bash
# Run Emacs on remote machine directly:
ssh -Y user@remote   # -Y enables X forwarding for GUI Emacs
emacs myfile.c       # or emacs -nw for terminal mode
```
