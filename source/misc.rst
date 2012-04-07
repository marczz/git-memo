Miscellaneous operations
========================
.. highlight:: bash

Well formed commits
-------------------

See `A Note About Git Commit
Messages <http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html>`_
to summarize:

#. Short (50 chars or less) summary
#. Blank line.
#. More detailed explanatory text wrapped to about 72 cols.
#. Further paragraphs come after blank lines.
#. You can use bulleted text with a hyphen or asterisk preceded by a
   single space
#. Use the present tense.





Adding an empty commit at root of a branch.
-------------------------------------------

::

    $ git symbolic-ref HEAD refs/heads/newroot
    $ git clean -dxf
    $ git commit --allow-empty -m 'root commit'
    $ git rebase newroot master

-  repeat for other branches you want to rebase on the same newroot
-  You can then move to some branch and ``git branch -d newroot``

switching branches without doing a checkout
-------------------------------------------

::

    git symbolic-ref HEAD refs/heads/otherbranch

For every work on the branch you must get a fresh index with:

::

    git reset

The script ``git-new-workdir`` in the contrib directory creates a symlink to a repository,
optionally with a new checked out branch::

    git-new-workdir <repository> <newworkdir> [<branch>]

..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
