# Emacs Shell Cheatsheet

## The Acme/Plan 9 Mental Model: Text as Command Surface

If you've spent time with Rob Pike's Acme editor from Plan 9, you'll recognize something familiar in Emacs's shell integration — and you can push it much further than most Emacs users do.

Acme's core idea: **the editor and the shell share the same surface.** Any text can be a command. Any output is text you can act on further. There's no meaningful distinction between "the editor" and "the environment." You middle-click a word to execute it; the output appears as text you can middle-click again.

Emacs doesn't go quite that far architecturally, but it gets surprisingly close. The key insight is that **every piece of output in Emacs is a buffer**, and every buffer is subject to everything Emacs can do to text — search, edit, pipe onward, commit to git, execute as code.

### The Core Primitives (Your Acme Toolkit)

**`M-!` — Run a command, get a buffer**

```
M-! ls -la          → output appears in *Shell Command Output* buffer
M-! date            → current date as editable text
M-! cat notes.txt   → file contents in a buffer you can edit and save elsewhere
M-! make 2>&1       → build output as navigable text
```

This is Acme's middle-click on a command string. The output isn't printed and forgotten — it's text. You can search it, copy from it, pipe it onward.

With `C-u` prefix, output inserts at point instead of a separate buffer:

```
C-u M-! date        → inserts the date at cursor position in current buffer
C-u M-! cat header_template.c   → inserts file contents at cursor
```

**`M-|` — Pipe region through a command, replace with output**

Select a region, run a command, the region is replaced by the command's output. This is Acme's pipe operation, and it's extraordinarily powerful for C work:

```
[select a block of C code]
M-| clang-format    → reformats selected code in place
M-| indent          → GNU indent on selection
M-| sort            → sort selected lines
M-| uniq            → deduplicate selected lines
M-| column -t       → align selected text into columns
M-| python3 -c "import sys; print(len(sys.stdin.read()))"  → count chars in selection
```

This is the Acme pipe model: text goes in, text comes out, the buffer is the surface.

**Eshell buffer redirection — output as named buffers**

```bash
# In eshell:
make 2>&1 > #<buffer build-output>
grep -rn "TODO" src/ > #<buffer todos>
find . -name "*.c" | xargs wc -l > #<buffer line-counts>
nm ./myprogram | grep " T " > #<buffer symbols>
```

Each of those output buffers is a first-class Emacs buffer. You can search them with `C-s`, run `M-x occur` on them, copy from them, even pipe their contents through further commands with `M-|`. The output of one command becomes the input surface for the next.

**`M-x shell-command-on-region` — the explicit version**

Same as `M-|` but named, runnable from `M-x` when you forget the binding.

### Composing Commands the Acme Way

The real power emerges when you chain these:

**Example: Find all malloc calls in a project, view as editable list**
```bash
# In eshell:
grep -rn "malloc" src/ --include="*.c" > #<buffer mallocs>
# Switch to that buffer: C-x b mallocs
# Now M-x occur on "sizeof" to find which ones use sizeof correctly
# Copy the bad ones, pipe through a script, fix in place
```

**Example: Generate a struct from a header, insert at point**
```
C-u M-! grep "^typedef struct" myheader.h
→ inserts all struct typedefs at cursor — editable scaffolding
```

**Example: Format only the function you're editing**
```
C-M-h          → mark current function
M-| clang-format-region   → format just that function
```

**Example: Check a value without leaving the file**
```
M-! python3 -c "print(0xFF & ~0x3C)"
→ quick bit arithmetic result in a buffer, never left your C file
```

### Org-Babel: The Closest Thing to Acme's Model

Org-mode's source blocks go furthest toward Acme's vision — executable text inline in a document:

```org
* Build Notes

Run this to check for memory leaks:
#+BEGIN_SRC sh
valgrind --leak-check=full ./myprogram 2>&1 | head -30
#+END_SRC

#+RESULTS:
==12345== LEAK SUMMARY:
==12345==    definitely lost: 0 bytes in 0 blocks
...
```

Press `C-c C-c` on the `#+BEGIN_SRC` block: it executes and the output appears in `#+RESULTS:` below it. The document is live. The notes and the commands that verify the notes are the same text.

This is what Acme was pointing at: a surface where the distinction between "documentation," "commands," and "output" dissolves into just *text that does things*.

### The Mental Shift

Once you start using `M-!` and `M-|` regularly, something changes in how you think about the editor. You stop thinking "I need to go to the terminal to do X, then come back." You start thinking "what command would transform this text into what I want?" The buffer is always the surface. The shell is always available. Output is always text.

That's the Acme insight, running inside Emacs.

---

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
