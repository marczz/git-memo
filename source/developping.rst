Developping
===========

Ref:
    :gitdoc:`User-manual: Developing with git
    <user-manual.html#Developing-With-git>`

..  index:
    single: git; commit
    single: commit; format

..  index
    single:git; add --interactive

Adding a part of a file
-----------------------
Ref:
    :gitdoc:`git-add(1) <git-add.html>`,
    `git book: Interactive Staging
    <http://git-scm.com/book/en/Git-Tools-Interactive-Staging>`

For a registered file, that has changed since last commit
you can use ``git add --interactive`` or ``-i`` to select the patch to
add to the index::

    $ git add -i
      1:        +0/-0       +65/-0 source/developping.rst
      2:    unchanged        +1/-1 source/error_fix.rst

      *** Commands ***
      1: status   2: update       3: revert       4: add untracked
      5: patch    6: diff         7: quit         8: help
      What now> p
      ........
      Stage this hunk [y,n,q,a,d,/,e,?]? e
      Waiting for Emacs...
      ....
      What now> s
                staged     unstaged path
      1:        +7/-0       +58/-0 source/developping.rst
      2:    unchanged        +1/-1 source/error_fix.rst
      .....
      What now> q
      Bye.

Here the patch has be selected by editing the patch in emacs.

You can also use tools like :gitdoc:`git-gui(1) <git-gui.html>`,
`magit <magit.github.io/documentation.html>`_, or one of the many guis
availables to select the hunks you want to stage.

Adding a part of a new file
---------------------------

Now if the file is yet unregistered you cannot add only a part with
``git add -i``.

But you can use the sequence::

  $ git add -N new_file
  $ git add -i

``git add -N`` create an entry for *new-file* in the index with no
content.

Then the interactive add is done like above.

Well formed commits
-------------------

Summary of `A Note About Git Commit Messages
<http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html>`_
:

#. Short (50 chars or less) summary
#. Blank line.
#. More detailed explanatory text wrapped to about 72 cols.
#. Further paragraphs come after blank lines.
#. You can use bulleted text with a hyphen or asterisk preceded by a
   single space
#. Use the present tense.



..  index:
    single: commit; empty

Adding an empty commit at root of a branch.
-------------------------------------------
Refs:
    :gitdoc:`git symbolic-ref <git-symbolic-ref.html>`,
    :gitdoc:`git checkout <git-checkout.html>`,
    :gitdoc:`git clean <git-clean.html>`

::

    $ #store inexistent ref: newroot in HEAD
    $ git symbolic-ref HEAD refs/heads/newroot
    $ # wipe the index
    $ git rm --cached -r .
    $ # clean the worktree
    $ git clean -df
    $ #create the branch newroot with an empty commit
    $ git commit --allow-empty -m 'root commit'
    $ # rebase everything over newroot
    $ git rebase newroot master

-  repeat for other branches you want to rebase on the same newroot
-  You can then move to some branch and remove *newroot*
   with ``git branch -d newroot``.

Recent git have the ``--orphan`` option to ``checkout`` to create a
new branch starting from nowhere. You can
also do::

    $ git checkout --orphan newroot
    $ clear the index and the working tree
    $ git rm -rf .
    $ git commit --allow-empty -m 'root commit'
    $ git rebase newroot master
