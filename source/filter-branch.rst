..  _filter_branch:

Filter branch
=============

This chapter describe some uses of ``git filter-branch`` before using
it you should be aware that this command is destructive, and even the
untouched commits end up with different object names so your new
branch is separate from the original one. If ever you repository was
shared anyone downstream  is forced to manually fix their history,
by rebasing all their topic branches over the new HEAD.
More details in
:gitdoc:`git-rebase(1) - recovering from upstream rebase
<git-rebase.html#_recovering_from_upstream_rebase>`

When filtering branches the original refs, are stored in the namespace
``refs/original/``, you can always recover your work from there, but if
you want to delete the previous state, after checking the new one is
coherent, you need to delete these refs otherwise the original object
will not be garbage collected.

.. _backup:

If you want to make experiments without the trouble to recovering from
``refs/original`` you should get a  copy of your repository
with::

     git clone path_of_origin path_of_copy
     cd path_of_copy
     git branch --unset-upstream
     git reset --hard





References
----------

-   The main reference is :gitdoc:`git documentation: filter-branch
    <git-filter-branch.html>`
-   It is introduced in S. Chacon Pro-Git `Rewriting History chapter
    <http://git-scm.com/book/ch6-4.html#The-Nuclear-Option:-filter-branch>`_
    and `in Maintenance and Data Recovery - removing objects
    <http://git-scm.com/book/ch9-7.html#Removing-Objects>`_.


Removing an object
------------------

This can be done with ``--tree-filter`` or ``-index-filter`` as the
second one does not check out the tree, it is a lot quicker.

When filtering branches you may remove all the changes introduced by
some commit and ends up with empty commit. Some of these emty commits
are useful because they have many parents, i.e. they record a merge.

To avoid such situation you can use ``--prune-empty`` (but it is
incompatible with ``--commit-filter``.

Your command will be::

  git filter-branch --prune-empty --index-filter  'git rm --cached --ignore-unmatch badfile' HEAD

Here the ``git rm`` command has the option ``--cached`` since we are
working on the index and ``--ignore-unmatch`` because the file can be
absent in the index for some commits, like those anterior to the first
occurrence of the file.

After that your history no longer contains a reference to ``badfile``
but all the ``refs/original/branch`` and the reflog still do. You have
to options, if you have no backup you should do::

      git clone file:///path/to/cleanrepo

It is quick since done with hardlinks and the clone will not have the
removed objects.

If you have yet done a backup as proposed :ref:`above <backup>`
you can clean  before repacking, first the original refs::


    git for-each-ref --format='%(refname)' refs/original | \
        xargs -n 1 git update-ref -d


Then your logs::

  git reflog expire --expire=now --all

And you garbage collect all unreferenced objects with::

  git gc --prune=now
