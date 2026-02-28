# Starter init.el

Copy this to `~/.emacs.d/init.el` before starting week 1.
It's conservative — just enough to make Emacs comfortable without overwhelming you.
You'll grow it throughout the 90 days.

---

## The File

Save this as `~/.emacs.d/init.el`:

```elisp
;;; init.el --- Starter configuration for touch-typing + Emacs journey
;;; Grow this file as you progress through the 90-day plan.

;;; ============================================================
;;; PACKAGE MANAGEMENT
;;; ============================================================

(require 'package)
(add-to-list 'package-archives
             '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)

;; Bootstrap use-package for cleaner package declarations
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))
(require 'use-package)
(setq use-package-always-ensure t)  ; auto-install packages

;;; ============================================================
;;; WEEK 1 BASICS: Start with these
;;; ============================================================

;; No startup screen — go straight to editing
(setq inhibit-startup-screen t)

;; No annoying bell sound
(setq ring-bell-function 'ignore)

;; Show line and column numbers in the mode line
(line-number-mode 1)
(column-number-mode 1)

;; Show matching parentheses
(show-paren-mode 1)

;; Don't create backup~ files everywhere (use version control instead)
;; If you want backups, comment out these lines
(setq make-backup-files nil)
(setq auto-save-default t)   ; DO keep auto-save (#file#) — it saves you

;; Larger font if you want it (adjust size to your preference)
;; (set-face-attribute 'default nil :height 140)  ; uncomment and adjust

;;; ============================================================
;;; WEEK 1-2: which-key (shows available key completions)
;;; ============================================================

(use-package which-key
  :config
  (which-key-mode 1)
  (setq which-key-idle-delay 1.0))  ; show after 1 second pause

;;; ============================================================
;;; WEEK 3-4: Better UI settings
;;; ============================================================

;; Uncomment these after week 2:

;; Show line numbers in programming files
;; (add-hook 'prog-mode-hook 'display-line-numbers-mode)

;; Highlight current line
;; (global-hl-line-mode 1)

;; Remember where you were in files
;; (save-place-mode 1)

;; Better buffer names when multiple files have same name
;; (require 'uniquify)
;; (setq uniquify-buffer-name-style 'forward)

;;; ============================================================
;;; WEEK 5-6: C programming basics
;;; ============================================================

;; Uncomment after week 4:

;; (setq c-default-style "linux"   ; change to: k&r, gnu, bsd, stroustrup
;;       c-basic-offset 4)

;; (add-hook 'c-mode-hook
;;           (lambda ()
;;             ;; Switch between .c and .h files
;;             (local-set-key (kbd "C-c o") 'ff-find-other-file)))

;;; ============================================================
;;; WEEK 7-8: Compile shortcuts
;;; ============================================================

;; Uncomment after week 6:

;; (global-set-key (kbd "<f5>") 'recompile)
;; (global-set-key (kbd "<f6>") 'next-error)
;; (global-set-key (kbd "<f7>") 'previous-error)
;; (global-set-key (kbd "C-c s") 'eshell)

;; Scroll compile output automatically
;; (setq compilation-scroll-output t)

;; Don't ask to save before compiling
;; (setq compilation-ask-about-save nil)

;;; ============================================================
;;; WEEK 8-9: Magit
;;; ============================================================

;; Uncomment after week 7:

;; (use-package magit
;;   :bind ("C-x g" . magit-status))

;;; ============================================================
;;; WEEK 9-10: Completion
;;; ============================================================

;; Company (code completion)
;; Uncomment after week 8:

;; (use-package company
;;   :hook (prog-mode . company-mode)
;;   :config
;;   (setq company-idle-delay 0.3
;;         company-minimum-prefix-length 2))

;; Ivy + Counsel (better minibuffer completion)
;; Uncomment after week 8:

;; (use-package ivy
;;   :config
;;   (ivy-mode 1))

;; (use-package counsel
;;   :config
;;   (counsel-mode 1)
;;   :bind
;;   (("M-x"     . counsel-M-x)
;;    ("C-x C-f" . counsel-find-file)
;;    ("C-x b"   . ivy-switch-buffer)))

;;; ============================================================
;;; WEEK 10+: Advanced options (add as you need them)
;;; ============================================================

;; Quick window switching without C-x o
;; (global-set-key (kbd "M-o") 'other-window)

;; Delete trailing whitespace on save
;; (add-hook 'before-save-hook 'delete-trailing-whitespace)

;; Use spaces not tabs
;; (setq-default indent-tabs-mode nil)
;; (setq-default tab-width 4)

;; Eglot (LSP — needs clangd installed)
;; (use-package eglot
;;   :hook (c-mode . eglot-ensure))

;; Ripgrep
;; (use-package rg
;;   :bind ("C-c r" . rg-dwim))

;;; ============================================================
;;; CUSTOM (added by Emacs automatically — don't edit by hand)
;;; ============================================================

(custom-set-variables)
(custom-set-faces)

;;; init.el ends here
```

---

## How to Use This File

1. **Week 1:** Use as-is. Only `which-key` is active beyond the basics.
2. **Week 3:** Uncomment the "Week 3-4" section.
3. **Week 5:** Uncomment the C programming section.
4. **Week 7:** Uncomment compile shortcuts.
5. **Week 8:** Uncomment Magit.
6. **Week 9:** Uncomment Company and Ivy/Counsel.

**Don't uncomment everything at once.** Each new feature is a new thing to learn. Add them one section at a time, at the pace of the weekly plan.

---

## Loading Changes

After editing `init.el`, apply changes without restarting:
```
M-x eval-buffer     (when init.el is the current buffer)
```
Or restart Emacs completely. If something breaks, `C-g` or revert to last working version.

---

## Troubleshooting init.el

**"Package not found"**  
```
M-x package-refresh-contents
M-x package-install which-key
```

**"Wrong type argument" or other errors on startup**  
Check the `*Messages*` buffer (`C-x b *Messages*`) for error details.

**Start fresh**  
```bash
mv ~/.emacs.d ~/.emacs.d.bak
# Then restart Emacs and create a new init.el
```

**Debug mode**  
```bash
emacs --debug-init
```
This shows detailed errors on startup.
