..  index:: !remote
            git; remote

Remote repositories
===================
..  index::
    git; clone

..  _remote_clone:

Cloning a remote repository
---------------------------

Ref: :gitdoc:`git-clone(1) <git-clone.html>`.

To clone a remote repository::

    $ git clone git://github.com/cramz/git-memo.git

This create a local ``git-memo`` repository with a copy of the remote
and check-out the current branch.

All the remotes branches are copied in the local repository, and the
local branch are ref:`tracking the remote ones <remote_tracking>`

If you add the ``--mirror`` option you create a *bare* repository and
maps all remote refs in the local repository.

Adding a new remote
-------------------
In my local ``git-memo`` repository:

::

    git remote add myserver git://myserver.com/cramz/git-memo.git

add a new remote called ``myserver``, this does not fetch or push, or
checkout anything, but the next:

::

    git fetch

Will fetch ``refs/remotes/myserver/master``. This remote tracking
branch is updated also by:

::

    git remote update myserver

By default all the remote branches are tracked, but you can use ``-track
<branch>`` (abbrev  ``-t``) to select only some branches.

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

creating a bare remote repository
---------------------------------

#.  copy local bare repo
    ::

        $ git clone --bare ~/proj proj.git
        $ scp proj.git myserver.com/var/git/proj.git
        $ cd proj
        $ git remote add origin ssh://myserver.com/var/git/proj.git

#.  push a branch to an empty repository

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
-   :gitdoc:`git-remote(1) <git-remote.html>`
-   `Setting up a new remote git repository
    <http://toolmantim.com/articles/setting_up_a_new_remote_git_repository>`_
