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

You have made a small error in the file SmtplibExample.py, and corrected
it, You don't want to make a new commit for this tiny fix, but make it
part of your previous commit.

You stash your current work

::

    $ git stash --quiet

You look at the last commit for the file

::

    $ git log -n 1 --pretty=oneline --abbrev-commit  TD/Smtp/SmtplibExample.py
    9c091e6 SmtplibExample.py: refactored to a function and a main.

You rebase from the previous commit::

  $ git rebase --interactive 9c091e6^

You get the rebase list to edit::

  pick 9c091e6 SmtplibExample.py: refactored to a function and a main.
  pick 3d3f53e SmtplibExample2.py: 2to3, switched to argparse, minor fixes
  pick 0c4f2cf Cours/SocketTcp.mdn: sockets lectures now in markdown
  pick aa34250 index.mdn: added sockets
  ....

You change the first *pick* to *edit* valid it, then rebase pause at::

  Stopped at 9c091e6... SmtplibExample.py: refactored to a function and a main.
  You can amend the commit now, with
  git commit --amend
  Once you are satisfied with your changes, run
  git rebase --continue

You checkout your amended file from the stash::

    git checkout stash@{0} -- : TD/Smtp/SmtplibExample.py

You add it and amend the commit::

    git add TD/Smtp/SmtplibExample.py
    git commit --amend


You continue the rebase::

  [detached HEAD eae8d29] SmtplibExample.py: refactored to a function and a main.
  1 files changed, 22 insertions(+), 14 deletions(-)
  Successfully rebased and updated refs/heads/master.

Checking your rebase and undoing it
-----------------------------------

The rebase can be a dangerous operation, sometime I lost a file by
deleting a commit that add a file within an interactive rebase. The
head *before* a rebase is stored in ORIG_HEAD. All dangerous
operations like *rebase*, *merge*, *pull*, *am*  modify this
reference, so you can only use it to refer to the HEAD *before* the
last dangerous operation (but a simple commit don't change it).

To see what you have changed in the repository since last dangerous
operation::

  git diff ORIG_HEAD HEAD

If it was an interactive rebase to clean your history you expect that
you preserved the global state of your repository, and to have an
empty answer.

To see what commits are in HEAD and not in ORIG_HEAD::

  git log ORIG_HEAD..HEAD


You can also use visualization tools like *tig* ou *gitk*::

  gitk ORIG_HEAD HEAD
  gitk ORIG_HEAD --not --all
  tig ORIG_HEAD..HEAD

Or::

  tig ORIG_HEAD...HEAD

and you may want to toggle revision graph visualization with `g` key.

After an interactive rebase you may want to check the commits since
the begining of the rebase in both branches. You will use::

   git log --boundary --pretty=oneline --left-right  ORIG_HEAD...HEAD

And if your rebase went wrong you restore the previous state with::

  git reset --hard ORIG_HEAD

If you have lost your ORIG_HEAD after a rebase because you did an other operation
that reset it, you can still find the previous head which is now a
dangling ref, unless you have garbage collected it.

You need to inspect your reflog and find the first commit before the
rebase, in an interactive rebase the process begin with a checkout of
the commit on which you rebase, so the previous commit was the head
before the rebase::

  git reflog

  ....
  95512de HEAD@{7}:  rebase -i (pick): fixin typos
  a1b9b5c HEAD@{8}: checkout: moving from master to a1b9b5c
  c819a90 HEAD@{9}: commit: adding myfile.txt

In this example the previous head was the ninth older commit HEAD\@{9} with an
abbreviated commit c819a90.


dangling objects
----------------

see :gitdoc:`Git user manual: dangling-objects
<user-manual.html#dangling-objects>`

After rebasing the old
branch head is no longer in a branch and so it is dangling, it will be
garbage collected when it will be no more referenced.

As explained in the previous section it is used in the reflog, so it
will be garbage collected after expiring the reflog.

Sometime, when we are certain our rebase is correct and we will never
want to come back to previous state, we want to clean these dangling
objects. We use::

  $ git prune

If we want to do the opposite, i.e. preventing this dangling commit to
be lost some next garbage collection away we can point a new branch at
it::

  $ git branch <recovery-branch> <dangling-commit-sha>

..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
