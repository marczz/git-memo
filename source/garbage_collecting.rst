..  _garbage_collection:

Garbage Collecting
==================

Refs:
  :gitdoc:`Git user manual: dangling-objects
  <user-manual.html#dangling-objects>`

  :gitdoc:`git-gc(1) <git-gc.html>`, :gitdoc:`git-fsck(1)
  <git-fsck.html>` :gitdoc:`git-prune(1) <git-prune.html>`,
  :gitdoc:`git-pack-refs(1) <git-pack-refs.htm>`,
  :gitdoc:`git-repack(1) <git-repack.html>`,
  :gitdoc:`git-prune-packed(1) <git-prune-packed.html>`,
  :gitdoc:`git-reflog(1) <git-reflog.html>`.

  :progit:`Maintenance and Data Recovery
  <Git-Internals-Maintenance-and-Data-Recovery>`
  has some examples of repository cleaning.

In a repository, some object become unreachable by any refs, during
some operations, like deleting a branch, deleting an unreachable tag,
rebasing, expiring entries in the
:gitdoc:`reflog <git-reflog.html>` ...
These unreachable objects can be reported by :gitdoc:`git-fsck
<git-fsck.html>`.

.. index: git fsck

git fsck
--------

If you use the default options::

  $ git fsck
  dangling tree 4df800e6a1c6ba57821e4e20680566492bbb5e81

report only the dangling objects.

You can add the object unreachable by any reference with::

  $ git fsck --unreachable
  unreachable tree 6e946512b1b4841dff713bb65e78a8ddbf0171d3
  unreachable tree 4df800e6a1c6ba57821e4e20680566492bbb5e81
  unreachable tree 125a284a7a428d91e199a3c9d7f7a834613d1d13

Both commands above use also the reflog. If you want to look at the
object which are dangling except from the reflog or object unreachable
except the reflog you do::

  $ git fsck --dangling --no-reflogs
  dangling commit 2b21a6a8e775a43eafbe1b9e8b1fb5debe77b2ee
  dangling commit c3e1ecc9f0e7a67c9a05db92d6c6548a1c965830
  dangling commit 1702677e8ca31c0536745b36280397457bf20002
  dangling commit b746f71fa14416e22920f38b8439bbb1972481a3
  dangling commit 408c05cafd2bfdb861676dd54a0ed083f7fdfcaa
  dangling commit 6c92fe1d7f47e397c3ccd2713ddd9c3b8239d1c8
  ...
  $ git fsck --unreachable --no-reflogs
  unreachable commit 2b21a6a8e775a43eafbe1b9e8b1fb5debe77b2ee
  unreachable tree 39610cc0e1126a2bb73cb3345b9228f1ccb374d9
  unreachable commit c3e1ecc9f0e7a67c9a05db92d6c6548a1c965830
  unreachable tree e981cec3e50cf10b59b544d86be681a7030cb0a6
  ...


Automated garbage collection with ``gc --auto``.
------------------------------------------------

Git stores the objects either one by one as *loose* objects, or with a
very efficient method in *packs*. But if the size of packs is a lot
lesser than the cumulated loose objects, the access time in a pack is
longer. So git does not pack the objects after each operations, but
only check the state of the repository with ``gc --auto``.

``gc --auto`` look if the number of loose objects exceeds ``gc.auto`` (default
6700) and  then run ``git repack -d -l`` which in turn run
:gitdoc:`git-prune-packed <git-prune-packed.html>`.  setting
``gc.auto`` to 0 disables repacking. When the numbers of packs is
greater than ``gc.autopacklimit``  (default 50, 0 disable it)
``git gc --auto`` consolidates them into one larger pack.

When doing a ``git gc --aggressive`` the efficiency of :gitdoc:`git-repack <git-repack.html>` depends
of gc.aggressiveWindow (default 250).

``git gc --auto`` also pack refs when ``gc.packrefs`` has its default
value of ``true``, the refs are then placed in a single file
``$GIT_DIR/packed-refs``, each modification of a ref again create a
new ref in ``GIT_DIR/refs`` hierarchy that override the corresponding
packed ref.

The ``gc`` command also run by default with the ``--prune`` option,
which clean unreachable loose objects that are older than
``gc.pruneExpire`` (default 14 days). If you want to use an other date
you have to add ``--prune=<date>``, ``--prune=all`` prunes loose
objects regardless of their age. *Note that it may not be what you want
on a shared repository, where an other operation could be run
concurrently.*

An unreachable commit is never pruned as long it is in a
:gitdoc:`reflog(1) <git-reflog.html>`, but
``gc --auto`` run ``git reflog expire`` to prune reflog entries that are
older than ``gc.reflogExpire`` (default 90 days) or unreachable
entries older than ``gc.reflogExpireUnreachable`` (default 30 days).
These values can be also configured for each individual ref see
:gitdoc:`git-config(1) <git-config.html>`.

Records of conflicted merge are also kept ``gc.rerereresolved``
(default 60 days) or ``gc.rerereunresolved`` (default 15 days) for
unresolved merges.

Forced garbage collection.
--------------------------

You can have an idea of the state of your repository by issuing
``git count-objects -vH``


After some operation that creates a lot of unreachables objects, like
rebasing or filtering branches you
may want to run ``git gc`` without waiting the three months of
expirability. This is also a necessity if you have to delete an
object, now unreachable, but that contains some sensible data, or a
very big object that was added and then deleted from the history (see
the :ref:`filter branch section <filter_branch>`).

As the operation is recorded in the reflog, you expire it with::

  git reflog expire --expire=now --all

And you garbage collect all unreferenced objects with::

  git gc --aggressive --prune=now
