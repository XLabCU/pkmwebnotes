---
title: Guide for Instructors
created: 2025-08-05T12:57:24.739Z
tags: [welcome]
---
Guide goes here.


## Customize with your own materials

Don't just take our word for it: see something you wish was different, just take a copy, modify the markdown in the `content` folder, and deploy in your own webspace!

- Fork the repo at [https://github.com/shawngraham/pkmwebnotes](https://github.com/shawngraham/pkmwebnotes)
- Add more content as .md files in the `content` subfolder
- Add the filenames to the `content/manifest.json` file (note that you can have subfolders), eg:

```json
{
  "defaultNotes": {
    "root": ["welcome.md", "custom-deploy.md"],
    "python": ["python-in-notes.md", "python-examples.md"],
    "projects": {
      "work": ["project1.md", "project2.md"],
      "personal": ["ideas.md"]
    }
  }
}
```

- Use GH-Pages or Netlify Drop or another webhosting service to push it online (no builds, no Jekyll, etc etc!)

## Colophon

Built through a combination of low cunning, guile, careful specification of structure, needs, and wants, and prompting and testing for one function at a time (with Claude & Gemini).
