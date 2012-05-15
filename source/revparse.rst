.. _naming_commits:

Naming commits
==============

See :gitdoc:`git-rev-parse <git-rev-parse>`,
:gitdoc:`gitrevisions <gitrevisions>`,
:gitdoc:`git-name-rev <git-name-rev>`,
:gitdoc:`git-describe <git-describe>`,
:gitdoc:`git-reflog <git-reflog>`

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

-   find the hash of some file

    -   Look at `Pro Git: Git Objects
        <http://progit.org/book/ch9-2.html>`_ for details

        -   `Discussion by Linus Torvald
            <http://article.gmane.org/gmane.comp.version-control.git/44849>`_

    ::

        git hash-object <file>
        cat <file> | git hash-object --stdin
        (/usr/bin/stat  --printf "blob %s\0" calculette.c; cat calculette.c) | sha1sum

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







..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
