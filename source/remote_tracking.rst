..  _remote_tracking:

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

To start a local branch from *origin/mywork* but not track the origin,
you issue::

    git branch --no-track origin/mywork

Note that for two local branches the default, is no tracking, so with::

    git branch develop master

or::

    git checkout -b develop master

develop will not track master, unless you have used::

    git branch --track  develop master

or::

    git checkout -b --track  develop master


You can add a tracking of an upstream branch with::

    git branch --set-upstream-to=origin/mywork mywork

This is specially usefull whan you first created *mywork* and  then
pushed it to *origin* as::

    git push origin mywork

will not set *origin/mywork* as remote tracking branch for *mywork*,
except if you explicitly issue::

    git push --set-upstream origin mywork

or have set  ``branch.autosetupmerge`` to ``always``.

``--set-upstream`` is abridged in ``-u``.

Configuration of upstream branches
----------------------------------

A branch is registered as *upstream* for another one by setting the
two configuration variables ``branch.<name>.remote`` and
``branch.<name>.merge``.

The previous tracking branch will result in a configuration
including::

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

    -   [[http://stackoverflow.com/questions/14169130/difference-between-git-branch-set-upstream-to-vs-git-remote-add-origin][Difference between git branch --set-upstream-to vs git remote add
        origin - Stack Overflow]]
    -   [[http://stackoverflow.com/questions/6089294/why-do-i-need-to-do-set-upstream-all-the-time][git - Why do I need to do `--set-upstream` all the time? - Stack
        Overflow]]
    -   [[http://felipec.wordpress.com/2013/09/01/advanced-git-concepts-the-upstream-tracking-branch/][the upstream tracking branch | Felipe Contreras]]
    -   [[http://gitready.com/beginner/2009/03/09/remote-tracking-branches.html][git ready » remote tracking branches]]
    -   [[https://blogs.atlassian.com/2013/07/git-upstreams-forks/][Git Forks And Upstreams- Atlassian Blogs]]
    -   [[http://alblue.bandlem.com/2011/07/git-tip-of-week-tracking-branches.html][Tracking Branches - AlBlue’s Blog]]
    -   [[http://ginsys.eu/git-and-github-keeping-a-feature-branch-updated-with-upstream/][Git and Github: keeping a feature branch updated with
        upstream?]]
    -   *** [[http://serverfault.com/questions/175052/how-to-tell-which-local-branch-is-tracking-which-remote-branch-in-git][How to tell which local branch is tracking which remote
        branch in Git? - Server Fault]]
    -   [[http://stackoverflow.com/questions/1526794/git-rename-master-branch-for-both-local-and-remote-repository][Git: Rename "master" branch for both local and remote
        repository - Stack Overflow]]
