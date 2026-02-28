# Week 13: Integration — Making Emacs Yours

## The Goal
By end of this week, Emacs is your editor. Not "the editor you're learning" — your editor. You have a personal `init.el` that reflects your workflow. You type without looking. You're faster than you were 90 days ago.

---

## Days 85–91

### Typing: Maintenance Mode

You don't need structured drills anymore. Maintenance means:

- Continue using proper touch technique every time you type
- Do a 1-minute WPM test once a week to track progress (it continues for months)
- When you catch yourself looking at the keyboard — stop, reset, find home row by feel
- Type C code from your real projects. That's your drill now.

**Expected WPM at day 90:**
- General text: 45–65 WPM (wide range depending on starting point)
- C code: 30–45 WPM
- You will continue improving for 6–12 more months without any extra effort

### Emacs: Build Your init.el

This week is about curating your Emacs configuration. Your `~/.emacs.d/init.el` should reflect *how you actually work*.

**Starting point — add these incrementally:**

```elisp
;;; init.el — My Emacs Configuration

;; Package setup
(require 'package)
(add-to-list 'package-archives 
             '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)

;; Bootstrap use-package
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))
(require 'use-package)
(setq use-package-always-ensure t)

;;; ============================================
;;; CORE BEHAVIOR
;;; ============================================

;; No startup screen
(setq inhibit-startup-screen t)

;; No bell
(setq ring-bell-function 'ignore)

;; Show matching parens
(show-paren-mode 1)

;; Line numbers in programming modes
(add-hook 'prog-mode-hook 'display-line-numbers-mode)

;; Highlight current line
(global-hl-line-mode 1)

;; Column number in modeline
(column-number-mode 1)

;; Auto-revert buffers when files change on disk
(global-auto-revert-mode 1)

;; Delete trailing whitespace on save
(add-hook 'before-save-hook 'delete-trailing-whitespace)

;; Use spaces not tabs (adjust per project)
(setq-default indent-tabs-mode nil)
(setq-default tab-width 4)

;; Better buffer names for duplicates
(require 'uniquify)
(setq uniquify-buffer-name-style 'forward)

;; Save minibuffer history
(savehist-mode 1)

;; Remember cursor position in files
(save-place-mode 1)

;;; ============================================
;;; KEYBINDINGS
;;; ============================================

;; Compile shortcuts
(global-set-key (kbd "<f5>") 'recompile)
(global-set-key (kbd "<f6>") 'next-error)
(global-set-key (kbd "<f7>") 'previous-error)

;; Magit
(global-set-key (kbd "C-x g") 'magit-status)

;; Faster window switching
(global-set-key (kbd "M-o") 'other-window)

;; Open eshell quickly
(global-set-key (kbd "C-c s") 'eshell)

;;; ============================================
;;; C PROGRAMMING
;;; ============================================

(use-package cc-mode
  :config
  (setq c-default-style "linux"    ; change to your preference
        c-basic-offset 4)
  
  ;; Auto-indent on newline
  (defun my-c-mode-hook ()
    (local-set-key (kbd "C-c C-c") 'compile)
    (setq compile-command 
          (concat "make -k -C " 
                  (locate-dominating-file buffer-file-name "Makefile"))))
  
  (add-hook 'c-mode-hook 'my-c-mode-hook))

;;; ============================================
;;; PACKAGES
;;; ============================================

;; Which-key: shows available keybindings
(use-package which-key
  :config (which-key-mode))

;; Magit: the best git interface
(use-package magit)

;; Company: completion
(use-package company
  :hook (prog-mode . company-mode)
  :config
  (setq company-idle-delay 0.2
        company-minimum-prefix-length 2))

;; Ivy/Counsel: better minibuffer completion
(use-package ivy
  :config
  (ivy-mode 1)
  (setq ivy-use-virtual-buffers t))

(use-package counsel
  :config
  (counsel-mode 1)
  :bind
  (("M-x" . counsel-M-x)
   ("C-x C-f" . counsel-find-file)
   ("C-x b" . ivy-switch-buffer)))

;; Ripgrep integration (if rg installed)
(use-package rg
  :bind ("C-c r" . rg-dwim))

;;; ============================================
;;; ESHELL CONFIGURATION
;;; ============================================

(defun my-eshell-setup ()
  ;; Custom prompt
  (setq eshell-prompt-function
        (lambda ()
          (concat
           (abbreviate-file-name (eshell/pwd))
           (if (= (user-uid) 0) " # " " $ "))))
  (setq eshell-prompt-regexp "^[^#$\n]*[#$] "))

(add-hook 'eshell-mode-hook 'my-eshell-setup)

;; Eshell alias file
(setq eshell-aliases-file "~/.emacs.d/eshell/aliases")
;; Create ~/.emacs.d/eshell/aliases with content like:
;;   alias ll ls -la $*
;;   alias gs magit-status

;;; ============================================
;;; ORG-MODE
;;; ============================================

(use-package org
  :config
  (setq org-directory "~/notes"
        org-default-notes-file "~/notes/inbox.org"
        org-todo-keywords '((sequence "TODO" "IN-PROGRESS" "WAITING" "|" "DONE" "CANCELLED")))
  
  (global-set-key (kbd "C-c c") 'org-capture)
  (global-set-key (kbd "C-c a") 'org-agenda)
  
  ;; Capture templates
  (setq org-capture-templates
        '(("t" "TODO" entry (file+headline "~/notes/inbox.org" "Tasks")
           "* TODO %?\n  %i\n  %a")
          ("n" "Note" entry (file+headline "~/notes/inbox.org" "Notes")
           "* %?\n  %i\n  %a"))))

;;; init.el ends here
```

### Days 85–88: Build your config incrementally

Don't copy this whole thing at once. Add sections one at a time:

1. Core behavior settings (no startup, line numbers, etc.)
2. Your keybindings
3. C-mode settings
4. `which-key`
5. `magit`
6. `company` (completion)
7. `ivy`/`counsel` (or `vertico`/`consult` — see note below)

**After each addition:** Restart Emacs (or `M-x eval-buffer`) and verify it works before adding more.

### Days 89–91: Personalize and reflect

Questions to answer with your config:

- What `compile-command` do I use most? (automate it)
- What directories do I navigate to constantly? (add bookmarks)
- What code patterns do I type repeatedly? (add snippets with `yasnippet`)
- What annoys me most about Emacs? (there's almost always a fix)

---

## What Comes Next

You've reached the end of the 90-day plan. Here's where to go from here:

### Touch Typing
- Keep tracking WPM weekly — you'll keep improving for 6+ more months
- Your target should be 60–80 WPM for sustained C code entry
- If you plateau, try new drills specifically on your weak keys (usually symbols)

### Emacs Depth
These are the next things worth learning, in rough priority order for a C programmer:

1. **`eglot` or `lsp-mode`** — Language Server Protocol for C, gives you IDE features (jump-to-definition across files, type checking, completion) using `clangd`
2. **`yasnippet`** — Template snippets for common patterns (`struct`, `for`, `malloc` pattern)
3. **`treesit`** (Emacs 29+) — Tree-sitter based syntax highlighting and navigation
4. **`projectile`** — Project-aware commands (search all project files, switch between projects)
5. **Advanced org-mode** — Babel for literate programming, agenda, time tracking
6. **Emacs Lisp basics** — Reading and writing simple elisp to customize further

### Community
- [r/emacs](https://reddit.com/r/emacs) — active community, lots of config sharing
- [Planet Emacsen](https://planet.emacsverse.org/) — Emacs blogs aggregator
- [Emacs Wiki](https://www.emacswiki.org/) — comprehensive reference

---

## Final WPM Log

| Week | WPM (General) | WPM (C Code) | Notes |
|------|--------------|-------------|-------|
| Start | | | Baseline |
| 2 | | | |
| 4 | | | |
| 6 | | | |
| 8 | | | |
| 10 | | | |
| 12 | | | |
| 13 | | | 90-day mark |

Fill this in. You'll be surprised how far you've come.

---

*You did it. Now go write some C.*
