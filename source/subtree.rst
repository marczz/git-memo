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

Extracting the branch
~~~~~~~~~~~~~~~~~~~~~

First you split a new branch from your history containing only the
subtree rooted at <prefix>. The new history includes only the commits
(including merges) that affected <prefix>.
The commit in which where previously rooted in the subdirectory
<prefix> are now  at the root of the project.
::

    git subtree split -P <prefix>  -b <name-of-new-branch>

Importing the branch in another repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the simpler alternative,
in the repository you pull your new branch::

  git pull </path/to/old-repo> <name-of-new-branch>



Using the branch as master branch of a new repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Then you create a new repo <new-repo> for the splitted branch::

  cd <new-repo>
  git init

You fetch the original branch and it is referred as a detached
``FETCH_HEAD``.

::

    git fetch </path/to/old-repo> <name-of-new-branch>

If you want to get it as master branch::

  git checkout -b master FETCH_HEAD

Rebasing the branch in the new repo.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

I show the example of rebasing on the top of the master, you can
easily adapt it to an other rebase.

In this example I have an old repo *unix-memo* and I want to extract
some part split as *git-extract* and include it on the top of the repo *git-memo*.

In *git-memo* I fetch the split branch::

  $ git fetch ../unix-memo/ git-extract
  ....
  From ../unix-memo
  * branch            git-extract  -> FETCH_HEAD

I can examine the situation::

  $ git log FETCH_HEAD
  $ gitk master FETCH_HEAD

I want to rebase, but as some of my commits refer to the old repo
*unix-memo*, I also want to amend them.

::

    $ git rebase --interactive --onto HEAD --root FETCH_HEAD

I then mark as *edit* the commit I want to amend, then when the rebase
is complete::

  $ git rebase --continue
  Successfully rebased and updated detached HEAD.

Now this HEAD is detached I can show it with::

  $ git rev-parse HEAD
  bb7d9a2a871e39e7f2f262f805221a41a4354f98

But it is easier to work on it with a temporary tag, then fast forward
above *master*.

::

    $ git tag detached
    $ git checkout master
    Previous HEAD position was bb7d9a2...
    Switched to branch 'master'
    $ git merge --ff-only detached
    Updating 755786a..bb7d9a2
    Fast-forward
    ....

    $ git tag -d detached
    Deleted tag 'detached' (was bb7d9a2)


Getting rid of the split branch.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You probably don't want anymore the split branch in your original directory,
if you still want to keep the history (because it has influenced the
past history of other files) you just remove in the original repo.::

  git rm -rf <prefix>
  git commit -m'moved new development of <name-of-new-branch>
      in a new repository'

If you truly want to extract from the root of history of the original
repository the split branch, and you can afford the
:gitdoc:`cost of recovering from upstream rebase
<git-rebase.html#_recovering_from_upstream_rebase>`

adding a subtree to a project
-----------------------------


We have a main project, with some commits in the master branch, and a
remote project that we add to our repo with::

    git remote add -f git-memo https://github.com/marczz/git-memo.git

Now our repository look like this.

..  digraph:: main

    size = 3.8;
    rankdir=RL;
    node [shape=circle, style=filled, color=aquamarine,/* fontsize=9,*/ height=0.3,
       width=0.3];
    g [shape=ellipse, label="g: master", color=magenta];
    g -> f -> e -> d -> c;
    v [shape=ellipse, label="v: git-memo/master", color="magenta"];
    v -> u -> t -> s -> r;


We want to add the remote in a subtree of our main repo rooted at
``./gitmemo``. We have to choose how we will mix the commits of the
two branches, either they can be intermixed, or we can squash all the
commits in one, before adding them. As the first option would mixe in
the commit timeline completely foreign works, we choose to squash with:

::

    git subtree add --prefix gitmemo --squash git-memo master

And we obtain:

..  digraph:: sub

    /*size = 5;*/
    rankdir=RL;
    node [shape=circle, style=filled, color=aquamarine, fontsize=7, height=0.2,
       width=0.2, margin="0.025,0.025"];
    nodesep=0.2;
    g [shape=ellipse, label="g: master", color=magenta];
    aa [shape=box,
       label="squashed gitmemo/\n from v",
       color="magenta"];
    g -> f -> e -> d -> c;
    g  -> aa [weight=10];
    v [shape=ellipse, label="v: git-memo/master", color="magenta"];
    v -> u -> t -> s -> r;
    aa -> v [style = "invis"];

Later you can pull change from the remote repository with::

  git subtree pull --prefix=git --squash git-memo

If you have one more commit on the remote, your commit tree is now
like this:

..  digraph:: sub

    /*size = 5;*/
    rankdir=RL;
    node [shape=circle, style=filled, color=aquamarine, fontsize=7, height=0.2,
       width=0.2, margin="0.025,0.025"];
    nodesep=0.2;
    h [shape=ellipse, label="h: master", color=magenta];
    aa [shape=box,
       label="squashed gitmemo/\n from v",
       color="magenta"];
    ab [shape=box,
       label="squashed gitmemo/\n from w",
       color="magenta"];
    h -> g -> f -> e -> d -> c;
    g -> aa /*[weight=5]*/;
    h -> ab /*[weight=5]*/;
    ab -> aa;
    w [shape=ellipse, label="w: git-memo/master", color="magenta"];
    w -> v -> u -> t -> s -> r;
    aa -> v [style = "invis"];
