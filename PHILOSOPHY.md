# Philosophy: Why This Approach Works

This plan has a specific origin. I built [Skred](https://github.com/octetta/skred), a polyphonic wavetable synthesizer controlled via terse ASCII commands over UDP. Someone asked whether Skred supported live coding via Emacs. That question prompted a session vibe-coding [`skred-emacs-mode`](https://github.com/octetta/skred-emacs-mode) with Claude — a proper major mode with syntax highlighting, persistent UDP client, and inline result overlays. Around the same time, a conversation on jart's Discord touched on Emacs, touch typing, and handedness.

The combination made something click: I'd been circling Emacs for years, and the obstacle wasn't Emacs — it was that hunt-and-peck typing and complex key chords are a genuinely bad combination. You can't build muscle memory for `C-M-f` when you're looking at the keyboard to find `f`. The plan in this repo came out of describing exactly that problem to Claude in a single conversation. The meta-story matches the thesis: building a synthesizer mode in one vibe-coding session is an argument for learning the tools that let you think faster.

## The Vi User's Specific Situation

Coming from vi (or vim, or vile) adds a wrinkle. You have decades of modal editing muscle memory. The question everyone asks is: why not Evil mode?

Because Evil mode solves a different problem. It's for vi users who want Emacs's power *without* changing their habits. This plan is for someone who has decided to change their habits. Routing everything through Evil means you're learning vi-in-Emacs rather than Emacs — and you'd be building three sets of muscle memory (vi modal, Emacs chords, touch typing) instead of two.

The vi background is also genuinely useful: you already understand that an editor can be an entire environment, that deep keyboard investment pays off, that text is manipulable at a structural level. Those intuitions transfer. The `hjkl` reflexes don't — but they fade faster than you expect when you're using the real thing daily.

See `VI-USERS.md` for a full treatment, including a translation table of vi habits to Emacs equivalents.

## The Acme Insight: Text as Command Surface

Rob Pike's Acme editor from Plan 9 had a radical idea: the editor and the shell share the same surface. Any text can be a command. Any output is text you can act on further. There's no meaningful distinction between "the editor" and "the environment."

Emacs doesn't go quite that far architecturally, but it gets surprisingly close — and closer than almost any other editor available today. The key primitives:

- `M-!` runs a shell command and drops the output into a buffer. This is Acme's middle-click on a command string.
- `M-|` pipes the selected region through a command and replaces it with the output. Select a messy block of C, pipe through `clang-format`, done. This is Acme's pipe operation.
- Eshell's `> #<buffer>` redirect sends command output to a named Emacs buffer you can then edit, search, or act on as text.
- Org-mode's `#+BEGIN_SRC` blocks let you execute code inline and capture output in the document — closer to Acme's model than most people realize.

The philosophical point: in Emacs, output is never just output. It's text in a buffer, which means it's subject to everything Emacs can do to text — search, edit, pipe onward, commit to version control. Once this clicks, the shell integration stops feeling like a convenience feature and starts feeling like the point.

See `cheatsheets/emacs-shell.md` for the full Acme-inspired workflow.

## The Real Argument for Touch Typing

Most people frame the case for touch typing around speed and accuracy. Those matter, but they're not the strongest argument.

The strongest argument is **cognitive load**.

Think about learning to drive. The first time, everything demands conscious attention — mirrors, pedals, steering, other cars, the rules. Turning the radio on feels dangerous. Years later, you drive long distances while holding a conversation, your hands and feet working without conscious direction. The mechanics became automatic, freeing your mind for everything else.

Touch typing works the same way. A hunt-and-pecker types by solving a constant stream of small problems: where is the `{`? Did I get that `->` right? Where's the semicolon? Each lookup is cheap, but they're continuous, and they occupy exactly the working memory you need for the actual problem you're solving — the logic, the design, the debugging.

When typing becomes automatic, it stops consuming that working memory. You think and type simultaneously rather than alternately. The keyboard becomes transparent in the same way a fluent reader doesn't see individual letters.

For programmers specifically, this matters more than WPM. You're not transcribing — you're composing. The cognitive tax of hunt-and-peck lands precisely when your mind is most loaded.

*Further reading: Hakan Serce's [Do Programmers Actually Need Touch Typing?](https://blog.hakanserce.com/post/do-programmers-actually-need-touch-typing/) makes this case well, including a concrete account of the moment it clicked for him.*

## The "Two Lives" Reality

The guide asks you to use Emacs for real work starting week one. That's the right long-term approach, but be honest with yourself about the short term: if you have deadlines, interviews, or any situation where your typing speed is load-bearing for someone else, you may need to keep hunt-and-peck available during working hours while practicing touch typing outside them.

This isn't failure. It's a practical bridge. Hakan Serce describes it as "living two lives" — hunt-and-pecker during the day, touch typist in the evenings — until touch typing reaches roughly two-thirds of your hunt-and-peck speed. At that point you make the switch permanently.

The switch is easier if you make it physically hard to revert. A cloth over your hands works for practice sessions. For the permanent switch, blank keyboard stickers (available cheaply online) are more committed — you can still feel the keys, but visual lookup becomes impossible. Some people start by blanking letters only, then add symbols as their muscle memory solidifies.

The key insight: don't try to suppress the hunt-and-peck reflex through willpower during your working day. Let it serve you there while you build the replacement. The new habit accumulates in dedicated practice, then overtakes the old one through sheer repetition.

## The Hunt-and-Peck Expert Problem

You've been programming for decades. You're fast — maybe faster than you think — at finding keys visually. Your brain has deep shortcuts: you know where `;` is, where `->` lives, where `{` hides. This is the problem.

Unlearning is harder than learning. You can't gradually shift. You have to break the loop.

The standard advice — "just use a typing tutor for 30 days" — fails experienced programmers because:

- The drills feel childish and disconnected from real work
- You're practicing "fjfjfj" when you actually type `malloc(sizeof(struct node))`
- After the tutor session you go right back to hunting
- The context switch is too expensive when you're in flow

## The Emacs Insight

Emacs is actually the perfect typing teacher for programmers, because:

**Emacs punishes not touching the home row.** The most common operations — `C-f`, `C-b`, `C-n`, `C-p`, `C-a`, `C-e`, `M-f`, `M-b` — are all home row or near-home keys. If you're looking at your keyboard for these, you're also losing your place on screen. The visual penalty reinforces the behavior.

**Emacs keybindings are muscle memory training.** `C-x C-s` (save) becomes as automatic as breathing within two weeks. That's the same mechanism as touch typing. You're not learning two different things — you're learning one thing: keyboard muscle memory.

**Emacs rewards deep investment.** Unlike VS Code or other editors where the ceiling is modest, Emacs mastery compounds. Every new keybinding you learn accelerates learning the next one. Same with touch typing.

## Why Simultaneous Learning Works

The risk of learning them separately:

- Learn touch typing first → relearn Emacs shortcuts with new finger positions
- Learn Emacs first → speed-limited and frustrated, hunt-and-peck through complex operations

Learning them together:
- Emacs chords are your touch typing drills
- Touch typing enforces correct Emacs technique
- You only build one set of muscle memories
- Real work motivation keeps you practicing

## The C Programmer Advantage

C code has a rich symbol vocabulary: `->`, `*`, `&`, `{`, `}`, `(`, `)`, `[`, `]`, `#`, `//`, `/*`, `*/`. These are exactly the symbols that typing tutors neglect and that you've spent years hunting for visually.

By week 7 of this plan, you'll be drilling these symbols explicitly in the context of real C patterns. Your hands will find `struct foo *bar = malloc(sizeof(*bar));` without a glance.

## The Laptop Variation Problem

Multiple laptops mean inconsistent keyboard feel, key placement, and sometimes layout. This sounds like a liability. It's actually an asset:

- You can't rely on visual confirmation since layouts differ
- You're forced into truly position-independent memory (relative to hand placement)
- You learn to adapt, which is the real skill

The one universal fix: **Caps Lock → Ctrl**. Do it on every machine. Non-negotiable.

## On Age

61 is not a disadvantage. Here's what's actually true:

- Motor learning takes longer in older adults, but the difference is smaller than people think
- Deliberate practice quality matters more than age
- You have decades of programming context that makes Emacs semantics immediately meaningful
- Motivation and discipline, at which experienced people typically excel, matter enormously

Expect 90 days, not 30. Plan for it. The outcome is the same.

## The Daily Practice Structure

Each day should have three components, totaling 20–40 minutes:

1. **Typing drill (10 min):** Structured exercise from the week's plan. Use a tutor tool.
2. **Emacs exercise (10 min):** Deliberate practice of that week's Emacs focus.  
3. **Real work (rest of the time):** Use Emacs for actual work, as awkward as it is.

The real work component is essential. Drills without application don't stick.
