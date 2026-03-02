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

Eshell is unique — it's implemented in Emacs Lisp and understands both shell syntax and Emacs commands natively. It has no pager because it doesn't need one: output is a buffer, and you navigate it with Emacs. A good deeper reference is Howard Abrams' essay [Eschewing Zshell for Emacs Shell](https://www.howardism.org/Technical/Emacs/eshell-fun.html).

### The `eshell-here` Pattern — Context-Aware Shell

The most useful eshell setup: a function that opens a shell in the directory of whatever file you're currently editing, in a split window at the bottom third of the screen. Add this to your `init.el`:

```elisp
(defun eshell-here ()
  "Open eshell in the directory of the current buffer's file,
in a window taking up the bottom third of the frame."
  (interactive)
  (let* ((parent (if (buffer-file-name)
                     (file-name-directory (buffer-file-name))
                   default-directory))
         (height (/ (window-total-height) 3))
         (name   (car (last (split-string parent "/" t)))))
    (split-window-vertically (- height))
    (other-window 1)
    (eshell "new")
    (rename-buffer (concat "*eshell: " name "*"))
    (insert (concat "ls"))
    (eshell-send-input)))

(global-set-key (kbd "C-!") 'eshell-here)

;; Type 'x' in eshell to close it and its window
(defun eshell/x ()
  (insert "exit")
  (eshell-send-input)
  (delete-window))
```

`C-!` drops you into a shell already in your project directory. `x` closes it and returns focus to your code. This is the everyday workflow — not a persistent shell but a quick pop-out-and-back.

### Launching and Navigating

```bash
M-x eshell          # open eshell (or reuse existing)
C-!                 # context-aware eshell (if you added above)
C-c s               # if you added this binding to init.el

# Navigation:
C-p / M-p           # previous command
C-n / M-n           # next command
C-r                 # search command history
M-r                 # regexp search history

# Navigate output without a pager — you don't need less/more:
C-c C-p             # jump to top of last command's output
C-v                 # scroll down from there
C-s                 # search through output
```

### Plan 9 Smart Display Mode

Enable this for an Acme-like experience — output starts at the top automatically, and you only drop to the prompt when you start typing:

```elisp
(require 'eshell)
(require 'em-smart)
(setq eshell-where-to-jump 'begin)
(setq eshell-review-quick-commands nil)
(setq eshell-smart-space-goes-to-end t)
```

### Output to Buffers

```bash
ls -la > #<buffer my-listing>        # send output to named buffer
grep -r "TODO" . >> #<buffer todos>  # append to buffer
find . -name "*.c" > #<buffer c-files>
ls -al > #<buffer notes.org>         # then C-c | in that buffer for an org table
```

### Run Emacs Commands Directly

```bash
find-file ~/projects/foo/main.c     # open file in Emacs
dired ~/projects/                   # open directory in Dired
magit-status                        # open Magit
switch-to-buffer *scratch*
highlight-regexp "ERROR"            # colorize keywords in an output buffer
```

### Lisp Expressions — Eshell is a REPL

```bash
(+ 2 3)               # → 5
(length "hello")      # → 5
(getenv "PATH")       # → your PATH
(emacs-version)       # → Emacs version string
message "hello"       # parens optional for top-level calls
setq CC "clang"       # set an Emacs variable
echo $CC              # → clang
echo $recentf-max-menu-items   # access any Emacs variable with $
```

### Globbing — More Powerful Than Bash

```bash
ls **/*.c              # recursive glob — all .c files
ls **/test-*.c         # recursive with pattern
rm **/*.o              # delete all object files recursively
ls ***/*.sh            # recursive without following symlinks
```

### Glob Predicates — Eshell's Secret Weapon

Filter files by attributes directly in the glob pattern. Run `eshell-display-predicate-help` for the full list:

```bash
ls *(/)           # directories only
ls *(.)           # regular files only
ls *(*)           # executable files only
ls *(@)           # symlinks only
ls *(U)           # files owned by you
ls *(IW)          # writable by group or world
ls *.c(m-1)       # .c files modified in the last day
ls *.org(mh-8)    # .org files modified in last 8 hours
bzip2 **/*(a+30)  # compress everything not accessed in 30 days
ls *.sh(*Lk+50)   # executable .sh files 50KB or larger
ls *(@^U)         # symlinks NOT owned by you
```

Predicates stack: `ls **/*(IW)` — all files recursively writable by group or world. Good for security audits.

### Glob Modifiers — Transform Filenames in Place

Run `eshell-display-modifier-help` for the full list:

```bash
echo $filename(:r)        # strip extension  (foo.c → foo)
echo $filename(:e)        # just the extension  (foo.c → c)
echo $filename(:t)        # basename  (/path/foo.c → foo.c)
echo $filename(:h)        # dirname  (/path/foo.c → /path)
echo $filename(:U)        # uppercase
echo $filename(:L)        # lowercase

# Practical: rename all .c files, append -old before extension
for F in *.c { mv $F $F(:r)-old.$F(:e) }
# foo.c → foo-old.c

# List modifiers
ls *(:o)                  # sort alphabetically
echo $mylist(:j/, /)      # join list with ", " separator
echo $mylist(:x/foo/)     # exclude members matching "foo"
```

### Loops

```bash
for file in *.c {
  echo "Checking: $file"
  gcc -Wall -fsyntax-only $file
}

for i in 1 2 3 4 { echo $i }

# Combine globs, predicates, and loops:
for f in src/**/*.c(m-7) { echo "Recently modified: $f" }
```

### Visual Commands — When Eshell Hands Off

Programs that use terminal control codes won't work in eshell's text buffer. Eshell maintains `eshell-visual-commands` — when you run one of these, eshell automatically opens a proper `term` buffer instead:

```bash
top     # eshell notices and opens in term automatically
htop    # same
vim     # same
man     # same
```

Add your own:
```elisp
(add-to-list 'eshell-visual-commands "mycurses-app")
```

### Aliases

Add to `~/.emacs.d/eshell/aliases`:
```bash
alias ll ls -la $*
alias la ls -a $*
alias g git $*
alias mc make clean $*
alias mr make $*
alias mct make clean && make test $*
```

### Eshell C Development Workflow

```bash
# C-! to open context-aware shell — already in the right directory

# Build and capture errors
make clean && make

# Run tests, send failures to a buffer for review
./test_suite --verbose 2>&1 | grep FAIL > #<buffer test-failures>
# C-x b test-failures to review

# Memory errors
valgrind --leak-check=full ./myprogram

# Find all TODO/FIXME/HACK, send to buffer, click lines to jump
grep -rn "TODO\|FIXME\|HACK" --include="*.c" --include="*.h" . > #<buffer todos>

# Find large leftover object files (predicate: Lk+100 = over 100KB)
ls **/*.o(Lk+100)

# Clean them all
rm **/*.o

# Security check: world-writable files in project
ls **/*(W)
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

## TRAMP: Your Entire Emacs Environment, Anywhere

TRAMP (Transparent Remote Access, Multiple Protocols) is built into Emacs and is one of its most quietly powerful features. The core idea: `C-x C-f` doesn't know or care whether the file is local or on a machine three timezones away. You get your full editing environment — keybindings, config, Magit, compile mode, eshell — working on remote files without logging into a separate session.

This matters especially if you work across multiple laptops. Your Emacs config travels with you locally; TRAMP means your Emacs config also travels to every server you touch.

### Basic Usage

```
C-x C-f /ssh:user@hostname:/path/to/file.c
```

That's it. Emacs opens an SSH connection, fetches the file, and you edit it locally. Saving sends it back. The file feels local because to Emacs it is local — it's just a buffer.

**Shorthand if your SSH config has aliases:**
```
C-x C-f /ssh:myserver:/path/to/file.c
```

TRAMP reads your `~/.ssh/config`, so any Host aliases, IdentityFile settings, or ProxyJump directives you have there work automatically.

**Open a directory in Dired over TRAMP:**
```
C-x d /ssh:user@hostname:/path/to/project/
```

Navigate the remote filesystem exactly as you would locally. Create files, rename them, delete them — all via Dired, all transparently over SSH.

### sudo and su

Edit files requiring root on a remote machine:

```
# sudo on local machine:
C-x C-f /sudo::/etc/hosts

# sudo on remote machine (pipe through SSH then sudo):
C-x C-f /ssh:user@hostname|sudo:hostname:/etc/nginx/nginx.conf
```

The `|` chains methods. Read it as: "SSH to hostname as user, then sudo on hostname to open the file." This is the pattern for editing system config on remote servers without a separate root SSH session.

### Multi-Hop Connections

Reach machines that aren't directly accessible — jump hosts, internal networks, anything your SSH config handles:

```
# Two hops: local → jumphost → internal-server
C-x C-f /ssh:user@jumphost|ssh:user@internal-server:/path/to/file.c

# Three hops:
C-x C-f /ssh:jump1|ssh:jump2|ssh:target:/path/file.c
```

Or configure it once in `~/.ssh/config` using `ProxyJump` and let TRAMP use a simple `/ssh:target:/path` with the hops handled invisibly.

### What Works Over TRAMP

This is the part that surprises people. It's not just file editing:

**Dired** — full filesystem navigation, file operations, bulk rename, everything. The remote directory is just a Dired buffer.

**Magit** — open a TRAMP buffer in a remote git repo and `C-x g` works. You're running Magit against the remote repo, seeing remote git history, staging and committing remotely, all from your local Emacs with your local Magit config.

**Compile mode** — `M-x compile` runs the compile command on the remote machine. Errors link back to remote files which TRAMP opens transparently. The full `<f5>` → error navigation loop works remotely.

**Eshell** — `M-x eshell` while visiting a TRAMP buffer opens a shell on the remote machine. Commands run there, output comes back as a buffer.

**Shell** — `M-x shell` similarly opens on the remote host when called from a TRAMP buffer.

**grep / rgrep** — searches the remote filesystem. Results link to remote files.

### Performance Tuning

TRAMP can feel slow on high-latency connections or large directories. These settings help considerably:

```elisp
;; In init.el:

;; Cache remote file attributes aggressively
(setq tramp-completion-reread-directory-timeout nil)

;; Use ssh ControlMaster to reuse connections (fastest option)
;; Requires OpenSSH — add to ~/.ssh/config:
;;   Host *
;;     ControlMaster auto
;;     ControlPath ~/.ssh/cm-%r@%h:%p
;;     ControlPersist 10m
;; Then tell TRAMP to use it:
(setq tramp-ssh-controlmaster-options
      (concat "-o ControlPath=~/.ssh/cm-%%r@%%h:%%p "
              "-o ControlMaster=auto "
              "-o ControlPersist=10m"))

;; Disable version control checks on remote files (big speedup)
(setq vc-ignore-dir-regexp
      (format "\\(%s\\)\\|\\(%s\\)"
              vc-ignore-dir-regexp
              tramp-file-name-regexp))

;; Use /tmp for TRAMP temp files (faster than default)
(setq tramp-auto-save-directory "/tmp/tramp-autosave/")

;; Verbose logging only when debugging (0 = silent, 6 = verbose)
(setq tramp-verbose 1)
```

**The single biggest speedup:** SSH ControlMaster in your `~/.ssh/config`. Once the first connection is established, subsequent TRAMP operations reuse the socket instead of re-authenticating. On a slow connection this is the difference between TRAMP being frustrating and it being seamless.

### Practical Workflow: Multi-Laptop + Remote Servers

With TRAMP, the multi-laptop situation becomes an asset rather than a complication. Your workflow:

1. Any laptop → open Emacs with your config
2. `C-x d /ssh:devserver:/projects/libfoo/` → browse remote project in Dired
3. Open files, edit with full local keybindings and config
4. `C-x g` → Magit on the remote repo
5. `<f5>` → compile on the remote machine, errors navigate back to remote files
6. `M-x eshell` → shell on the remote machine for anything else

You never SSH into a separate terminal. You never lose your editor config. The remote machine is just another surface your Emacs is editing.

This is the Acme insight applied to network transparency: the distinction between "local file" and "remote file" dissolves into just *a path that Emacs can open*.

### TRAMP Troubleshooting

**Connection hangs on open:**
Check that your SSH connection works manually first (`ssh user@host`). TRAMP inherits all SSH issues. Also check that the remote shell doesn't print anything to stdout on login (`.bashrc` output breaks TRAMP's handshake — guard it with `[[ $- == *i* ]]` before any echo statements).

**"Tramp: Waiting for prompts from remote shell" forever:**
Your remote `.bashrc` or `.bash_profile` is printing something unexpected. Add this guard around any output in your remote shell config:
```bash
# Only print things in interactive shells
if [[ $- == *i* ]]; then
    echo "welcome message or whatever"
fi
```

**Slow directory listings:**
```elisp
(setq tramp-use-ssh-controlmaster-options t)
```
And ensure ControlMaster is configured in `~/.ssh/config` as above.

**Check what TRAMP is doing:**
```
M-x tramp-cleanup-all-connections   ; reset all connections
M-x tramp-bug                       ; generate bug report with diagnostics
(setq tramp-verbose 6)              ; temporary verbose logging
```


The real power is switching between shell and editing without friction:

1. Edit C file in main window
2. `C-c s` → jump to eshell (split or existing)
3. Run `make` or `./myprogram`
4. See error output
5. `C-x o` → switch back to source, navigate to error
6. Fix, `C-x C-s` save
7. `C-x o` → back to eshell, up arrow, Enter — rerun

Once this loop is in your muscle memory, you'll wonder how you ever worked any other way.
