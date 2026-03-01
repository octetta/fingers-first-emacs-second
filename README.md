# Fingers First, Emacs Second: A 90-Day Journey

> *For experienced programmers who hunt-and-peck their way through brilliance — and are ready to stop.*

This repo is a structured 90-day plan to simultaneously learn touch typing and Emacs, aimed at working programmers (particularly C developers) who live on the command line. It's built for people who:

- Have been programming for years (or decades) without touch typing
- Have tried Emacs and bounced off the key chord complexity
- Suspect those two problems are actually the same problem
- Use multiple laptops with varying keyboard layouts
- Love the shell and want it *inside* their editor

**The core insight:** Learning touch typing and Emacs together isn't twice the work — it's actually easier. You're building new muscle memory from scratch either way. Emacs key chords *become* your touch typing practice.

---

## Repo Structure

```
.
├── README.md                     ← You are here
├── PHILOSOPHY.md                 ← Why this approach works
├── VI-USERS.md                   ← For vi/vim/vile users: why not Evil mode
├── weeks/
│   ├── week-01-02.md            ← Home row, Dired & Emacs survival
│   ├── week-03-04.md            ← Top row, Magit & basic editing
│   ├── week-05-06.md            ← Bottom row & buffers/windows
│   ├── week-07-08.md            ← Numbers, symbols & shell/eshell
│   ├── week-09-10.md            ← C symbols deep dive & org-mode
│   ├── week-11-12.md            ← Speed building & advanced Emacs
│   └── week-13.md               ← Integration & your init.el
├── cheatsheets/
│   ├── emacs-survival.md        ← The 20 commands you need day 1
│   ├── emacs-shell.md           ← Shell, eshell, Acme-style text/command surface
│   ├── emacs-c-programming.md   ← C mode, compile, debug, navigate
│   ├── c-symbols-typing.md      ← Drilling {, }, *, ->, :: and friends
│   └── keyboard-variations.md   ← Surviving laptop keyboard chaos
└── resources/
    ├── tools.md                 ← Software recommendations
    ├── init-el-starter.md       ← A minimal init.el to get going
    └── daily-log-template.md    ← Track your WPM and Emacs wins
```

---

## The 90-Day Overview

| Phase | Weeks | Typing Focus | Emacs Focus |
|-------|-------|-------------|-------------|
| **Survive** | 1–2 | Home row only | Open, edit, save, quit |
| **Expand** | 3–4 | Top row | Navigation, search |
| **Build** | 5–6 | Bottom row | Buffers, windows, dired |
| **Code** | 7–8 | Numbers & symbols | Shell, eshell, compile |
| **Specialize** | 9–10 | C symbols | C-mode, GDB, magit |
| **Accelerate** | 11–12 | Speed drills | Macros, org, registers |
| **Integrate** | 13 | Maintenance | Your personal init.el |

---

## Ground Rules

1. **No looking at the keyboard.** Put a cloth over it if you have to. Seriously.
2. **Slow is smooth. Smooth is fast.** Type at the speed where you don't make errors.
3. **Use Emacs for real work starting week 1.** Not just drills.
4. **15–30 minutes of deliberate practice daily beats 3-hour weekend sessions.**
5. **Track your WPM weekly.** You'll be shocked by week 6.
6. **Your pinky fingers are weak. That's okay. Emacs will build them.**

---

## Quick Start

If you want to jump in today:

1. Read `cheatsheets/emacs-survival.md`
2. Install Emacs (see `resources/tools.md`)
3. Copy `resources/init-el-starter.md` as your `~/.emacs.d/init.el`
4. Start `week-01-02.md`
5. Log your starting WPM in `resources/daily-log-template.md`

---

## A Note on Keyboard Variations

Laptop keyboards vary in annoying ways — function key rows, Ctrl placement, missing keys. See `cheatsheets/keyboard-variations.md` for strategies. The short version: **remap Caps Lock to Ctrl on every machine you use.** It takes 2 minutes and changes everything for Emacs.

---

## Origin

This plan has a specific origin story, which is worth telling because it might resonate with other people in similar situations.

I've been programming for decades — C, embedded systems, synthesizers — without ever learning to touch type. I built [Skred](https://github.com/octetta/skred), a polyphonic wavetable synthesizer controlled via terse ASCII commands over UDP. Someone asked whether Skred supported live coding via Emacs. That question prompted me to vibe-code [`skred-emacs-mode`](https://github.com/octetta/skred-emacs-mode) — a proper Emacs major mode with syntax highlighting, a persistent UDP client, and inline result overlays — using Claude as the coding partner.

Around the same time, a conversation on [jart's](https://github.com/jart) Discord server touched on Emacs, touch typing, and handedness. The combination of that conversation and the Skred live coding question made something click: I'd been circling Emacs for years and the thing stopping me wasn't Emacs — it was that hunt-and-peck typing and complex key chords are a bad combination. You can't build muscle memory for `C-M-f` when you're looking at the keyboard to find `f`.

The plan in this repo came out of a single conversation with Claude (an AI assistant made by Anthropic), describing exactly that problem. I'm doing it myself — follow the log below.

> *Vibe-coding a synthesizer mode in one session is a decent argument for learning the tools that let you think faster. This repo is the next step.*

## My Progress Log

*(Link your own fork or log file here once you start. If you're the original author, this is where you track your WPM week by week and share what's actually working.)*

---

## Contributing

If you find this useful, PRs welcome. Especially:
- Reports of what worked / didn't at specific weeks
- Additional cheatsheets (Rust, Python, etc.)
- Alternative exercises for specific weak spots
- Progress logs from people actually doing the plan

---

## Acknowledgments

This plan was shaped in conversation with [Claude](https://claude.ai), an AI assistant made by Anthropic. The ideas, structure, and drills came out of describing a real problem to a very patient interlocutor. If you want to build something similar for your own situation, that's a reasonable way to start.

---

*The keyboard is not your enemy. You just haven't been introduced properly.*
