..  _remote_tracking:

.. index:: !branch

.. index::
    !single: git;branch

..  index::
    remote; tracking
    pair: upstream; branch

Remote tracking branches
========================

Tracking or not tracking
------------------------

When you :ref:`clone a remote repository <remote_clone>` all the
remote branches are tracked and set as *upstream branch* for
the new checked out master, then :gitdoc:`git-pull(1) <git-pull.html>`
will appropriately merge from the starting point branch.

But it is not special to cloning,  when a local branch is started off
a remote-tracking branch, the remote branch is tracked, with the
default value of the global ``branch.autosetupmerge`` configuration flag.

If you want to override this global setting, you can use the option
``--track`` or ``--no-track``.

Listing upsteam branches
------------------------

To see what are the upstream branches of your branches you can do:
::

    $ git branch -vv

or for a specific branch *mybranch*:
::

    $git branch -vv --list mybranch

or:
::

    $ git config branch.mybranch.merge

To know what branch are tracked by a specific remote *origin*:
::

    $ git remote show origin


Create a branch and track the origin
------------------------------------

To start a local branch from *origin/mywork* and track the origin,
you issue::

    $ git branch  mywork origin/mywork

The new branch is created, but the HEAD is unchanged, to move HEAD to
*mywork* you have to issue::

    $ git checkout mywork

both command can be summarized in only one::

    $ git checkout mywork

because when the branch is not existing, git will look for a matching
name for a remote tracking branch.

Creating a branch from a remote tracking branch will set the
following config options:

..  code-block:: ini

    branch.mywork.remote=origin
    branch.mywork.merge=refs/heads/mywork

The first line tell what is the default remote to fetch and push to. If you want to
fetch/pull from one remote and push to another one use ``remote`` for fetching and
``pushremote`` for pushing.

The second line tell which ref on the remote should be used for fetching, pulling and
rebasing.

Togrther these options will cause a :gitdoc:`git pull<git-pull.html>` without remote nor
refspec given, to merge *origin/mywork* in *mywork*.

You can also use non matching names::

    $ git branch --track devel origin/mywork
    $ git checkout devel

is also done with::

    $ git checkout -b devel --track origin/mywork

Since branches from a remote are implicitely tracked, you can omit the ``--track``
option from the previous two commands and do::

    $ git branch --track devel origin/mywork
    $ git checkout devel

or::

    $ git checkout -b devel origin/mywork


The default when you create a branch from an other local branch is to not track,

When you do ::

  $ git branch newdev devel

devel is not tracked, if you want to have it tracked you should use::

  $  git branch --track  newdev devel

These defaults depend of the global configuration option ``branch.autoSetupMerge`` whose
default value is ``true``.



Asking to not track
-------------------

To start a local branch from *origin/mywork* but not track the origin,
you issue::

    $ git branch --no-track  mywork origin/mywork

Note that for two local branches the default, is no tracking, so with::

    $ git branch develop master

or::

    $ git checkout -b develop master

*develop* will not track *master*, unless you have used::

    $ git branch --track  develop master

or::

    $ git checkout -b --track  develop master


You can add a tracking of an upstream branch with::

    $ git branch --set-upstream-to=origin/mywork mywork

This is specially usefull whan you first created *mywork* and  then
pushed it to *origin* as::

    $ git push origin mywork

will not set *origin/mywork* as remote tracking branch for *mywork*,
except if you explicitly issue::

    $ git push --set-upstream origin mywork

``--set-upstream`` is abridged in ``-u``.

Configuration of upstream branches
----------------------------------

A branch is registered as *upstream* for another one by setting the
two configuration variables ``branch.<name>.remote`` and
``branch.<name>.merge``.

The previous tracking branch will result in a configuration
including:

..  code-block:: ini

    [branch "mywork"]
    remote = origin
    merge = refs/heads/mywork

    [remote "origin"]
    url = <url>
    fetch = +refs/heads/*:refs/remotes/origin/*


see the documentation of these two configuration options in
:gitdoc:`git-config(1) <git-config.html>` to learn the configuration
setting a local branch as upstream for an other local branch.

..   source for article

    -   [[https://blogs.atlassian.com/2013/07/git-upstreams-forks/][Git Forks And Upstreams- Atlassian Blogs]]
    -   [[http://alblue.bandlem.com/2011/07/git-tip-of-week-tracking-branches.html][Tracking Branches - AlBlue’s Blog]]
    -   [[http://ginsys.eu/git-and-github-keeping-a-feature-branch-updated-with-upstream/][Git and Github: keeping a feature branch updated with
        upstream?]]
