---
layout: post
title:  "Editing Markdown files with the help of Git"
date:   2014-03-30 20:39:00
categories: meta
---

[Markdown](http://daringfireball.net/projects/markdown/dingus) is a [lightweight markup language](http://blog.codinghorror.com/is-html-a-humane-markup-language/) for writing formatted, semantic content. Because it's so lightweight, it's also very easy to use.

It fell to me recently to edit a series of large documents that were stored in a [Git](http://git-scm.com/) repository. Using Git for storing text documents can seem daunting at first given the extra steps involved in [staging](https://www.kernel.org/pub/software/scm/git/docs/git-add.html), [committing](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html) and [pushing](http://git-scm.com/docs/git-push) your changes. Yet armed with knowledge of just some of Git's many commandline switches, it becomes really easy.

### Editing Markdown

I use [vim](http://www.vim.org/) for editing source code, and it's fairly well suited for editing Markdown. However, I also find it useful to see a visual preview to help me spot any formatting mistakes.

On Mac OS X, @samjsharpe introduced me to [Mou](http://mouapp.com/), which currently in beta and does the job nicely. I also use Windows, for which [Haroo Pad](http://pad.haroopress.com/) appears to be a promising alternative (and is also cross-platform).

### Commit your changes with patch mode

With your text editor open in one window and a console open ready for Git commands in the other, editing and pushing Markdown code is easy:

First edit the text, making related changes or amendments together (i.e. [atomic commits](http://www.freshconsulting.com/atomic-commits/)).

Once you've reached a logical place to commit, enter the following in your console:

```bash
git commit -vp
```

Note that this technique only works on files already checked in to a Git repository. If this is a new file, you would need to use `git add <file>` first to stage the file.

Git will enter patch mode (`-p`), allowing you to interactively choose which hunks (parts) of the file you would like to stage for the comit:

```diff
» git commit -vp
diff --git a/_posts/2014-03-19-git-editing-markdown.md b/_posts/2014-03-19-git-editing-markdown.md
index 2f7eb76..41a2eb5 100644
--- a/_posts/2014-03-19-git-editing-markdown.md
+++ b/_posts/2014-03-19-git-editing-markdown.md
@@ -11,7 +11,7 @@ I also use [Git](http://git-scm.com/), a distributed version control system.

 I love the idea of using Git to store code; the smallest change in a script could drastically alter its functionality and so the idea of using version control for source code is very intuitive.

-Using Git for storing text documents, while a good idea in principle, can seem daunting at first given the extra steps involved in [staging](), [committing]() and [pushing]() your changes. Yet armed with knowledge of Git's many commandline switches, the prospect becomes much more palatable.
+Using Git for storing text documents, while a good idea in principle, can seem daunting at first given the extra steps involved in [staging](https://www.kernel.org/pub/software/scm/git/docs/git-add.html), [committing](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html) and [pushing](http://git-scm.com/docs/git-push) your changes. Yet armed with knowledge of Git's many commandline switches, the prospect becomes much more palatable.

 ### Editing Markdown

Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? ?
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk nor any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk nor any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

While the above screen readout looks complicated, the shortcuts quickly become second nature. Git is iterating through each of the changes you have made, separating them into 'hunks', and checking if you wish to stage that hunk ready for committing.

Type `y` to stage the current hunk show above, `n` to leave it unstaged and `q` when you've staged all the hunks you need and are ready to type your commit message.

`s` is useful for splitting large hunks into smaller changes, and `?` will print out the list of options shown above in case you forget.

Note that the `-v` commandline switch stands for 'verbose'; when specified with the 'commit' command, Git will show you a *diff* of the changes you have made beneath your commit message.

Git will keep asking you which hunks you would like to stage until you've either pressed `q`, or, accepted or rejected all of the changes since your last commit.

You'll then need to enter your commit message as normal and you're done.
