# Tools & Resources

## Installing Emacs

### Linux
```bash
# Ubuntu/Debian (usually slightly behind latest):
sudo apt install emacs

# For latest version (Emacs 29+):
sudo add-apt-repository ppa:kelleyk/emacs
sudo apt update
sudo apt install emacs29

# Arch:
sudo pacman -S emacs

# Fedora/RHEL:
sudo dnf install emacs
```

### macOS
```bash
# Via Homebrew (recommended):
brew install --cask emacs       # GUI Emacs
brew install emacs              # CLI-focused build

# Emacs for Mac OS X (pre-built, excellent):
# https://emacsformacosx.com/
```

### Windows
- [GNU Emacs for Windows](https://www.gnu.org/software/emacs/download.html) — official
- [MSYS2 + Emacs](https://www.msys2.org/) — Unix-like environment

### Check version
```bash
emacs --version
# Aim for Emacs 29.x for best features (tree-sitter, eglot built-in)
```

---

## Typing Tutor Tools

### Online (no install)
- [typingclub.com](https://www.typingclub.com) — structured lesson progression (lessons 1-188 cover basics through shift key); good for complete beginners or people who want a clear syllabus rather than adaptive training
- [keybr.com](https://www.keybr.com) — smart adaptive training, highly recommended
- [monkeytype.com](https://monkeytype.com) — speed testing, code mode available
- [typing.com](https://www.typing.com) — structured lessons, free
- [typeracer.com](https://www.typeracer.com) — competitive, fun after week 6; good for motivation
- [ratatype.com](https://www.ratatype.com) — focused on accuracy and muscle memory for symbols; particularly good for `[]`, `{}`, `|` and other programmer punctuation that other sites neglect
- [typing.io](https://www.typing.io) — typing practice specifically using real source code; supports C, C++, and many other languages; use from week 7 onward

**Suggested progression:** typingclub.com for weeks 1-3 (structured foundation), keybr.com for weeks 4-8 (adaptive refinement), typeracer.com and typing.io from week 6 onward (speed and code-specific practice), ratatype.com whenever symbols feel weak.

### Command Line (install once)
```bash
# gtypist — terminal-based typing tutor
sudo apt install gtypist        # Linux
brew install gtypist            # macOS

# Lessons relevant to this plan:
# D.1 - D.5: Home row drills
# T.1 - T.5: Top row
# B.1 - B.5: Bottom row
gtypist                         # runs interactive menu
```

### For C code specifically
[speedcoder.net/lessons/c/1](https://www.speedcoder.net/lessons/c/1) has C-specific typing lessons. Use from week 7 onward.

---

## Essential Emacs Packages

These are the packages referenced in the weekly plans. Install via `M-x package-install` or add to `init.el`.

### Must-Have
| Package | Purpose | Install |
|---------|---------|---------|
| `which-key` | Shows keybinding completions | `M-x package-install which-key` |
| `magit` | Best git interface | `M-x package-install magit` |
| `company` | Auto-completion | `M-x package-install company` |
| `ivy` + `counsel` | Better completion UI | See init.el starter |

### Highly Recommended
| Package | Purpose |
|---------|---------|
| `eglot` | LSP client (built into Emacs 29+) |
| `rg` | Ripgrep integration |
| `yasnippet` | Code snippets |
| `hl-todo` | Highlight TODO/FIXME |
| `projectile` | Project management |

### Nice to Have
| Package | Purpose |
|---------|---------|
| `vertico` + `consult` | Alternative to ivy/counsel (more modern) |
| `treemacs` | File tree sidebar |
| `vterm` | Best terminal emulator in Emacs |
| `flycheck` | Real-time syntax checking |
| `multiple-cursors` | Multiple cursor editing |

### Installing packages automatically in init.el
```elisp
;; Bootstrap straight.el (alternative to package.el, more reproducible):
(defvar bootstrap-version)
(let ((bootstrap-file
       (expand-file-name "straight/repos/straight.el/bootstrap.el" user-emacs-directory))
      (bootstrap-version 6))
  (unless (file-exists-p bootstrap-file)
    (with-current-buffer
        (url-retrieve-synchronously
         "https://raw.githubusercontent.com/radian-software/straight.el/develop/install.el"
         'silent 'inhibit-cookies)
      (goto-char (point-max))
      (eval-print-last-sexp)))
  (load bootstrap-file nil 'nomessage))
```

---

## External Tools That Work Well with Emacs

### For C Development
```bash
# clangd — C/C++ language server (for eglot/lsp)
sudo apt install clangd         # or clangd-15, clangd-16
brew install llvm               # macOS

# bear — generates compile_commands.json from make
sudo apt install bear
brew install bear

# ctags / etags
sudo apt install universal-ctags
brew install universal-ctags

# ripgrep — faster grep, used by rg.el
sudo apt install ripgrep
brew install ripgrep

# valgrind (Linux only)
sudo apt install valgrind

# gdb
sudo apt install gdb
```

### Checking what you have
```bash
which clangd ctags rg valgrind gdb make gcc
```

---

## Learning Resources

### Eshell Specifically
- [Eschewing Zshell for Emacs Shell](https://www.howardism.org/Technical/Emacs/eshell-fun.html) — Howard Abrams' deep dive; the source of the `eshell-here` pattern, glob predicates, and Plan 9 smart display mode used in this guide
- [Mastering Eshell](https://www.masteringemacs.org/article/complete-guide-mastering-eshell) — Mickey Petersen's comprehensive reference (free article)

### Emacs Documentation
- [Emacs Manual](https://www.gnu.org/software/emacs/manual/emacs.html) — comprehensive, official
- [Emacs Lisp Reference](https://www.gnu.org/software/emacs/manual/elisp.html) — when you're ready to customize
- [Mastering Emacs](https://www.masteringemacs.org/) — excellent blog, book available
- Built-in: `C-h t` (tutorial), `C-h r` (full manual)

### C-Mode Specifically
- `C-h i` → Emacs → CC Mode (full CC mode manual, built-in)
- [CC Mode Manual](https://www.gnu.org/software/emacs/manual/ccmode.html)

### Touch Typing
- [Do Programmers Actually Need Touch Typing?](https://blog.hakanserce.com/post/do-programmers-actually-need-touch-typing/) — Hakan Serce; the best short case for why cognitive load, not speed, is the real argument
- [How to Learn Touch Typing: a Guide for Seasoned Hunt and Pecker](https://blog.hakanserce.com/post/how-to-learn-touch-typing-a-guide-for-seasoned-hunt-and-pecker/) — Serce's follow-up; the "two lives" method, blank stickers, and a realistic progression that this guide draws on
- Any of the online tools above have their own learning resources

### Community
- [r/emacs](https://www.reddit.com/r/emacs/) — active, helpful
- [GNU Emacs Mailing Lists](https://lists.gnu.org/mailman/listinfo/help-gnu-emacs)
- [Emacs Stack Exchange](https://emacs.stackexchange.com/)

---

## WPM Testing Protocol

To get consistent measurements across the 90 days:

1. Use the same tool each time ([monkeytype.com](https://monkeytype.com) recommended)
2. Set: Mode=Words, Duration=60 seconds, Punctuation=off, Numbers=off
3. Take 3 tests, record the middle score (discard outliers)
4. Test at the same time of day (energy affects WPM ±5–10 WPM)
5. Record in your daily log

For C code specifically, use [speedcoder.net](https://www.speedcoder.net) C mode. The scores will be lower than general text — that's normal.
