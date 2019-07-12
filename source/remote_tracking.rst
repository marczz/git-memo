..  _remote_tracking:

..  index::
    branch
    !single: git;branch
    pair: remote; tracking
    pair: upstream; branch
    pair: branch; track

========================
Remote tracking branches
========================

Tracking or not tracking
========================

When you :ref:`clone a remote repository <remote_clone>` all the
remote branches are tracked and set as *upstream branch* for
the new checked out master, then :gitdoc:`git-pull(1) <git-pull.html>`
will appropriately merge from the starting point branch.

But it is not special to cloning,  when a local branch is started off
a remote-tracking branch, the remote branch is tracked, with the
default value of the global ``branch.autosetupmerge`` configuration flag.

If you want to override this global setting, you can use the option
``--track`` or ``--no-track``.

.. _list_upstream_branch:

Listing upstream branches
=========================

To see what are the upstream branches of your branches you can do:
::

    $ git branch -vv
    * master 376438d [origin/master] this commit is an example

or for a specific branch *mybranch*:
::

    $ git branch -vv --list mybranch
    * mybranch 226f541 [gitlab/master] an other example

The upstream branch is shown between brackets.

You can also explore the configuration and show the remote and the default fetched
reference *and the default pushed one if it differs from the fetched one* with:

::

    $ git config --get-regexp branch.mybranch
    branch.mybranch.remote gitlab
    branch.master.merge refs/heads/master

If you want to know all the upstream branches you can do

::

    $ git config --get-regexp branch\\..*\\.remote

To know what branches are tracked by a specific remote *origin*, and know the status of
the local branches (up to date or out of date):
::

    $ git remote show bitbucket
    * remote bitbucket
      Fetch URL: git@bitbucket.org:bigjohn/repo.git
      Push  URL: git@bitbucket.org:bigjohn/repo.git
      HEAD branch: master
      Remote branch:
        master tracked
      Local branch configured for 'git pull':
        master merges with remote master
      Local ref configured for 'git push':
        master pushes to master (up to date)

This command will query the remote repository, if you want to know only the local
configuration, you may add the ``-n`` option, but you will not know the status of the
local branches.

..  _create_and_track:

Create a branch and track the origin
====================================

Example with matching name
--------------------------

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


Example with non matching names
-------------------------------

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

..  _tracking_defaults:

Tracking defaults
=================
The default when creating a branch from a remote is to track the remote, but
when you create a branch from an other local branch is to not tracked by default.

When you do ::

  $ git branch newdev devel

devel is not tracked, if you want to have it tracked you should use::

  $  git branch --track  newdev devel

These defaults depend of the global configuration option ``branch.autoSetupMerge`` whose
default value is ``true``.



Asking to not track
===================

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


You can also :ref:`add to any branch a tracking of an upstream branch<set_upstream>`
::

    $ git branch --set-upstream-to=origin/mywork mywork

This is specially usefull whan you first created *mywork* and  then
pushed it to *origin* as::

    $ git push origin mywork

will not set *origin/mywork* as remote tracking branch for *mywork*,
except if you explicitly issue::

    $ git push --set-upstream origin mywork

``--set-upstream`` is abridged in ``-u``.

..  _upstream_config:

..  index::
    pair: upstream; config
    single: branch; remote
    single: branch; merge
    pair: branch; config

Configuration of upstream branches
==================================

A branch is registered as *upstream* for another one by setting the
two configuration variables ``branch.<name>.remote`` and
``branch.<name>.merge``.

These two configuration options are described in
:gitdoc:`git-config(1) <git-config.html>`.

The previous tracking branch will set the following config options:

..  code-block:: ini

    branch.mywork.remote=origin
    branch.mywork.merge=refs/heads/mywork

The first line tell what is the default remote to fetch and push to. If you want to
fetch/pull from one remote and push to another one use ``remote`` for fetching and
``pushremote`` for pushing.

If you want to set a default remote for all branches, you can also set the configuration
variable ``remote.Pushdefault`` which overide ``branch.<name>.remote`` for all branches.

The second line tell which ref on the remote should be used for fetching, pulling and
rebasing.

Together these options will cause a :gitdoc:`git pull<git-pull.html>` without remote nor
refspec given, to merge *origin/mywork* in *mywork*.


In the ``.git/config`` it appears as:

..  code-block:: ini

    [branch "mywork"]
    remote = origin
    merge = refs/heads/mywork

    [remote "origin"]
    url = <url>
    fetch = +refs/heads/*:refs/remotes/origin/*


If you track an other local branch, the *pseudo* remote is designed by a dot:

..  code-block:: ini

    branch.feature.remote=.
    branch.feature.merge=refs/heads/master

..  _set_upstream:

Changing the upstream branch
============================

We have seen how to :ref:`create a branch and track the origin <create_and_track>`
but you may also want change the tracking option of an existing branch, either declare
an upstream branch for a branch which does not have one, or change the upstream branch.

You can do it at the level of :ref:`upstream configuration <upstream_config>` either by using
:gitdoc:`git-config(1) <git-config.html>`, or even by editing the ``.git/config`` file,
but since v 1.8 git offer a new option of :gitdoc:`git-branch(1) <git-branch.html>`

::

    $ git branch --set-upstream-to github/master

will set the upstream branch for the currently checked out branch to ``github/master``,
this can also be done for an other branch by:

::

    $ git branch mywork --set-upstream-to github/master

You can also do it while pushing your branch to the new remote by

::

    $ git push --set-upstream github/master

or

::

    $ git push mywork --set-upstream github/master



This is specially usefull whan you first created a local branch *mywork* and  then
pushed it to *origin* as::

    $ git push origin mywork

will not set *origin/mywork* as remote tracking branch for *mywork*,
except if you either explicitly issue::

    $ git push --set-upstream origin mywork

or you later add tracking with:
::

    $ git branch --set-upstream-to=origin/mywork mywork

``--set-upstream`` (for ``git push``) or ``--set-upstream-to`` (for ``git branch``) are
both abridged in ``-u``.

If you no longer want any tracking for a branch, you can remove it with:
::

    $ git branch --unset-upstream <branch>

as usual if the branch name is omitted, it applies to the current checked out branch

..   source for article:

     -   [[http://alblue.bandlem.com/2011/07/git-tip-of-week-tracking-branches.html][Tracking Branches - AlBlue’s Blog]]
     -   [[http://ginsys.eu/git-and-github-keeping-a-feature-branch-updated-with-upstream/][Git and Github: keeping a feature branch updated with
         upstream?]]
