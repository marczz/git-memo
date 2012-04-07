Fixing errors
===============

..  index:: rebase
..  index:: amend

Amend a commit
--------------

To amend the **last** commit::

    $ #correct the errors, then
    $ git add file
    $ git commit --amend
    $ # fix the commit message if necessary

To amend a past commit on a clean working directory
(adapted from :gitdoc:`user-manual: Rewriting a
single commit <user-manual.html#rewriting-one-commit>`)

::

    $ git log --stat -- badfile
    $ git tag bad mywork~5
    $ git checkout bad
    $ # make changes here and update the index
    $ git commit --amend
    $ git rebase --onto HEAD bad mywork
    $ git tag -d bad

The use of the tag is optional you can also do

::

    $ BAD="7c2c66b71b"
    $ git checkout $BAD
    $ # make changes here and update the index
    $ git commit --amend
    $ git rebase --onto HEAD $BAD mywork

When you want to amend past commits with changes in the working tree,
you cannot checkout the past commit because the worktree is dirty.

 You can use
:gitdoc:`git stash <git-stash.html>` to come back to the original
worktree or work in a linked temporary working directory::

    $ git-new-workdir <repository> <temporary_workdir>
    $ cd <temporary_workdir>
    # find the commit to amend
    $ git log --stat
    $ BAD="<commit>"
    $ git reset --hard $BAD
    # edit the wrong file may be copying from <repository>
    $ git add <changed file>
    $ git commit --amend
    $ git rebase --onto HEAD $BAD mywork


You can also use ``git rebase --interactive`` as indicated
:ref:`bellow <rebase_interactive>`.


splitting a commit
------------------

ref: :gitdoc:`git-rebase: SPLITTING COMMITS <git-rebase.html#_splitting_commits>`

::

    $ git rebase -i <commit>^
    $ # Mark the commit with the action "edit"
    $ # When it comes to editing:
    $ git reset HEAD^
    $ git-gui
    $ # commit the appropriate entries
    $ git rebase --continue.


..  _rebase_interactive:

Reorder or merge patches
------------------------

Use :gitdoc:`git-rebase interactive mode <git-rebase.html#_interactive_mode>`


::

    $ git rebase --interactive HEAD~8
    pick f08daa2 modified:   moin/farmconfig.py configured for moinmoin notebook and marcwiki
    pick 802071d moin/notebook.py: added config for notebook
    pick 65802dc moin/farmconfig.py added  mail_smarthost
    pick ee35e7d changed fstab and hosts
    pick 9913667 /etc/fstab: fixed cifs and nfs shares
    pick 54055e3 fstab: crypt cannot be fscked at boot, disabled fsck
    pick 1470a45 fstab: changed mountpoint
    pick afbb0b8 passwd group mailcap state of etc/kernoel/master

    # Rebase 15b369f..afbb0b8 onto 15b369f
    #
    # Commands:
    #  p, pick = use commit
    #  e, edit = use commit, but stop for amending
    #  s, squash = use commit, but meld into previous commit,
    #              and edit the sum of commit messages
    # If you remove a line here THAT COMMIT WILL BE LOST.

You can also reorder commits, *reorder* + *squash* + *delete* is a very powerfull
tool to correct a suite of patches. For each edit or conflict rebase
will stop until you edit or merge comments (in case of a squash), or fix
the conflict, then you just need to::

    $ git rebase --continue
    $ #or git rebase --abort

..  A developper
    git stash
    git stash list
    git stash show
    git show stash@{0}:TP/Controles/test_adresses/test_valide.py
    git log test_valide.py
    git log -n 5 d97580ec
    git rebase --interactive d8f3f8a9b
    git checkout stash@{0} -- test_valide.py
    git rebase --continue
    git stash pop

I have made a small error in the file SmtplibExample.py, and corrected
it, I don't want to make a new commit for this tiny fix, but make it
part of my previous commit.

I stash my current work

::

    $ git stash --quiet

I look at the last commit for the file

::

    $ git log -n 1 --pretty=oneline --abbrev-commit  TD/Smtp/SmtplibExample.py
    9c091e6 SmtplibExample.py: refactored to a function and a main.

I rebase from the previous commit::

  $ git rebase --interactive 9c091e6^

I get the rebase list to edit::

  pick 9c091e6 SmtplibExample.py: refactored to a function and a main.
  pick 3d3f53e SmtplibExample2.py: 2to3, switched to argparse, minor fixes
  pick 0c4f2cf Cours/SocketTcp.mdn: sockets lectures now in markdown
  pick aa34250 index.mdn: added sockets
  ....

I change the first *pick* to *edit* valid it, then rebase pause at::

  Stopped at 9c091e6... SmtplibExample.py: refactored to a function and a main.
  You can amend the commit now, with
  git commit --amend
  Once you are satisfied with your changes, run
  git rebase --continue

I checkout my amended file from the stash::

    git checkout stash@{0} -- : TD/Smtp/SmtplibExample.py

I continue the rebase::

  [detached HEAD eae8d29] SmtplibExample.py: refactored to a function and a main.
  1 files changed, 22 insertions(+), 14 deletions(-)
  Successfully rebased and updated refs/heads/master.

dangling objects
----------------

see :gitdoc:`Git user manual: dangling-objects
<user-manual.html#dangling-objects>`

After rebasing the old
branch head is no longer in a branch and so it is dangling, usually
we just want to prune it. We can inspect it with

::

    $ gitk <dangling-commit-sha> --not --all
    # or
    $ gitk <dangling-commit-sha> <rebased-new-head>

then

::

    $ git prune
    # or
    $ git branch <recovery-branch> <dangling-commit-sha>


..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
