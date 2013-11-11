Repository Backup
=================

file-system copy
----------------

A git repository can be safely copied to an other directory, or put in
an archive file. This is a very basic way of keeping a backup of your
repo.

::

    cp -r myrepo backup_copy
    tar -czf backup_copy.tgz myrepo

But such a frozen copy can not be updated.

.. _git-bundle:

git bundle
----------

Git bundle let you pack the references of your repository as a single
file, but unlike the tar command above the bundle is a recognized git
source. You can  fetch, pull clone from it.

::

    $ git bundle create /path/to/mybundle master branch2 branch3

or to get all refs

::

   $ git bundle create /path/to/mybundle --all

You can then check what is in your bundle::

    $ git bundle list-heads /path/to/mybundle

After moving the bundle on any location you can get a new repository
by::

    $ git clone -b master /path/to/mybundle newrepo

If you want to continue your development in the first repository
you can do as follow::

    $ git tag lastsent master
    #.... do some commits
    $ git bundle create /path/to/newcommitbundle lastsent..master

Then send it by email or any dumb transport, and in the repository
on the other side::

    $ git bundle verify /path/to/newcommitbundle
    $ git pull /path/to/newcommitbundle master



More details in :gitdoc:`git-bundle(1) Manual Page <git-bundle.html>`
and `Git's Little Bundle of Joy <http://git-scm.com/2010/03/10/bundles.html>`_

A discussion is in `Stackoverflow: Backup a Local Git Repository
<http://stackoverflow.com/questions/2129214/backup-a-local-git-repository>`_

.. _gcrypt-remote:

gcrypt remote
-------------

Joey Hess gcrypt remote offer a great solution to backup to an unsecure
location.

You should first install ``git-remote-gcrypt`` from his repository
https://github.com/joeyh/git-remote-gcrypt.git

Then create the encrypted remote by pushing to it::

    git remote add cryptremote gcrypt::rsync://example.com:repo
    git push cryptremote master

More information in the `GitHub repository
<https://github.com/joeyh/git-remote-gcrypt/>`_

and in `Joey Hess: fully encrypted git repositories with gcrypt
<http://git-annex.branchable.com/tips/fully_encrypted_git_repositories_with_gcrypt/>`_
and `git-annex special remote: gcrypt
<http://git-annex.branchable.com/special_remotes/gcrypt/>`_


git mirror
----------

It is more interesting to use git do do backups.

You can mirror your repository to a bare repository with:

    git clone --mirror  myrepo repo_backup.git

*repo_backup.git* has the same branches than *myrepo* an an "origin" remote
pointing to *myrepo*. You can update the copy from repo_backup.git by
pulling *origin*.

mirror remote
-------------

In the previous solution the backup repository track its origin,
but the original repository knows nothing about its backup.
It ca be solved by using instead a mirror remote.

You first create an empty bare repository::

    git init --bare /path/to/reposave.git

Then in *myrepo*::

    git remote add --mirror=push backup file:///path/to/reposave.git
    git push backup

The backup repository will be updated by repeating the last command.

If you want to be able to recover in the backup from a bad push, you
may want the backup to keep a reflog. As reflog are disabled by
default in bare repository you shoul set it in *reposave.git*::

    git config core.logAllRefUpdates true

live mirror
-----------

Sometime you may want a mirror to experiment a dangerous operation.
In this case the mirror is not a backup we can better think as the
origin as the backup. The ``clone --mirror`` is not the solution since
it creates a bare repository. And a simple clone is not either even
if it as all reference, because it has only one branch, the other
branch are just registered as ``remotes/origin/name``. *

As you what to make experiments in this repository you probably don't
even want to track the origin, if you succeed you will replace the
original one, if you fail you will remove the repository.

You first do a simple clone::

    git clone myrepo /path/to/experimental

Then create all missing branches::

    cd /path/to/experimental
    git branch --no-track branch2 remotes/origin/branch2
    git branch --no-track branch3 remotes/origin/branch3

Delete the refs to origin::

    git branch -d remotes/origin/master remotes/origin/branch2 \
        remotes/origin/branch3 remotes/origin/HEAD
    git remote remove origin

And play with *experimental*
