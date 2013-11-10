Repository Info
===============

Refs: :gitdoc:`git-log <git-log>` :gitdoc:`git-diff <git-diff>`,
:gitdoc:`git-show <git-show>`


Review changes in the whole repository.

::

    $ git log --name-status
    $ git log --summary
    $ git log --stat

Changes introduced by the last commit:

::

    $ git log -1 --stat
    $ git log -1 -p
    $ git diff HEAD^!

Changes introduced by some commit:

::

    $ git log -1 --stat 20b0f6e1961d5da
    $ git log -1 --stat -p  20b0f6e1961d5da
    $ git show 20b0f6e1961d5da
    $ git diff 20b0f6e1961d5da^!

Changes on some file/directory

-    ``-- follow`` follow among file renames

::

    $ git log  --stat   -- Muttrc
    $ gitk -- Muttrc
    $ gitk --all -- Muttrc

View source commit tree:

::

    $ gitview --branches --remotes

Diff and index:

::

    # Changes in the working tree not yet staged for the next commit.
    $ git diff
    # Changes between the index and your last commit; what you would be committing if you run "git commit" without "-a" option.
    $ git diff --cached
    # Changes in the working tree since your last commit; what you would be committing if you run "git commit -a"
    $ git diff HEAD

Show the blog sha associated with a file in the index:

::

    $ git ls-files --stage <path>
