# Philosophy: Why This Approach Works

This plan was built in a conversation with Claude (an AI assistant by Anthropic), which is itself a small illustration of the argument: when you can think and type faster, you can externalize and structure ideas faster. The meta-story matches the thesis.

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
