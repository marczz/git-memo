..  index:: !remote

Remote repositories
===================

Fetching from Remote
--------------------

To fetch all remotes::

    $ git remote update

..  index:: config

Remote configuration
--------------------

To get remotes configuration  we can edit .git/config or

::

    $ git config --get-regexp 'remote'
    remote.etc.url /shared/home/marc/crypt/gits/etc_shared.git
    remote.etc.fetch +refs/remotes/etc/*:refs/remotes/etc/*
    remote.etc.tagopt --no-tags


Options are added with commands like::

    $ git config --add remote.etc.tagopt --no-tags

creating a remote repository
----------------------------

::

    $ git clone --bare ~/proj proj.git
    $ touch proj.git/git-daemon-export-ok
    $ scp proj.git myserver.com/var/git/proj.git
    $ cd proj
    $ git remote add origin ssh://myserver.com/var/git/proj.git

We can also create it empty and push on the remote server

-   on the remote server::

        $ mkdir /var/git/proj.git && cd /var/git/proj.git
        $ git --bare init

-   on the client::

       $ cd proj
       $ git remote add origin ssh://myserver.com/var/git/proj.git
       $ git push origin master

push and delete remote branches
-------------------------------

To push a new branch::

  $ git push origin newfeature

To delete the branch on the remote::

  $ git push origin :newfeature

*It means push an empty branch to newfeature*

References
----------

-   :github:`remotes`
-   :gitdoc:`git-remote`
-   `Setting up a new remote git repository
    <http://toolmantim.com/articles/setting_up_a_new_remote_git_repository>`_


..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
