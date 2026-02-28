# Emacs Shell Cheatsheet

## Choosing Your Shell Mode

Emacs offers several ways to run shell commands. Each has trade-offs:

| Mode | Command | Best for |
|------|---------|----------|
| **Eshell** | `M-x eshell` | Daily use, Emacs integration, scripting |
| **Shell** | `M-x shell` | Familiar bash/zsh, less Emacs integration |
| **Term** | `M-x term` | Full terminal (ncurses apps work) |
| **Ansi-term** | `M-x ansi-term` | Better color support than term |
| **Comint** | (base of shell/eshell) | Building custom shells |

**Recommendation:** Start with eshell for daily use. Use `ansi-term` when you need a real terminal (vim, htop, etc.).

---

## Eshell

Eshell is unique — it's implemented in Emacs Lisp and understands both shell syntax and Emacs commands natively.

### Launching and Navigating

```bash
M-x eshell          # open eshell (or reuse existing)
C-c s               # if you added this binding to init.el

# Inside eshell, standard navigation applies:
C-p / M-p           # previous command
C-n / M-n           # next command
C-r                 # search command history
M-r                 # regexp search history
```

### Eshell-Specific Features

**Output to buffer:**
```bash
ls -la > #<buffer my-listing>       # send output to named buffer
grep -r "TODO" . >> #<buffer todos> # append to buffer
find . -name "*.c" > #<buffer c-files>
```

**Run Emacs commands from eshell:**
```bash
find-file ~/projects/foo/main.c     # open file in Emacs
dired ~/projects/                   # open directory in Dired
magit-status                        # open Magit
switch-to-buffer *scratch*
```

**Lisp expressions:**
```bash
(+ 2 3)               # → 5
(length "hello")      # → 5
(getenv "PATH")       # → your PATH
(emacs-version)       # → Emacs version string
```

**Globbing (more powerful than bash):**
```bash
ls **/*.c              # recursive glob — all .c files
ls **/test-*.c         # recursive with pattern
rm **/*.o              # delete all object files recursively
```

**Eshell aliases** (add to `~/.emacs.d/eshell/aliases`):
```bash
alias ll ls -la $*
alias la ls -a $*
alias g git $*
alias mc make clean $*
alias mr make $*
alias mct make clean && make test $*
```

**Redirect to clip/selection:**
```bash
echo "hello" > /dev/kill           # send to kill ring (Emacs clipboard)
cat file.txt > /dev/kill
```

### Eshell C Development Workflow

```bash
# Navigate to project
cd ~/projects/libfoo

# Check status
git status        # runs real git
# or:
magit-status      # opens Magit

# Build
make clean && make

# Run tests
./test_suite --verbose 2>&1 | head -50

# Check for memory errors
valgrind --leak-check=full ./myprogram

# Search for TODO items, send to buffer
grep -rn "TODO\|FIXME\|HACK" --include="*.c" --include="*.h" . > #<buffer todos>
# Then: C-x b todos to review them
```

---

## Shell Mode (M-x shell)

Shell mode runs your login shell as a subprocess. More familiar, less integrated.

```
M-x shell           # open shell buffer
```

### Shell Mode Keys

| Keys | What it does |
|------|-------------|
| `C-c C-c` | Send interrupt (like Ctrl+C) |
| `C-c C-z` | Send stop (like Ctrl+Z) |
| `C-c C-d` | Send EOF (like Ctrl+D) |
| `M-p` / `M-n` | Previous / next command |
| `C-c C-r` | Go to beginning of last output |
| `C-c C-e` | Go to end of buffer |
| `C-c C-o` | Delete last output |

---

## Term / Ansi-term (Full Terminal Emulator)

Use when you need ncurses apps (htop, vim, etc.) or need full terminal behavior.

```
M-x ansi-term       # prompts for shell (/bin/bash, /bin/zsh, etc.)
```

### Term Mode: Two Modes

**Char mode** (default) — keyboard goes to the program:
- Emacs keybindings mostly don't work
- `C-c C-j` — switch to line mode

**Line mode** — Emacs editing in terminal:  
- Normal Emacs keybindings work
- `C-c C-k` — switch back to char mode

### Useful Term Bindings

| Keys | What it does |
|------|-------------|
| `C-c C-j` | Switch to line mode |
| `C-c C-k` | Switch to char mode |
| `C-x o` | Still works to switch windows |
| `C-c C-c` | Send interrupt |

---

## Running Commands Without a Shell Buffer

For quick one-off commands without opening a shell:

| Command | Keys | What it does |
|---------|------|-------------|
| Shell command | `M-!` | Run one shell command |
| Shell command on region | `M-\|` | Pipe region through command |
| Async shell command | `M-&` | Run command asynchronously |

```
M-! ls -la <RET>                    # output in minibuffer or buffer
M-! make 2>&1 <RET>                 # run make
M-| python3 -m json.tool <RET>      # format selected JSON through python
M-& make -j4 <RET>                  # async compile
```

---

## Compile Mode (Preferred for Building C)

Better than running make in a shell buffer, because errors become clickable navigation links.

```
M-x compile         # runs compile, prompts for command
M-x recompile       # reruns last compile command (bind to <F5>)
```

### In the Compile Buffer

| Keys | What it does |
|------|-------------|
| `g` | Rerun the compilation |
| `C-x \`` | Next error (also works from source buffer) |
| `M-g n` | Next error |
| `M-g p` | Previous error |
| `<RET>` on error | Jump to that file/line |
| `q` | Bury the compile buffer |

**Setup for automatic compilation binding:**
```elisp
;; In init.el:
(global-set-key (kbd "<f5>") 'recompile)
(global-set-key (kbd "<f6>") 'next-error)
(global-set-key (kbd "<f7>") 'previous-error)

;; Scroll compile buffer automatically:
(setq compilation-scroll-output t)

;; Don't ask for save before compile:
(setq compilation-ask-about-save nil)
```

---

## Combining Shell and Emacs: The Power Pattern

The real power is switching between shell and editing without friction:

1. Edit C file in main window
2. `C-c s` → jump to eshell (split or existing)
3. Run `make` or `./myprogram`
4. See error output
5. `C-x o` → switch back to source, navigate to error
6. Fix, `C-x C-s` save
7. `C-x o` → back to eshell, up arrow, Enter — rerun

Once this loop is in your muscle memory, you'll wonder how you ever worked any other way.
