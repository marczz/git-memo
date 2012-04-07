Merge and Patch
===============

.. highlight:: bash

.. index:: !merge

Merging
-------

-  In a failed merge an unmerged path `file` contains the combined
   unmerged file, and `git diff` will show a *combined diff* which show
   differences with the two parents.

   The two parents and their common  base form the three stages of the
   merge:

   -  ``git show :1:file`` is the ``base`` we have the difference as
      ``git diff -1`` or ``git diff --base``
   -  ``git show :2:file`` is ``ours`` we have the difference as
      ``git diff -2`` or ``git diff --ours``
   -  ``git show :3:file`` is ``theirs`` we have the difference as
      ``git diff -3`` or ``git diff --theirs``

Submitting patches
------------------

Use :gitdoc:`git format-patch <git-format-patch.html>`

::

   $ git format-patch origin

Or with a commit range::

   $ git format-patch before..end

Produces in the current directory a sequence of patches, with names
from each commit log.

You can apply them one by one or as a whole with::

    $ git am patches.mbox

git-am will fail if the patch does not apply, you can instead use a 3
ways merge with::

    $ git am -3 patches.mbox

You can fix conflicts, add the fixed files to the index and commit with::

    $ git am --resolved


`Applying / Merging Changes From One Git Repository To Another
<http://factore.ca/on-the-floor/51-applying-merging-changes-from-one-git-repository-to-another>`_

`git – applying patches
<http://www.winksaville.com/blog/linux/git-applying-patches/>`_

`Pro Git: Maintaining a Project
<http://progit.org/book/ch5-3.html>`_

`gitready: pick out individual commits
<http://gitready.com/intermediate/2009/03/04/pick-out-individual-commits.html>`_
compare ``git cherry-pick``, ``git format-patch`` with ``git am``, or with ``git apply``,
and ``git merge``.
