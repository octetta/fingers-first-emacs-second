# A Note for Vi/Vim/Vile Users

## You Are Not the Target of Evil Mode

If you've arrived here with years of vi muscle memory — `hjkl`, `:wq`, modal editing baked into your fingers at a cellular level — the first question everyone will ask you is: *"Why not just use Evil mode?"*

Evil mode is a remarkably complete vi emulation layer for Emacs. It's well-built and many people use it happily. It is not what this plan is about, and this document explains why that's a deliberate choice rather than an oversight.

---

## Why Not Evil Mode

The goal of this plan is to build **one clean set of muscle memories**, not to maintain two sets in parallel.

Evil mode solves a different problem than the one we're solving here. It's for vi users who want Emacs's extensibility and ecosystem while preserving their existing motor habits. That's a completely legitimate goal. But it's not this goal.

This plan starts from a specific premise: you've decided to learn Emacs *as Emacs*. Not as a vi host. If you route all your editing through Evil, you're not learning Emacs — you're using vi with a more complicated substrate and occasional confusion about which mode you're in and why `C-x C-s` isn't working.

More concretely: this plan teaches touch typing and Emacs keybindings *simultaneously*, because they reinforce each other. Emacs chords live on the home row. They build the same muscle memory that touch typing builds. Evil mode breaks that pairing — you'd be building vi modal memory and touch typing memory, with Emacs chords as a third thing you occasionally need. That's worse, not better.

---

## Your Vi Background: Asset and Liability

Be honest with yourself about both sides.

**Asset:**
- You already understand that an editor can be an entire environment, not just a text box
- You're comfortable with the idea of deep keyboard investment paying off over time
- You think in terms of text objects and operations, which maps well to Emacs's region/kill/yank model
- You have zero illusions about the learning curve — you've been through one before

**Liability:**
- `hjkl` will fire involuntarily, especially when you're tired or under deadline pressure
- `:w` and `:wq` are so deeply encoded they may appear in Emacs buffers for weeks
- The concept of *modes* (insert vs normal vs visual) is deeply grooved — Emacs's modeless editing will feel wrong at first
- `u` for undo in vi conflicts with Emacs's `C-/` — your fingers will argue about this

None of these liabilities are disqualifying. They're just things to expect. When `:w` appears in your C file, don't be frustrated — note which situation triggered it and move on. The old reflexes fade, but they fade on their own schedule.

---

## The Specific Conflicts to Watch For

| Vi habit | What happens in Emacs | Emacs equivalent |
|----------|----------------------|-----------------|
| `hjkl` navigation | Types literal h, j, k, l | `C-f`, `C-b`, `C-n`, `C-p` |
| `:w` | Opens M-x prompt partially, then types `w` | `C-x C-s` |
| `:q` | Same confusion | `C-x C-c` |
| `u` undo | Inserts literal `u` (you're not in normal mode) | `C-/` or `C-x u` |
| `dd` delete line | Types `dd` | `C-k` (kill to end of line) or `C-a C-k` |
| `yy` yank line | Types `yy` | `C-a C-<SPC> C-n M-w` |
| `/` search | Types `/` | `C-s` |
| `o` open line below | Types `o` | `C-e <RET>` |
| `G` end of file | Types `G` | `M->` |
| `gg` start of file | Types `gg` | `M-<` |
| `w` / `b` word motion | Types `w` or `b` | `M-f` / `M-b` |
| `%` jump to match | Types `%` | `C-M-f` / `C-M-b` (over expressions) |

Keep this table handy for the first few weeks. When you catch yourself typing a vi command into a buffer, look it up here.

---

## What Vi Intuitions Transfer Well

Not everything fights you. Some vi thinking maps cleanly:

- **The kill ring** is richer than vi's registers but the concept is similar — you cut things and retrieve them
- **Marks** in Emacs (`C-x r <space> <letter>`) are conceptually close to vi marks
- **Macros** (`C-x (`, `C-x )`, `C-x e`) work similarly to vi's `q<letter>` / `@<letter>`
- **`:!command`** in vi (run a shell command) maps to `M-!` in Emacs — same idea, different syntax
- **`:r !command`** (insert command output) maps to `C-u M-!` — again, same idea

That last pair is worth dwelling on. If you used vi's `:!` and `:r !` regularly, you'll feel immediately at home with Emacs's `M-!` and `M-|`. See `cheatsheets/emacs-shell.md` for the full treatment.

---

## A Practical Suggestion

For the first two weeks, when you catch yourself in a vi reflex, do this:

1. Notice it without frustration
2. `C-g` to cancel anything weird that happened
3. Do the Emacs equivalent from memory (or look it up above)
4. Move on

Don't try to suppress the vi reflex through willpower — that doesn't work. Just build the Emacs reflex alongside it through repetition, and let time sort out which one wins. Given that you'll be using Emacs for real work starting week one, the Emacs reflex will accumulate practice faster than you expect.

The vi habits won't disappear. They'll recede. After a few months you'll find you can still use vi fluently when you need to (editing a file over SSH on a machine with no Emacs, for instance) without it interfering with your Emacs work. The two can coexist — you just don't want them fighting during the learning phase, which is exactly what Evil mode would cause.
