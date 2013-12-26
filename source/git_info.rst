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
    $ git log --follow #To follow among file renames

.. index:: gitk

Changes on some file/directory

::

    $ git log  --stat   -- Muttrc
    $ gitk -- Muttrc
    $ gitk --all -- Muttrc
    $ git log -S'foo()' # commits which add or remove any file data
                        # matching the string 'foo()'


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
        gitrevisions
        !single: git;diff
        single: git;format-patch

git diff
--------

:gitdoc:`git diff <git-diff.html>` show differences introduced by commits

Refs:
    :gitdoc:`git-diff(1) <git-diff.html>`,
    :gitdoc:`gitrevisions(7) <gitrevisions.html>`,
    :gitdoc:`git-format-patch(1) <git-format-patch.html>`.

Diff and index:

::

    # Changes in the working tree not yet staged for the next commit.
    $ git diff
    # Changes between the index and your last commit;
    # what you would be committing if you run "git commit" without "-a" option.
    $ git diff --cached
    # Changes in the working tree since your last commit;
    #what you would be committing if you run "git commit -a"
    $ git diff HEAD

diffs between  two branches::

    $ git diff master..test

diff between the common ancestor of *master* and *test* and the tip of
*test*. The semantic of the triple dot is different with ``git log``::

    $ git diff master...test

Patch to apply to *master* to obtain *test*::

    $ git format-patch master..test


..  index::
        gitk
        tig
        single: git;log

commit tree
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

Looking file content in the tree
--------------------------------
Refs:  :gitdoc:`git-grep(1) <git-grep.html>`

::

    $ git grep "foo()"          # search working directory for "foo()"
    $ git grep v2.6.15 "foo()"  # search old tree for "foo()"


..  index::
        single: git;show


Viewing other versions of a file
--------------------------------
Refs:  :gitdoc:`git-show(1) <git-show.html>`,

You can use a tag, a branch, or a commit sha.

::

    $ git show devel:src/prog.py
    $ git show v2.5:src/prog.py
    $ git show e05db0fd4f3:src/prog.py

..  index::
        single: git;ls-files

Show the blog sha associated with a file in the index:

::

    $ git ls-files --stage <path>
