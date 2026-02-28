# Emacs Survival Cheatsheet

> Print this. Keep it next to your keyboard until you don't need it.

## The Escape Hatch
| Keys | What it does |
|------|-------------|
| `C-g` | **Cancel anything. Mash this when lost.** |

## Open / Save / Quit
| Keys | What it does |
|------|-------------|
| `C-x C-f` | Open file (creates if doesn't exist) |
| `C-x C-s` | Save current buffer |
| `C-x C-w` | Save as (write to new filename) |
| `C-x s` | Save all modified buffers |
| `C-x C-c` | Quit Emacs |
| `C-z` | Suspend (go back to shell, return with `fg`) |

## Moving Around
| Keys | What it does |
|------|-------------|
| `C-f` / `C-b` | Forward / backward one character |
| `C-n` / `C-p` | Next / previous line |
| `M-f` / `M-b` | Forward / backward one word |
| `C-a` / `C-e` | Beginning / end of line |
| `M-a` / `M-e` | Beginning / end of sentence |
| `C-v` / `M-v` | Scroll down / up one screen |
| `M-<` / `M->` | Beginning / end of buffer |
| `M-g g` | Go to line number |
| `C-l` | Center screen on cursor |

## Editing
| Keys | What it does |
|------|-------------|
| `C-d` | Delete character forward |
| `<DEL>` | Delete character backward |
| `M-d` | Delete word forward |
| `M-<DEL>` | Delete word backward |
| `C-k` | Kill (cut) to end of line |
| `C-/` | Undo |
| `C-x u` | Undo (alternate) |

## Cut / Copy / Paste (Kill / Yank)
| Keys | What it does |
|------|-------------|
| `C-<SPC>` | Set mark (start selection) |
| `C-w` | Kill (cut) region |
| `M-w` | Copy region |
| `C-y` | Yank (paste) |
| `M-y` | Cycle through kill ring after `C-y` |
| `C-x h` | Select entire buffer |

## Search
| Keys | What it does |
|------|-------------|
| `C-s` | Incremental search forward |
| `C-r` | Incremental search backward |
| `M-%` | Query replace |
| `C-M-%` | Query replace with regexp |
| `M-x occur` | List all matching lines in buffer |

## Buffers
| Keys | What it does |
|------|-------------|
| `C-x b` | Switch to buffer |
| `C-x C-b` | List all buffers |
| `C-x k` | Kill (close) buffer |
| `C-x <right>` | Next buffer |
| `C-x <left>` | Previous buffer |

## Windows (Splits)
| Keys | What it does |
|------|-------------|
| `C-x 2` | Split horizontally (top/bottom) |
| `C-x 3` | Split vertically (left/right) |
| `C-x o` | Switch to other window |
| `C-x 1` | Maximize this window (close others) |
| `C-x 0` | Close this window |
| `M-o` | Other window (if bound in init.el) |

## Help
| Keys | What it does |
|------|-------------|
| `C-h k <key>` | What does this key do? |
| `C-h f` | Describe a function |
| `C-h v` | Describe a variable |
| `C-h a` | Search for commands by word |
| `C-h t` | Open the Emacs tutorial |
| `C-h ?` | Help about help |

## The Minibuffer (bottom input area)
| Keys | What it does |
|------|-------------|
| `<TAB>` | Complete partial input |
| `<RET>` | Confirm |
| `C-g` | Cancel and exit |

---

## M-x: Run Any Command by Name

When you don't know the keybinding, `M-x` lets you type the command name:

```
M-x replace-string
M-x goto-line
M-x sort-lines
M-x whitespace-cleanup
M-x rename-buffer
```

With `which-key` installed (from starter init.el), pressing `C-x` and pausing 1 second shows all `C-x ...` keybindings available. Use this constantly.

---

## Key Notation Reference

| Notation | Meaning |
|----------|---------|
| `C-x` | Hold Ctrl, press x |
| `M-x` | Hold Meta/Alt, press x (or press Esc, then x) |
| `C-M-x` | Hold Ctrl and Meta, press x |
| `S-x` | Hold Shift, press x |
| `<SPC>` | Spacebar |
| `<DEL>` | Backspace |
| `<RET>` | Enter/Return |
| `<TAB>` | Tab |
