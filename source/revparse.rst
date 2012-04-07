Naming commits
==============

See `rev parse <http://localhost/doc/git-doc/git-rev-parse.html>`_,
`gitrevisions <http://localhost/doc/git-doc/gitrevisions.html>`_,
`name-rev <http://localhost/doc/git/html/git-name-rev.html>`_,
`describe <http://localhost/doc/git/html/git-describe.html>`_

-  To inspect your repository (and in scripts)

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

-  translating symbolic <-> sha

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

-  find the hash of some file

   -  Look at `Pro Git: Git
      Objects <http://progit.org/book/ch9-2.html>`_ for details
   -  `Discussion by Linus
      Torvald <http://article.gmane.org/gmane.comp.version-control.git/44849>`_

   ::

       git hash-object <file>
       cat <file> | git hash-object --stdin
       (/usr/bin/stat  --printf "blob %s\0" calculette.c; cat calculette.c) | sha1sum

-  to see remotes

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

-  remote details

   ::

       $ git remote show ssh_server
       * remote ssh_server
         URL: ../ssh_server
         Tracked remote branches
           kernoel master tubuntu
       $ git config --get-regexp remote\\.ssh_server\\..\*
       remote.ssh_server.url ../ssh_server
       remote.ssh_server.fetch +refs/heads/*:refs/remotes/ssh_server/*

-  version/most recent tag

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

..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
