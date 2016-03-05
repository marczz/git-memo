Git annex
=========

Usual operations
----------------

More on this topic in :annex:`Git annex walkthrough <walkthrough/>`.

Adding an annex to git repository.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

     $ git annex init


Adding one file.
~~~~~~~~~~~~~~~~
::

    $ git annex add myfile
    add myfile ok
    (recording state in git...)
    $ git commit  -m"myfile added to annex"

When you add a file to the annex the content itself is stored in
git-annex's backend, ``.git/annex/`` and the symlink is added to
git.

When you commit it, a symlink to the content is committed to git.



Adding files in directory.
~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    $ git annex add .
    add foo.txt
    add bigdoc.pdf
    $ git commit -a -m added

all the unregistered files are added to the annex; but
if  ``annex.largefiles``  is  configured only matched files are
added to the annex other files are added directly to the git repository.

Removing the symlink from git.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    $ git rm myfile

The annexed content is still in the annex, but git mark the file as
removed, you can checkout the previous link or revert, and you
file is back again.

Droping the file content.
~~~~~~~~~~~~~~~~~~~~~~~~~
::

    $ git annex drop myfile

Thi will remove myfile from your annex, but only if some exemplar
exist in a remote annex. The symlink registered in git is
unchanged, but will become broken, and is repaired if you do again
a ``git annex get``.

You can force to drop a file without checking a backup with:
::

    $ git annex drop --force myfile

By default annex try to keep one exemplar of each file in some
repository, but you can tweak the number with *numcopies*.

Modifying files.
~~~~~~~~~~~~~~~~
In order to modify a file you have to replace the
symlink by a copy of the file, it is the *unlock* operation.
::

    $ git annex unlock myfile

Then you can modify your file, and register the modification with
git.
::

    $ git commit myfile -m"new version of myfile"

and ``git annex`` will record the new file, and git commit the
change, the file stay in unlocked mode until you run
::

   $ git annex lock myfile

If before comitting you add your new version of the file to the
annex.
::

 $ git annex add myfile

annex lock it again by replacing it by a symlink.

Refs: :annex:`unlocked files <tips/unlocked_files/`,
    :annex:`git-annex-unlock`, :annex:`git-annex-lock`,
    :annex:`git-annex-add`.


Defining which files are stored in annex.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
If you put in your ``.gitattributes`` file:
::

    * annex.largefiles=((largerthan=100kb)and(not(mimetype=text/*)))or(mimetype=application/*)

or set your git config with
::

    $ git config annex.largefiles '(largerthan=100kb and not mimetype=text/*) or mimetype=application/*'

Then when adding files
::

    $ git annex add .
    add foo.txt (non-large file; adding content to git repository) ok
    add bigdoc.pdf
    $ git commit -a -m added

Refs: :annex:`largefiles <tips/largefiles>`

Which annexed files are no longer used.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    $ git annex unused
    unused . (checking for unused data...) (checking master...)
    Some annexed data is no longer used by any files:
    NUMBER  KEY
    1       SHA256E-s27--8c20c91f834dabcf6b9ba5de670572136ce8382b2ebf897fa86c8d2d71f081ef.txt
    2       SHA256E-s5--819f04e5706f509de5a6b833d3f561369156820b4240c7c26577b223e59aae97.txt
    3       SHA256E-s18--12a4f82f1286e21ee87c63a14efb3e96d25ff26f82128377b87c8c34fc071499.txt

As the key is part of the commit message you can inspect which
symlink in some commit had this key for target.
::

    $ git log --name-status -S'SHA256E-s5--819f04e5706f509de5a6b833d3f561369156820b4240c7c26577b223e59aae97.txt'

If you want to drop extra copies of unused files you can do
::

    $ git annex dropunused 1

to drop the file n° 1 or
::

    $ git annex dropunused

to drop all unsused files, but the drop is effective only if
git-annex can find in some remote with an other copy of the file.
If you want to bypass checks and deleting without condition the
file
::

    $ git annex dropunused --force

of course you can no longer checkout some commit which used the
file.


Adding a remote.
~~~~~~~~~~~~~~~~
Any git remote can be also used as an annex remote. Either
ordinary remotes or bare remotes.
You create the remote repository with either
ref:`cloning your repo <remote_clone>` or
ref:`creating a bare remote from scratch
<creating_bare_remote>`.

If you want to use the remote as an annex remote you declare it on
the server with:
::

    $ git annex init origin

where *origin* is the description you have choosen for the remote
annex, usually you would choose either the same name than the
remote name, or a derived explicit name. *You can change
description with*
`git-annex describe
<https://git-annex.branchable.com/git-annex-describe/>`_

In any case you have added the remote to your local repository
with:
::

    $ git remote add origin myserver.org:bare-annex.git

It is also a good practive to add the local repo on the server
::

    $ git remote add laptop mylaptop.org:myrepo

then you do an initial push from local repo to the *origin* remote
::

    $ git push origin master git-annex

Then your remote appear on both repo
::

    git annex info
    $ git annex info
    repository mode: indirect
    trusted repositories: 0
    semitrusted repositories: 4
        00000000-0000-0000-0000-000000000001 -- web
        00000000-0000-0000-0000-000000000002 -- bittorrent
        143a9c6a-99cd-4e60-8d66-277990d99d94 -- laptop [here]
        39e9aa8a-d369-4b2e-aa37-d6c4d3282ad5 -- [origin]

The content of the annex is not yet on origin you can synchronize
it with ``git-annex get --all``  you can also use
``git-annex sync --content``.

It is also possible to work on the local repo an do:
::

    $ git annex copy --all --to=origin

If you do want to organize where is stored your annex files you
can get a view of the present situation on all accessible servers
by:
::

    $ git annex whereis
    whereis SHA256E-s1561353--2052371eabac0cb7eda1a8056d003060a7043274e4593e1091e5acacb1c96096.pdf (2 copies)
        143a9c6a-99cd-4e60-8d66-277990d99d94 -- [laptop]
        39e9aa8a-d369-4b2e-aa37-d6c4d3282ad5 -- origin [here]
    ok
    whereis SHA256E-s9407097--ee52bc1e983d0b1b1cb7a23e5cc280b94f798bb764ae2bea7c5f5460949fa56e.pdf (2 copies)
        143a9c6a-99cd-4e60-8d66-277990d99d94 -- [laptop]
        39e9aa8a-d369-4b2e-aa37-d6c4d3282ad5 -- origin [here]

You find in git-annex documentation how to
:annex:`handle special remotes <walkthrough/#index12h2>` such
S3, rsync, webdav, and more than thirty remotes in the
:annex:`list of special remotes <special_remotes>`.

Refs:
    :annex:`centralized repo on your own server
    <tips/centralized_git_repository_tutorial/on_your_own_server/>`,
    :annex:`using ssh remotes <walkthrough/#index11h2>`,
    :annex:`bare_repositories`,
    :annex:`git-annex-sync`,
    :annex:`git-annex-get`,
    :annex:`git-annex-copy`,
    :annex:`git-annex-whereis`.
