Merge and Patch
===============

.. highlight:: bash

.. index:: !merge

Merging
-------


-  The two parents and their common  base form the three stages of the
   merge:

   -  ``git show :1:file`` is the ``base`` we have the difference as
      ``git diff -1`` or ``git diff --base``
   -  ``git show :2:file`` is ``ours`` we have the difference as
      ``git diff -2`` or ``git diff --ours``
   -  ``git show :3:file`` is ``theirs`` we have the difference as
      ``git diff -3`` or ``git diff --theirs``

-  In a failed merge an unmerged path `file` contains the combined
   unmerged file, and `git diff` will show a *combined diff* which show
   differences with the two parents.

-  ``git log --merge -p <path>`` will show diffs first for the HEAD
   version and then the MERGE_HEAD version.

-  ``git log ..otherbranch`` show the changes that will be merged
   in the current branch.
-  ``git diff ...otherbranch`` is the diff from common ancestor (merge
   base) to graphical representation of the branches since they were merged last time.

-   ``git mergetool`` launch a graphical mergetool which will work you through the merge.


Refs:

-   :gitdoc:`git-merge.html`

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


Refs:
-   `Applying / Merging Changes From One Git Repository To Another
<http://factore.ca/on-the-floor/51-applying-merging-changes-from-one-git-repository-to-another>`_
-   `git – applying patches
<http://www.winksaville.com/blog/linux/git-applying-patches/>`_
-   `Pro Git: Maintaining a Project
<http://progit.org/book/ch5-3.html>`_
-   `gitready: pick out individual commits
<http://gitready.com/intermediate/2009/03/04/pick-out-individual-commits.html>`_
compare ``git cherry-pick``, ``git format-patch`` with ``git am``, or with ``git apply``,
and ``git merge``.

Subtree-merge
-------------

Subtree merge is a very useful strategy to import a subtree from an
other repository in a branch of our repository.

It is presented in a very concise way in
:gitdoc:`Git howto: How to use the subtree merge strategy
<howto/using-merge-subtree.html>`
from which I extract the following code that illustrates the
merging of a project **B** in the subdirectory  ``dir-B``
of our project.

.. code::

   $ git remote add -f Bproject /path/to/B
   $ git merge -s ours --no-commit Bproject/master
   $ git read-tree --prefix=dir-B/ -u Bproject/master
   $ git commit -m "Merge B project as our subdirectory"

To follow the changes in the B project you use::

  $ git pull -s subtree Bproject master

This strategy ia applied to a bigger example in
`GitHub Help: Working with subtree merge
<https://help.github.com/articles/working-with-subtree-merge>`_


A different technique, doing the merge after the read-tree is in
Scott Chacon `Pro Git: Subtree Merging
<http://git-scm.com/book/en/Git-Tools-Subtree-Merging>`_
and used in this `Git Subtree Workflow
<https://github.com/ande3577/Git-Subtree-Workflow-Proposal/wiki/Subtree-Based-Workflow>`_
by David S Anderson and in `Git Subtree Merge –The Quick Version
<http://www.codeproject.com/Articles/562950/GitplusSubtreeplusMergeplus-e2-80-93TheplusQuickpl>`_
by  John Atten.

..

   Local Variables:
   ispell-local-dictionary: "english"
   End:
