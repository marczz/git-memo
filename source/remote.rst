..  index:: !remote
            !git; remote

..  _remote_repositories:

Remote repositories
===================

..  _remote_list:

Listing configured remotes
--------------------------

To know what are the name of the configured remotes::

  $ git remote

To list also the url::

  $ git remote -v

To show how is configured one remote, and also
:ref:`list which branches are tracked <list_upstream_branch>`::

       $ git remote show <remote name>

..  index::
    git; clone
    single: remote; clone

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
To add a new remote called ``myserver``, in my local ``git-memo`` repository, I do:
::

    git remote add myserver git://myserver.com/cramz/git-memo.git

this does not fetch or push, or checkout anything, but the next:
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


..  index:: single: remote; bare

..  _creating_bare_remote:

Creating a bare remote repository
---------------------------------
You can either transfer a local bare repository or create an empty
remote and push your branches.

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

These operations can also be used to create an ordinary remote, you
simply omit the ``--bare`` option.

..  index::
    single: remote; push
    single: remote; delete branch

Push and delete remote branches
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
