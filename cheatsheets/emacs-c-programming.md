# Emacs C Programming Cheatsheet

## C-Mode Basics

C-mode activates automatically for `.c`, `.h`, `.cc`, `.cpp` files.

### Navigation in C Code

| Keys | What it does |
|------|-------------|
| `C-M-a` | Beginning of current function |
| `C-M-e` | End of current function |
| `C-M-h` | Mark (select) entire function |
| `C-M-f` | Forward over balanced expression `()`, `[]`, `{}` |
| `C-M-b` | Backward over balanced expression |
| `C-M-n` | Forward to next `#ifdef`/`}` at same level |
| `C-M-p` | Backward to matching `{`/`#ifdef` |
| `C-M-u` | Up one level of nesting |
| `C-M-d` | Down one level of nesting |

### Indentation

| Keys | What it does |
|------|-------------|
| `<TAB>` | Indent current line |
| `C-M-\` | Indent region |
| `M-q` | Fill/rewrap current comment |
| `C-c C-q` | Indent current top-level definition |

**Set your style in init.el:**
```elisp
(setq c-default-style "linux"   ; or "k&r", "gnu", "bsd", "stroustrup"
      c-basic-offset 4)
```

### Commenting

| Keys | What it does |
|------|-------------|
| `M-;` | Insert or align comment |
| `C-x C-;` | Comment or uncomment line |
| `M-x comment-region` | Comment out a region |
| `C-u M-x comment-region` | Uncomment a region |

---

## Tags and Code Navigation

### Generate Tags

```bash
# In project root (in eshell or term):

# GNU etags (Emacs native format):
find . -name "*.c" -o -name "*.h" | xargs etags

# Universal ctags (more languages, same format):
ctags -R --languages=C,C++ .

# Add to Makefile:
# tags:
#     find . -name "*.c" -o -name "*.h" | xargs etags
```

### Use Tags

| Keys | What it does |
|------|-------------|
| `M-.` | Jump to definition of symbol at point |
| `M-,` | Return to where you jumped from |
| `C-x 4 .` | Jump to definition in other window |
| `M-x tags-search` | Search through all tagged files |
| `M-x tags-query-replace` | Find/replace across all tagged files |
| `M-x tags-apropos` | List all tags matching pattern |
| `M-x visit-tags-table` | Load a specific TAGS file |

### With LSP/Eglot (clangd) — Better than Tags

If you have `clangd` installed:

```elisp
;; In init.el:
(use-package eglot
  :hook (c-mode . eglot-ensure))
```

Generate `compile_commands.json` for clangd:
```bash
# With Bear:
bear -- make

# With CMake:
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON .

# With compiledb:
pip install compiledb
compiledb make
```

Eglot/LSP keybindings:
| Keys | What it does |
|------|-------------|
| `M-.` | Go to definition |
| `M-?` | Find all references |
| `C-c C-r` | Rename symbol |
| `C-c C-a` | Code actions (fix suggestions) |
| `M-x eglot-format` | Format code |

---

## Compile and Build

```
M-x compile         # first time — set your command
M-x recompile       # subsequent times (or <F5>)
C-x `               # next error
M-g n               # next error (alternative)
M-g p               # previous error
```

**Smart compile command in init.el:**
```elisp
(defun my-compile ()
  "Compile from project root if Makefile found, else compile file directly."
  (interactive)
  (let ((makefile-dir (locate-dominating-file buffer-file-name "Makefile")))
    (if makefile-dir
        (compile (concat "make -k -C " makefile-dir))
      (compile (concat "gcc -Wall -Wextra -g -o "
                       (file-name-sans-extension buffer-file-name)
                       " "
                       buffer-file-name)))))

(global-set-key (kbd "<f5>") 'my-compile)
```

---

## GDB Integration

```
M-x gdb                 # start GDB (prompts: gdb -i=mi /path/to/binary)
M-x gdb-many-windows    # switch to full debugger layout
```

### GDB Commands (in the GDB buffer)

```gdb
run [args]              -- start program
run < input.txt         -- run with redirected input
break filename.c:42     -- breakpoint at line
break function_name     -- breakpoint at function
break function if x>0   -- conditional breakpoint
info breakpoints        -- list breakpoints
delete 1                -- delete breakpoint 1
next    (n)             -- step over
step    (s)             -- step into function
finish                  -- run until current function returns
continue  (c)           -- continue execution
print expr              -- evaluate and print expression
print *ptr              -- dereference and print pointer
print arr[0]@10         -- print 10 elements of array
display variable        -- auto-print variable each step
undisplay 1             -- stop auto-display #1
backtrace  (bt)         -- show call stack
frame 2                 -- switch to stack frame 2
info locals             -- print all local variables
info args               -- print function arguments
watch variable          -- watchpoint: stop when variable changes
set variable = value    -- change variable value
quit
```

### GDB Emacs UI Keys

In source buffer during GDB session:
| Keys | What it does |
|------|-------------|
| `C-x C-a C-b` | Set breakpoint at current line |
| `C-x C-a C-d` | Remove breakpoint |
| `C-x C-a C-r` | Continue |
| `C-x C-a C-n` | Next (step over) |
| `C-x C-a C-s` | Step (step into) |
| `C-x C-a C-f` | Finish function |
| Click on line number | Set/remove breakpoint |

---

## Header Files and Switching

```
M-x ff-find-other-file      # switch between foo.c and foo.h
M-x ff-find-other-file-other-window  # in split window
```

Add to init.el for keybinding:
```elisp
(add-hook 'c-mode-hook
          (lambda ()
            (local-set-key (kbd "C-c o") 'ff-find-other-file)))
```

---

## Useful C-Mode Settings

```elisp
;; In init.el:

;; Highlight TODO/FIXME/HACK in comments
(use-package hl-todo
  :hook (c-mode . hl-todo-mode))

;; Auto-insert header guard on new .h files
(define-auto-insert
  '("\\.h\\'" . "C header")
  '(nil
    "#ifndef " (upcase (file-name-nondirectory (file-name-sans-extension buffer-file-name))) "_H\n"
    "#define " (upcase (file-name-nondirectory (file-name-sans-extension buffer-file-name))) "_H\n"
    "\n"
    _
    "\n"
    "#endif /* " (upcase (file-name-nondirectory (file-name-sans-extension buffer-file-name))) "_H */\n"))

;; Eldoc for C — shows function signature in minibuffer
(add-hook 'c-mode-hook 'eldoc-mode)
```

---

## C Symbol Reference

For touch-typing practice, these are the C-specific symbols that need drilling:

| Symbol | Finger | Notes |
|--------|--------|-------|
| `{` | Right pinky + Shift + `[` | Block open |
| `}` | Right pinky + Shift + `]` | Block close |
| `[` | Right pinky stretch right | Array open |
| `]` | Right pinky far right | Array close |
| `(` | Right ring + Shift | Paren open |
| `)` | Right pinky + Shift | Paren close |
| `*` | Right middle + Shift + 8 | Pointer/deref/multiply |
| `&` | Right index + Shift + 7 | Address-of/bitwise AND |
| `->` | Right pinky `-`, right ring `>` (Shift+`.`) | Member via pointer |
| `!=` | Right index Shift+1, right pinky `=` | Not equal |
| `==` | Right pinky `=` twice | Equal comparison |
| `<=` | Right ring Shift+`,`, right pinky `=` | Less or equal |
| `>=` | Right ring Shift+`.`, right pinky `=` | Greater or equal |
| `<<` | Right ring Shift+`,` twice | Left shift |
| `>>` | Right ring Shift+`.` twice | Right shift |
| `\|` | Right pinky Shift+`\\` | Bitwise OR |
| `&&` | Right index Shift+7 twice | Logical AND |
| `\|\|` | Right pinky Shift+`\\` twice | Logical OR |
| `#` | Left middle Shift+3 | Preprocessor |
| `;` | Right pinky (home row!) | Statement end |
| `:` | Right pinky Shift+`;` | Ternary/label/bitfield |

See `c-symbols-typing.md` for dedicated drills on these.
