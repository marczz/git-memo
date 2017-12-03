Fixing errors
=============

..  index:: amend
..  index:: rebase

Amend a commit
--------------
..  index:: single: git; commit --amend

To amend the **last** commit::

    $ #correct the errors, then
    $ git add file
    $ git commit --amend
    $ # fix the commit message if necessary

To amend a past commit on a clean working directory, you need to come
back to the erroneous commit, fix it and :ref:`rebase:<rebase>` the latter
commits on the the new fixed state.

An example adapted from :gitdoc:`user-manual: Rewriting a
single commit <user-manual.html#rewriting-one-commit>`

::

    $ git log --stat -- badfile
    $ git tag bad mywork~5
    $ git checkout bad
    $ # make changes here and update the index
    $ git commit --amend
    $ git rebase --onto HEAD bad mywork
    $ git tag -d bad

The use of the tag is optional you can also do

::

    $ BAD="7c2c66b71b"
    $ git checkout $BAD
    $ # make changes here and update the index
    $ git commit --amend
    $ git rebase --onto HEAD $BAD mywork

When you want to amend past commits with changes in the working tree,
you cannot checkout the past commit because the worktree is dirty.

You can use
:gitdoc:`git stash <git-stash.html>` to come back to the original
worktree or work in a linked temporary working directory::

    $ git-new-workdir <repository> <temporary_workdir>
    $ cd <temporary_workdir>
    # find the commit to amend
    $ git log --stat
    $ BAD="<commit>"
    $ git reset --hard $BAD
    # edit the wrong file may be copying from <repository>
    $ git add <changed file>
    $ git commit --amend
    $ git rebase --onto HEAD $BAD mywork


You can also use ``git rebase --interactive`` as indicated
:ref:`in the rebase section <rebase_interactive>`.
where you find an
:ref:`example of fixing an old error with interactive rebase
<interractive_rebase_example>`.

..  index::
    squash,fixup
    single: git;commit --squash
    single: git;commit --fixup
    single: git;rebase --autosquash

Commit a fixup and squash.
--------------------------

The :ref:`interactive rebase <rebase_interactive>` \  can be made a lot
simpler for fixing errors with the ``git commit --fixup`` or
``git commit --squash command``.

On a clean worktree, or cleaned by a ``git stash``, you change your
erroneous file(s) and commit it (them) with
::

    $ git commit --fixup=a0b1c2d3

Where you give the erroneous commit number, then you *fixup* the error
with:
::

    $ git rebase --interactive --autosquash a0b1c2d3^

The first command just use the original message prefixed by
``fixup!``, the second one squash the original and next commit
discarding the message of the fixup commit.

You can also do a simple commit and begin your message by ``fixup!``
followed by an initial section of the original commit message.

If instead of *fixup* you use *squash* the process is similar but the
commit message for the folded commit is the concatenation of the
messages of the first commit and of those with the *squash* command.

..  _split_commit:

splitting a commit
------------------

..  index:: pair: split; commit

ref: :gitdoc:`git-rebase: splitting commits
<git-rebase.html#_splitting_commits>`,
:gitdoc:`git gui <git-gui.html>`

To split a commit, you first rebase interactively to the commit or one
of its ancestor
::

    $ git rebase -i <commit>

Then you mark the commit with the action *edit*, and when it comes to
editing::

    $ git reset HEAD^

Then you can examine the status with::

    $ git status

and add some files and stage the appropriate hunks. It can be easy to
use::

    $ git gui

to commit the appropriate hunks in individual commits

Then you can as usual do::

    $ git rebase --continue.

..  index::
    broken repository
    packed objects
    single:git;verify-pack
    single:git;unpack-objects
    single:git;fsck


Broken repository
-----------------
I repeat a warning so often given, so often forgot : *Keep a copy of
your repository before making any destructive change on
the repository internals*.

It can happen that a repository become corrupted. When doing any
operation that check the repo structure, often when trying to commit,
you have this error::

    error: invalid object 100644 db4f06caa1f321c01c667572f08449e8ebfc0713 for 'mystory.txt'


When you do a structure check in the repo you have::

    $ git fsck --full
    Checking object directories: 100% (256/256), done.
    Checking objects: 100% (7448/7448), done.
    dangling commit e733d3ed3e27725db037078e56006c54fe3b6095
    dangling commit 35b7ea4d2648bd3d2cbfeaf90d2ddb0742211517
    dangling commit 323ebd233c5fa597cbd354ffae1ecc30643605ad
    missing blob db4f06caa1f321c01c667572f08449e8ebfc0713
    dangling commit bb5152e989824799cc7a0de8e4753b411c17feb5
    dangling commit 90d7899f21ec19abdc59764162a4161a7e9451f8

The dangling commits are not an error, there are just objects that are
not in any branch, may be because you have rebased, after some time
they will go away during a garbage collect.

The missing blob imply that the named blob is neither in
``.git/objects/`` namely with this hash in ``.git/objects/d4/``
or in a pack inside ``.git/objects/pack`` there are two ways of fixing
it either find it in another repo or recreate it.

To find the object in a copy of your repo you can also look at the
``objects/d4/`` repo; but it it gives only loose object that are not
yet packed. You can see the blobs in a pack by::

    $ git show-index .git/objects/pack/pack-ef11586b8de074c2b62c5a148b16096806adeb1c.idx | cut -f 2 -d ' '

or::

    $ git verify-pack -v .git/objects/pack/pack-ef11586b8de074c2b62c5a148b16096806adeb1c.idx |\
    grep ' blob ' | cut -f 1 -d ' '

but if you just want to check if the pack contains your object::

    $ git show-index .git/objects/pack/pack-ef11586b8de074c2b62c5a148b16096806adeb1c.idx |\
    grep db4f06caa1f321c01c667572f08449e8ebfc0713

Then you can unpack the pack to make the object loose again by moving the pack
out of the repo and issuing::

    $ git unpack-objects < .git/objects/pack/pack-ef11586b8de074c2b62c5a148b16096806adeb1c.pack

If the pack is corrupt you need to add the ``-r`` option.

If you don't succeed to find a copy of the missing object there is an
other way, that is to recreate the object. I will not deal with it
here, because it is very well explained in
`How to fix a broken repository? from the Git FAQ
<https://git.wiki.kernel.org/index.php/GitFaq#fix-broken-repo>`_.

And if nothing work and this information is lost, you can keep going
by `removing the broken refs
<https://git.wiki.kernel.org/index.php/GitFaq#How_to_remove_all_broken_refs_from_a_repository.3F>`_.

References:
:gitdoc:`git core tutorial: Packing your repository
<gitcore-tutorial.html#_packing_your_repository>`,
`Git Pro: Packfiles
<https://git-scm.com/book/uz/v2/Git-Internals-Packfiles>`_,
:gitdoc:`git-fsck(1) <git-fsck.html>`,
:gitdoc:`git-show-index(1) <git-show-index.html>`,
:gitdoc:`git-verify-pack(1) <git-verify-pack.html>`,
:gitdoc:`git-unpack-objects(1) <git-unpack-objects.html>`.
