Rebase
======

The introduction is a condensed presentation of examples in
:gitdoc:`git-rebase(1) <git-rebase.html>`

Rebase a topic
--------------

Suppose you have this situation:

::

         A---B---C---D---E topic
        /
    D---E---A---F master

And you are on ``topic``.

Then the command:
::

    git rebase master

will apply on ``master`` all commit in ``topic`` that where not yet
there, *A will be skipped,* and the result is:
::

                  B'--C'---D'---E' topic
                 /
    D---E---A---F master

If you where not on ``topic`` but on ``master`` then you shoul have
use instead:
::

    git rebase master topic

That will do the successive operations:
::

    git checkout topic
    git rebase master

If you want only to use the commits starting with ``D`` you use:
::

    git rebase --onto master C topic

to get:
::

                  D'---E' topic
                 /
    D---E---A---F master

In all these example the rebase let you on ``topic``.
