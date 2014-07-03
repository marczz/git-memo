Fixing errors
===============

..  index:: amend
..  index:: rebase

Amend a commit
--------------
..  index:: git;commit --amend

To amend the **last** commit::

    $ #correct the errors, then
    $ git add file
    $ git commit --amend
    $ # fix the commit message if necessary

To amend a past commit on a clean working directory, you need to come
back to the erroneous commit, fix it and :ref:`rebase` the latter
commits on the the new fixed state.

An example adapted from :gitdoc:`user-manual: Rewriting a
single commit <user-manual.html#rewriting-one-commit>`

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
:ref:`in the rebase section <rebase_interactive>`.
where you find an
:ref:`example of fixing an old error with interactive rebase
<interractive_rebase_example>`.

..  index::
    pair: commit; squash
    pair: commit; fixup
    pair: rebase; autosquash

Commit a fixup and squash.
--------------------------

The :ref:`rebase --interactive <rebase_interactive>` \  can be made a lot
simpler for fixing errors wit the ``git commit --fixup`` or
``git commit --squash command``.

On a clean worktree, or cleaned by a ``git stash``, you change your
erroneous file(s) and commit it (them) with
::

    $ git commit --fixup=a0b1c2d3

Where you give the erroneous commit number, then you *fixup* the error
with:
::

    $ git rebase --interactive --autosquash a0b1c2d3^

The first command just use the original message prefixed by
``fixup!``, the second one squash the original and next commit
discarding the message of the fixup commit.

You can also do a simple commit and begin your message by ``fixup!``
followed by an initial section of the original commit message.

If instead of *fixup* you use *squash* the process is similar but the
commit message for the folded commit is the concatenation of the
messages of the first commit and of those with the *squash* command.

..  _split_commit:

splitting a commit
------------------

..  index:: pair: split; commit

ref: :gitdoc:`git-rebase: splitting commits
<git-rebase.html#_splitting_commits>`,
:gitdoc:`git gui <git-gui.html>`

To split a commit, you first rebase interactively to the commit or one
of its ancestor
::

    $ git rebase -i <commit>

Then you mark the commit with the action *edit*, and when it comes to
editing::

    $ git reset HEAD^

Then you can examine the status with::

  $ git status

and add some files and stage the appropriate hunks. It can be easy to
use::

  $ git gui

to commit the appropriate hunks in individual commits

Then you can as usual do::

    $ git rebase --continue.
