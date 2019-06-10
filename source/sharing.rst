Sharing development
===================

Refs:
    :gitdoc:`git manual:Sharing development with others
    <user-manual.html#sharing-development>`

..  index:
    single:git; pull

Git Pull
--------

Refs:
    :gitdoc:`git pull(1) <git-pull.html>`,
    :gitdoc:`git fetch(1) <git-fetch.html>`

To pull your master branch with the remote tracking branch in
*origin*:
::

    $ git pull origin master

It is equivalent t:
::

    $ git fetch
    $ git merge origin/master

..  index:
    single git; pull --rebase

Git pull |min2|\ rebase
-----------------------

Refs: `gitolite: git-pull --rebase
<http://gitolite.com/git-pull--rebase.html>`_

Instead of merging the remote repository after fetching, ``git pull --rebase``
rebase  the current branch on top of the upstream branch. But it uses
relog information to avoid to rebase commits previously pulled from
the remote.

When you develop in a topic branch, you usually want to do ``git-pull
--rebase`` instead of a mere pull, to get a clean patch above master.

If master has progressed in an incompatible way with your work
you may have to fix the conflict during rebase.

If you want to always do a rebase when pulling you can set the config
option ``branch.<name>.rebase`` to true.

If you need to do a simple pull (fast-forward or merge) once you have
to use the option ``--no-rebase`` to annihilate the effect of your
config.

See the options ``branch.autosetuprebase`` and ``pull.rebase`` in
:gitdoc:`git config(1) <git-config.html>` to set automatic rebase
to all branches, or a wide category of branches.


..  index:
    single: git; push

Git push
--------

Refs:
    :gitdoc:`git push(1) <git-push.html>`,
    :gitdoc:`git manual: Pushing changes
    <user-manual.html#pushing-changes-to-a-public-repository>`

..  index:
    single: remote branch; create

Pushing to a registered remote
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have a register remote *origin*

::

    $ git remote
    origin

You can push a branch to it with

::

    $ git push mybranch origin

It will push all references in the branch *mybranch* to a branch with same name on
*origin*. If the remote *mybranch* does not exist it is created.

If *mybranch* has a configured :ref:`upstream branch<remote_tracking>` that ou can see
with
xs
::

    $git branch -vv --list mybranch

or

::

    $ git config branch.mybranch.merge

you can omit the remote and do:
::

    $ git push mybranch

If may branch is the current checked out branch, you can also omit the branch and do:
::

    $ git push

The remote branch is supposed to be an ancestor of the local one, and git refuses to
push if it is not the case, sometime, like after a rebase, you want to override thsi
restriction *(tis is to avoid if the remote is shared with other developers)* and you
do:
::

    $ git push --force mybranch



Creating a new remote branch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To push a *feature* branch to the *origin* repo with the same name:
::

    $ git push origin feature

The origin should be :ref:`configured as remote <remote_config>`.

If you want to track the changes of the remote branch, as explained in the
:ref:`Remote tracking section <remote_tracking>` you can
do:
::

    $ git push --set-upstream origin feature

You can also later change the remote tracking setting as :ref:`explained in this section
<remote_tracking>`.


You can also push it with a distinct name
::

    $ git push origin feature:joe_new_feature


..  index:
    pair: remote branch; delete

Delete a remote branch
~~~~~~~~~~~~~~~~~~~~~~

If you want later to delete the remote branch:
::

    $ git push origin :feature

or:
::

    $ git push --delete origin feature

..  index:
    pair: remote branch; move


Rename a remote branch
~~~~~~~~~~~~~~~~~~~~~~

If you want to rename a branch and its remote, say rename *oldbranch* to *newbranch*
you first rename on the local machine, and push it to the remote, then delete the old
remote:
::

    $ git branch -m oldbranch newbranch
    $ git push --set-upstream origin newbranch
    $ git push origin :oldbranch

An other will still see the local copy of ``remotes/origin/oldbranch``, to clean the
remote list do on this client
::

    $ git remote prune origin


..  index:
    single: tag; share
    single: tag; remote

.. _remote_tags:

Sharing Tags
------------

..  index:
    single:git; ls-remote
    single:git; fetch
    single:git; show-ref

Listing remote tags
~~~~~~~~~~~~~~~~~~~

Refs:
    :gitdoc:`git ls-remote(1) <git-ls-remote.html>`,
    :gitdoc:`git fetch(1) <git-fetch.html>`,
    :gitdoc:`git show-ref(1) <git-show-ref.html>`


We have seen how to :ref:`List local tags <tag_info>`, but the remote
repository can have a different set of tags. Usually we want to have
the tags of a remote origin bare repository, but if we include also in
our remotes the repo of a fellow developper it is usually inapropriate
to import all his tags.

To list the remote tags we use::

    $ git ls-remote --tags somerepo
    da4412bf6edd0d99c8149a205d78b6a0a6f8f091	refs/tags/torepair
    4a7f903017e22d0effb4b233f99548fd3abdac11	refs/tags/torepair^{}
    17b3e9b93faf30e59fe9910de2da208d018bba7a	refs/tags/v1
    4a7f903017e22d0effb4b233f99548fd3abdac11	refs/tags/v1^{}

Here the objects ``da4412b`` and ``17b3e9b`` are the tags object, and
``4a7f9030`` is  the commit pointed to by the two tags. The notation
``<rev>^{}`` dereference the tag recursively until a non-tag
object is found (see :gitdoc:`gitrevisions <gitrevisions.html>`)

The lightweight tags are also shown by this command. But lightweight
tags are not object, but only an alias for a commit, so only the
commit appear in the list.

To differenciate between lightweight and anotated tags you can
``git-cat-file -t <tag>`` it output tag for a rag object, but commit
for a lightweight tag.

Tags are fetched by default, unless you spectify ``--no-tags``
or have set the option ``remote.<name>.tagopt``. If you don't change defaults
you get the remote tags from the repository you fetch from; but
they are not pushed by default, that allow to have tags for local use
in your repo.

You can also use::

    $  git ls-remote --tags .

to get the *local* tags.

It is equivallent to::

    $ git show-ref --tags --dereference

..  index:
    pair: tag; fetch

Fetching remote tags
~~~~~~~~~~~~~~~~~~~~
Refs:
    :gitdoc:`git fetch(1) <git-fetch.html>`,
    :gitdoc:`git show(1) <git-show.html>`.

To fetch an individual remote tag::

    $ git fetch somerepo tags/torepair

Then you can examine it with :ref:`git tag <tag_info>`, or with::

    $ git show torepair
    tag torepair
    Tagger: Some Body <some.body@git.org>
    Date:   Sun Oct 19 11:45:13 2014 +0200

    defective commit

    commit 4a7f903017e22d0effb4b233f99548fd3abdac11
    ........

..  index:
    pair: tag; push

Pushing Tags to remote
~~~~~~~~~~~~~~~~~~~~~~

Refs:
    :gitdoc:`git push(1) <git-push.html>`.

To push an individual tag::

    $ git push origin tags/v1
    To git@github.com:me/testrepo.git
     * [new tag]         v1 -> v1

To push and include *all* tags::

    $ git push --tags origin
    Counting objects ...
    ...
    [new tag]         v1 -> v1

..  index:
    single:tag; modify

Changing the tag message
~~~~~~~~~~~~~~~~~~~~~~~~

Modifying a shared tag is strongly discouraged by
:gitdoc:`git-tag(1) <git-tag.html>`. But changing only the message
while keeping an unchanged date and commit reference is not too
harmfull, but you have to know that your change will not be
automatically propagated to people that pull from you.

If you want to keep the original date use::

    GIT_COMMITTER_DATE="2014-09-28 11:52" git tag -a -f \
    -m "new description" tag v0.90 v0.90

..  |min2| unicode:: 0x2d 0x2d
