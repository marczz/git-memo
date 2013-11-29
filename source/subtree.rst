Git Subtree
===========


Reference `git-subtree(1) in contrib directory
<http://git.kernel.org/cgit/git/git.git/plain/contrib/subtree/git-subtree.txt>`_

Git subtree is an alternative from submodules, but while submodules
are mainly aimed at putting in a directory of your repository an other
project maintained somewhere else, and keeping the foreign repo in
sync; git-subtree allow to keep separate a subproject and allow
bidirectional collaboration between your main repo and the subprojects.

Subtree split
-------------

First you split a new branch from your history containing only the
subtree rooted at <prefix>. The new history includes only the commits
(including merges) that affected <prefix>, and each of those commits
now has the contents of <prefix> at the root of the project instead of
in a subdirectory.
::

    git subtree split -P <prefix>  -b <name-of-new-branch>

Then you create a new repo <new-repo> for the splitted branch::

  cd <new-repo>
  git init

and in this repository you pull your new branch::

  git pull </path/to/old-repo> <name-of-new-branch>

or if you want to get it as master branch::

  git fetch </path/to/old-repo> <name-of-new-branch>
  git checkout -b master FETCH_HEAD

You probably want anymore the split branch in your original directory,
if you still want to keep the history (because it has influenced the
past history of other files) you just remove in the original repo.::

  git rm -rf <prefix>
  git commit -m'moved new development of <name-of-new-branch>
      in a new repository'

If you truly want to extract from the root of history of the original
repository the split branch, and you can afford the
:gitdoc:`cost of recovering from upstream rebase
<git-rebase.html#_recovering_from_upstream_rebase>`
