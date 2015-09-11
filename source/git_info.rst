Repository Info
===============

Ref: :gitdoc:`git tutorial <gittutorial.html>`
    :gitdoc:`Git User Manual <user-manual.html>`

..  index::
    !single: git;status

Status
------

Refs: :gitdoc:`git-status(1) <git-status.html>`

Status of the repo: files modified from index, index that differ from
the HEAD::

  git status

Don't show untracked files::

  git status -u no

Status of the current directory::

  git status -- .

..  index::
    branches
    single: git;branch

Branches
--------
Refs: :gitdoc:`git-branch(1) <git-branch.html>`,

Local branches::

    $ git branch

Remotes branches::

    $ git branch -r
    $ git branch -a # both local and remotes

Show also what are the tracking branches, if any.::

    $ git branch -vv

.. index::
   remote
   single: git; remote

Remotes
-------
See also the :doc:`remote section <remote>`

-   to see remotes

    ::

        $ git remote
        lighttpd
        nx_server
        ssh_server
        ....
        $ git rev-parse  --symbolic --remotes
        lighttpd/master
        nx_server/distribution
        nx_server/kernoel
        nx_server/master
        ssh_server/distribution
        ssh_server/kernoel
        ssh_server/master
        ssh_server/tubuntu
        ....

-   remote details

    ::

        $ git remote show ssh_server
        * remote ssh_server
          URL: ../ssh_server
          Tracked remote branches
          kernoel master tubuntu
          $ git config --get-regexp remote\\.ssh_server\\..\*
          remote.ssh_server.url ../ssh_server
          remote.ssh_server.fetch +refs/heads/*:refs/remotes/ssh_server/*


..  index::
    single: tag; list
    single: git; tag

..  _tag_info:

Tags
----
Refs: :gitdoc:`git-tag(1) <git-tag.html>`,

To know your local tags::

    $ git tag
    v1
    v2

If you want to know the annotation that *may* come with the tag you
have to use the option ``--list <pattern>`` abr. ``-l<pattern>`` wich list the tages that
match a shell pattern, and ``n<num>`` that allow *n* lines of
annotation.

To get all the tags with annotations::

    $ git tag -n100 -l \*

See also :ref:`remote_tags`

..  index::
    version
    single: git; describe


Describe
--------

Refs: :gitdoc:`git-describe <git-describe.html>`

``git-describe`` show the the most recent tag that is reachable from a
commit. By default it uses only annotated tags, but you can use any
tag with ``--tags`` option.

Exemple::

    $ git tag
    v0.2
    v0.90
    $ git describe
    v0.90-3-g8a8e4de

You are 3 commits after the version  v0.90 at the commit
``8a8e4de``. The prefix ``g`` is added to indicate a git managed version.

..  index::
    log
    gitrevisions
    !single: git;log
    single: git;show
    single: git;diff

Logs
----

Refs:
    :gitdoc:`git-log(1) <git-log.html>`,
    :gitdoc:`git-show(1) <git-show.html>`,
    :gitdoc:`git-diff(1) <git-diff.html>`,
    :gitdoc:`gitrevisions(7) <gitrevisions.html>`.

    :gitdoc:`git tutorial: Exploring history
    <gittutorial.html#_exploring_history>`.

    :gitdoc:`Git User Manual <user-manual.html>`:
    :gitdoc:`Browsing revisions
    <user-manual.html#browsing-revisions>`,
    :gitdoc:`Understanding Commits
    <user-manual.html#understanding-commits>`,

Review changes in the whole repository.

::

    $ git log --name-status
    $ git log --summary
    $ git log --stat
    $ git log --patch # abbrev -p


.. index:: gitk

Changes on some file/directory

::

    $ git log  --stat   -- Muttrc
    $ gitk -- Muttrc
    $ gitk --all -- Muttrc

All the commits which add or remove any file data
matching the string ``'foo()'``::


    $ git log -S'foo()'

To follow among renames, even crossing directories, use for a single
file::

    $ git log -p --follow Muttrc


Changes in a commit range::

    $ git log v2.6.15..v2.6.16  # ...in v2.6.16, not in v2.6.15
    $ git log master..test      # ...in branch test, not in branch master
    $ git log test..master      # ...in branch master, but not in test
    $ git log test...master     # ...in one branch, not in both
    $ git log --since="2 weeks ago"


Changes introduced by the last commit:

::

    $ git log -1 --stat
    $ git log -1 -p

Changes introduced by some commit:
You need only the initial part of the commit sha.

::

    $ git log -1 --stat 20b0f6e1961d5da
    $ git log -1 --stat -p  20b0f6e1961d5da
    $ git show 20b0f6e1961d5da
    $ git show HEAD
    $ git show devel # the tip of the "devel" branch or tag
    $ git show HEAD^  # to see the parent of HEAD
    $ git show HEAD~4 # 4 commits before HEAD

If the commit is a merge commit ``git show <commit>`` give only the
difference between <commit> and its first parent. To get both::

    $ git show <commit>^1
    $ git show <commit>^2

You can also use :gitdoc:`git-diff <git-diff.html>` but by suffixing
the commit with ``^!`` to mean the commit and nothing in the ancestors
(see :gitdoc:`gitrevisions <gitrevisions.html>`)
::

    $ git diff 20b0f6e1961d5da^!
    $ git diff HEAD^!

..  index::
    !reflog
    !single: git;reflog
    single: git; log -g
    single: git; show-branch -g

Reflogs
-------

Refs:
    :gitdoc:`git-reflog(1) <git-reflog.html>`,
    :gitdoc:`git-log(1) <git-log.html>`,
    :gitdoc:`git-show(1) <git-show.html>`,
    :gitdoc:`user-manual: recovering lost changes
    <user-manual.html#recovering-lost-changes>`,
    `git-notes: reflog <http://gitolite.com/reflog.html>`_

The reflog records each position of HEAD in the last 30 days (or
configuration ``gc.reflogExpireUnreachable``).  The reflog history is
local to your repository not shared, or cloned.

To show the reflog use:
::

    $ git reflog show --date=relative
    $ git log --walk-reflogs
    $ git show-branch --reflog

``--walk-reflogs`` and ``--reflog`` are abridged in ``-g``.
If the rebase and amend don't appear in a simple log without ``-g``,
when you use the reflog you can see and recover commits that have been
amended or let away by a rebase.

You can attain any past commit not yet pruned by:
::

    $ git log master@{1}
    $ git show HEAD@{"1 week ago"}


..  index::
    gitrevisions
    !single: git;diff
    !single: git;difftool
    single: git;format-patch

git diff
--------

:gitdoc:`git diff <git-diff.html>` show differences introduced by commits

Refs:
    :gitdoc:`git-diff(1) <git-diff.html>`,
    :gitdoc:`git-difftool(1) <git-difftool.html>`,
    :gitdoc:`gitrevisions(7) <gitrevisions.html>`,
    :gitdoc:`git-format-patch(1) <git-format-patch.html>`.

Diff and index:

::

    # Changes beetween the index and the working tree;
    # i.e change in the working tree not yet staged for the next commit.
    $ git diff
    # Changes between  your last commit and the index;
    # what you would be committing if you run "git commit" without "-a" option.
    $ git diff --cached
    # Changes beetween your last commit and the working tree;
    # what you would be committing if you run "git commit -a"
    $ git diff HEAD

diffs between  two branches::

    $ git diff master..test

You can also use a *difftool*, if you want to see the diff with
*meld*::

    $ git difftool --tool=meld  master..test


To know the list of available tools::

    $ git difftool --tool-help

To define a new tool you set in your ``.gitconfig``::

    [difftool "ediff"]
        cmd = emacs --eval \"(ediff-files \\\"$LOCAL\\\" \\\"$REMOTE\\\")\"

You use a triple dot to get the diff between the common ancestor of
*master* and *test* and the tip of *test*. *Warning: The semantic of the triple
dot is different with* ``git log``::

    $ git diff master...test

Patch to apply to *master* to obtain *test*::

    $ git format-patch master..test


..  index::
    gitk
    tig
    single: git;log

Commit tree
-----------

Refs:
:gitdoc:`gitk(1) <gitk.html>`,
`tig-manual <http://jonas.nitro.dk/tig/manual.html>`_,
:gitdoc:`git-log(1) <git-log.html>`,

View source commit tree, you can use many GUIs, gitk is provided with
git, and `tig <http://jonas.nitro.dk/tig/>`_ is a ncurses front-end.
.

::

    $ gitk --all
    $ tig --all
    $ gitg

You can also use :gitdoc:`git-log <git-log.html>`, with the option ``--graph``::

    $ git log --graph --pretty=oneline --abbrev-commit --decorate --all --color


..  index::
    !single: git;grep
    single: git;log

Looking file content in the tree
--------------------------------
Refs:
    :gitdoc:`git-grep(1) <git-grep.html>`,
    :gitdoc:`git-log(1) <git-log.html>`

::

    $ git grep "foo()"                   # search working directory for "foo()"
    $ git grep 'defun.*init *\(.*\)'     # search working directory for pattern
    $ git grep -E 'defun.*init *(.*)'    # use extended regexp (default basic)
    $ git grep "foo()" v2.6.15           # search old tree for "foo()"
    $ git grep init 6874caeedb3c -- *.el # search "init" in .el files at some commit

To search ``"foo()"`` in all files in all commit history::

    $ git rev-list --all | xargs git grep "foo()"

To look for the commits that **introduced** or **removed** ``"foo()"``::

    $ git log -p -S "foo()"

To search for commit that  **introduced** or **removed** an extended regex::

    $ git log -p -S pickaxe-regex 'defun.*init *\(.*\)'

To search for commit whose patch text contains added/removed lines that match
a regex::

    $  git log -p -G 'defun.*init *\(.*\)'

``log -G`` will show a commit that just **moved** the regexp,
without changing its number of occurences, while ``log -p -S pickaxe-regex``
will not retain it.

..  index::
    !single: git;show


Viewing other versions of a file
--------------------------------
Refs:  :gitdoc:`git-show(1) <git-show.html>`,

You can use a tag, a branch, or a commit sha.

::

    $ git show devel:src/prog.py
    $ git show v2.5:src/prog.py
    $ git show e05db0fd4f3:src/prog.py
