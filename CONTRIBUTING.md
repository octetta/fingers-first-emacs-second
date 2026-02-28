# Setting Up This as a GitHub Repo

## Quick Start

```bash
# Clone this repo (once it's on GitHub):
git clone https://github.com/YOUR_USERNAME/fingers-first-emacs-second.git
cd fingers-first-emacs-second

# Or initialize your own from scratch:
git init
git add .
git commit -m "Initial commit: 90-day touch typing + emacs plan"
git remote add origin https://github.com/YOUR_USERNAME/touch-type-emacs-journey.git
git push -u origin main
```

## Personalizing Your Fork

After forking or cloning, make it yours:

1. **Update `resources/daily-log-template.md`** — copy it to `my-log.md` and fill in your actual dates and WPM scores. Don't commit this to a public fork unless you want to share your progress.

2. **Add to `.gitignore`:**
   ```
   my-log.md
   my-init.el
   *.elc
   ```

3. **Customize the weekly plans** — if a phase takes you longer, rename `week-01-02.md` to `week-01-03.md` or just add notes to the existing files.

## Sharing Progress

If you want to track publicly:
- Update your WPM table in `resources/daily-log-template.md` weekly
- Tag milestones: `git tag -a v0.week4 -m "Week 4 complete: 22 WPM"`

## Contributing Back

If you find something that works better, fixes an error, or adds useful content:
1. Fork the repo
2. Make your changes on a branch
3. Open a pull request with a clear description

Especially welcome:
- Corrections to keybinding descriptions
- Better typing drills for specific weak spots  
- Cheatsheets for other languages (Rust, Python, etc.)
- Reports: "At week X I found that Y was actually harder than the plan expected"
