Developping
===========

Ref:
    :gitdoc:`User-manual: Developing with git
    <user-manual.html#Developing-With-git>`

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
