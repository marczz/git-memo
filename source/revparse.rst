.. _naming_commits:

Objects names
=============

Naming commits
--------------

.. index:: pair;git;rev-parse

See :gitdoc:`git-rev-parse <git-rev-parse.html>`,
:gitdoc:`gitrevisions(7) <gitrevisions.html>`,
:gitdoc:`git-name-rev(1) <git-name-rev.html>`,
:gitdoc:`git-describe(1) <git-describe.html>`,
:gitdoc:`git-reflog(1) <git-reflog.html>`

-   To inspect your repository (and in scripts)

    ::

        $ pwd
        /home/marc/bash/lib
        $ git rev-parse  --is-inside-git-dir
        false
        $ git rev-parse  --is-inside-work-tree
        true
        $ git rev-parse --git-dir
        /shared/home/marc/bash/.git
        $ git rev-parse --show-cdup
        ../
        $ git rev-parse --show-prefix
        lib/

-   translating symbolic <-> sha

    ::

        $ git rev-parse --symbolic-full-name HEAD
        refs/heads/master
        $ git symbolic-ref HEAD
        refs/heads/master
        $ git name-rev --name-only HEAD
        master
        $ git rev-parse  HEAD~3
        25f4b1d58e20f2026a36d80073654f52b055537b
        $ git name-rev --name-only 25f4b1d58e20
        master~3

.. index::
   hash
   single:git; hash-object

-   find the hash of some file

    -   Look at :progit:`Pro Git: Git
        Objects<Git-Internals-Git-Objects>`
        for details.

        -   `Discussion by Linus Torvald
            <http://article.gmane.org/gmane.comp.version-control.git/44849>`_

    ::

        git hash-object <file>
        cat <file> | git hash-object --stdin
        (/usr/bin/stat  --printf "blob %s\0" calculette.c; cat calculette.c) | sha1sum

.. index::
   pair:git; remote

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

.. index::
   pair:git;describe

-   version/most recent tag

    ::

        $ git describe HEAD
        init-1.0-29-gcb97cd9
        $ git name-rev --name-only cb97cd9
        master
        $ git describe HEAD~14
        init-1.0-15-g84aeca4
        $ git name-rev --name-only 84aeca4
        master~14
        $ git describe HEAD~29
        init-1.0
        $ git describe --long HEAD~29
        init-1.0-0-ge23c217


.. index::
   pair:git;name-rev

-   past tips of branches

    We use the reflog, be careful that the reflog is local to your
    repository, and is pruned by ``git reflog expire`` or by ``git gc``
    ``HEAD@{25}`` is the 25th older head of branch, this is not always
    the same than ``HEAD~25`` which is the 25th ancestor of the
    actual head.
    ::

        $ git name-rev HEAD@{25}
        HEAD@{25} b3distrib~11
        $ git rev-parse HEAD@{25}
        2518dd006de12f8357e9694bf51a27bbd5bb5c7a
        $ git rev-parse HEAD~11
        2518dd006de12f8357e9694bf51a27bbd5bb5c7a
        $ git name-rev 2518dd0
        2518dd0 b3distrib~11
        $ git rev-parse HEAD@{18}
        0c4c8c0ea9ab54b92a2a6d2fed51d19c50cd3d76
        $ git name-rev HEAD@{18}
        HEAD@{18} undefined
        $ git rev-parse HEAD@{14}~4
        0c4c8c0ea9ab54b92a2a6d2fed51d19c50cd3d76
        $ git rev-parse HEAD@{13}~5
        24c85381f6d7420366e7a5e305c544a44f34fb0f
        git log -1 -g --oneline HEAD@{13}
        a1b9b5c HEAD@{13}: checkout: moving from b3distrib to a1b9b5c

    In the previous example The 13th ancestor from the ``HEAD`` is a
    checkout at the beginning of a rebase so ``HEAD@{14}`` is now
    dangling, and ``HEAD@{18}`` the fourth predecessor (``HEAD@{14}~4``) of
    ``HEAD@{14}`` is unreachable from a ref.

    Nevertheless ``HEAD@{25}`` has been rebased as  ``HEAD~11`` and
    can be reached.

..  index::
    pair object; sha
    single: file; sha
    single: git;ls-files
    single: git; ls-tree


Finding the sha of a file
-------------------------

Refs:
    :gitdoc:`git ls-files(1) <git-ls-tree.html>`,
    :gitdoc:`git ls-tree(1) <git-ls-tree.html>`,
    :gitdoc:`git-rev-parse(1) <git-rev-parse.html>`,
    :gitdoc:`gitrevisions(7) <gitrevisions.html>`.

To show the blog sha associated with a file **in the index**:

::

    $ git ls-files --stage somefile
    100644 a8ca07da52ba219e2c76685b7e59b34da435a007 0	somefile

:gitdoc:`git ls-file <git-ls-files.html>` use by default the cached
content, while next commands can give any object commited or in the index.

If you use plumbing commands, you can also show the blog sha of the
object associated with a relative path in the *HEAD* revision by::

    $ git ls-tree HEAD <path>

You can also use path starting from the git worktree directory.
If the root of your are in a directory *subdir* you get the same
result with::

    $ git ls-tree HEAD somefile
    100644 blob 1a8bedab89a0689886cad63812fca9918d194a98	somefile
    $ git ls-tree HEAD :somefile
    100644 blob 1a8bedab89a0689886cad63812fca9918d194a98	somefile
    $ git ls-tree HEAD :./somefile
    100644 blob 1a8bedab89a0689886cad63812fca9918d194a98	somefile
    git ls-tree HEAD :/subdir/file #note initial slash
    100644 blob 1a8bedab89a0689886cad63812fca9918d194a98	somefile

you can also use :gitdoc:`git rev-parse <git-rev-parse.html>` with::

    $ git rev-parse HEAD:subdir/somefile # no leading slash
    1a8bedab89a0689886cad63812fca9918d194a98
    $ git rev-parse HEAD:./somefile
    1a8bedab89a0689886cad63812fca9918d194a98
    $ git rev-parse :./somefile # index cached content
    a8ca07da52ba219e2c76685b7e59b34da435a007
    $ git rev-parse :0:./somefile
    a8ca07da52ba219e2c76685b7e59b34da435a007
    $ sha1sum somefile # the unregisterd worktree version
    67a21c581328157099e8eac97b063cff2fb1a807  somefile


Finding the top level directory
-------------------------------

.. index::
   single: git;rev-parse

Ref: :gitdoc:`git-rev-parse(1) <git-rev-parse.html>`

To show the absolute path of the top-level directory.:
::

    $git rev-parse --show-toplevel

To show the *relative* path of the top-level repository::

    $git rev-parse --show-cdup

or to show the path of the current directory relative to the
top-level::

    $git rev-parse --show-prefix

I use it to have a default message showing paths relative to top-level
with::

    $git commit :/$(git rev-parse --show-prefix)<relative-name>


To show the git directory:
::

    $git rev-parse --git-dir

If ``$GIT_DIR`` is defined it is  returned otherwise when we are in
Git directory return the ``.git`` directory, if not exit with nonzero
status after printing an error message.

To know if you are in a work-tree::

    $git rev-parse --is-inside-work-tree

Note also that an alias expansion  prefixed with an exclamation point
will be executed from the top-level directory of a repository
i.e. from ``git rev-parse --show-toplevel``.
