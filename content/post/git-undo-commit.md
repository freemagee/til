---
title: "Undo a Git commit and redo"
date: 2019-01-03T14:52:45+00:00
categories:
  - development
tags:
  - git
---

Today I learned how to sort out a **local** mistake in Git<sup>1</sup>:

```
$ git commit -m "Something terribly misguided"             # (1)
$ git reset HEAD~                                          # (2)
<< edit files as necessary >>                              # (3)
$ git add ...                                              # (4)
$ git commit -c ORIG_HEAD                                  # (5)
```

1. This is what you want to undo
2. This leaves your working tree (the state of your files on disk) unchanged but undoes the commit and leaves the changes you committed unstaged (so they'll appear as "Changes not staged for commit" in `git status`, and you'll need to add them again before committing). If you only want to add more changes to the previous commit, or change the commit message<sup>2</sup>, you could use `git reset --soft HEAD~` instead, which is like `git reset HEAD~` (where `HEAD~` is the same as `HEAD~1`) but leaves your existing changes staged.
3. Make corrections to working tree files.
4. git add anything that you want to include in your new commit.
5. Commit the changes, reusing the old commit message. reset copied the old head to `.git/ORIG_HEAD`; `commit` with `-c ORIG_HEAD` will open an editor, which initially contains the log message from the old commit and allows you to edit it. If you do not need to edit the message, you could use the `-C` option.

<!--more-->

Apparently the tilde character `~` is used in this example as DOS has problems with carats `^` (The `^` is a continuation character in DOS so it won't work properly). Otherwise the statement would be `git reset HEAD^`.

I am normally pretty careful with my local Git operations. But sometimes using the up arrow to move through last used commands can cause premature committing!

--------

**Footnotes:**

1. This is cribbed from [Stack Overflow](https://stackoverflow.com/a/927386). I just wanted the reference 👍 
2. Note, however, that you don't need to reset to an earlier commit if you just made a mistake in your commit message. The easier option is to git reset (to upstage any changes you've made since) and then git commit --amend, which will open your default commit message editor pre-populated with the last commit message.


