Sharing development
===================

Refs:
    :gitdoc:`git manual:Sharing development with others
    <user-manual.html#sharing-development>`

Git Pull
--------

Refs:
    :gitdoc:`git pull <git-pull.html>`,
    :gitdoc:`git fetch <git-fetch.html>`

To pull your master branch with the remote tracking branch in
*origin*:
::

    $ git pull origin master

It is equivalent t:
::

    $ git fetch
    $ git merge origin/master

Git pull --rebase
-----------------
Refs:
    <http://gitolite.com/git-pull--rebase.html>`_

Instead of merging the remote repository after fetching, ``git pull --rebase``
rebase  the current branch on top of the upstream branch. But it uses
relog information to avoid to rebase commits previously pulled from
the remote.

Git push
--------

Refs:
    :gitdoc:`git push <git-push.html>`,
    :gitdoc:`git manual: Pushing changes
    <user-manual.html#pushing-changes-to-a-public-repository>`

To push your master branch to the *origin* repo:
::

    git push origin master

The origin should be ref:`configured as remote <remote_config>`.
