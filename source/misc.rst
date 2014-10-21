Miscellaneous operations
========================


..  index:
    single: git; symbolic-ref
    single: branch; change
    single: git;new-workdir
    single: git; reset

switching branches without doing a checkout
-------------------------------------------

Refs:
    :gitdoc:`git symbolic-ref <git-symbolic-ref.html>`,
    :gitdoc:`git reset <git-reset.html>`,

::

    $ git symbolic-ref HEAD refs/heads/otherbranch

For every work on the branch you must get a fresh index with:

::

    $ git reset

The script ``git-new-workdir`` in the contrib directory creates a symlink to a repository,
optionally with a new checked out branch::

    $ git-new-workdir <repository> <newworkdir> [<branch>]


Transparent encryption
----------------------

There are two different use of encryption, the first is when your
un-encrypted repository tree is convenient on your own server but you don't
want to push un-encrypted data on remote. You have to alternative,
either you encrypt the sensible files in the repository, and then you
can push freely the repository content, or you don't push to unsecure
remote but with a backend like the
:ref:`gcrypt encrypted remote <gcrypt-remote>` or even you store
encrypted :ref:`bundles <git-bundle>`.

If your data is truly sensible, you should not let it un-encrypted even
on your preferred server, you can then either encrypt it in the
repository tree, and nothing else is necessary, or have the repository
and working tree in an encrypted filesystem, and apply one of the
previous solution to backup your repo, or to encrypt the data in the
repository in the unencrypted filesystem, and decrypt  it on-the-fly in the
working-tree which is itself in an encrypted filesystem.


You have to be aware that encrypted files can lead to a very
inefficient storing because close versions of a file when encrypted do
not have a *delta* allowing packing. See
`git-pack-objects(1) Manual Page
<https://www.kernel.org/pub/software/scm/git/docs/git-pack-objects.html>`_
for details

I show an elementary example of transparent encryption.

In a new repository I put a big text, and look at the repo size::

    $ git add DavidCopperfield.txt
    $ git commit -m'added DavidCopperfield.txt'
    $ du -sk DavidCopperfield.txt
    1968	DavidCopperfield.txt
    $ du -sk .git
    1052	.git

Now I change a sentence in the text, and check in my work::

    $ git diff --numstat
    1       0       DavidCopperfield.txt

Now I look at the size of my repo::

    $ du -sk DavidCopperfield.txt
    1968	DavidCopperfield.txt
    $ du -sk .git
    1960	.git

My repo is nearly twice as big as previously, not very nice.
But I remember that git use *loose* object until told to pack,
so I do::

    $ git gc
    Counting objects: 8, done.
    Delta compression using up to 2 threads.
    Compressing objects: 100% (5/5), done.
    Writing objects: 100% (8/8), done.
    Total 8 (delta 1), reused 0 (delta 0)
    $ du -sk .git
    892	.git

A lot, better.

Now I do the same experiment with an encrypted file.

I first create some script ~/gitencrypt/passphrase::

    pass="my secret passphrase"

``~/gitencrypt/clean_filter``:

..  code-block:: sh

    #!/bin/sh

    salt=82acb021e056fc9e8f75a5fe # 24 or less hex characters
    . ${0%/*}/passphrase
    openssl enc -base64 -aes-256-cbc -S $salt -k "$pass"

``~/gitencrypt/smudge_filter``:

..  code-block:: sh

    #!/bin/sh

    . ${0%/*}/passphrase
    openssl enc -d -base64 -aes-256-cbc -k "$salt"

``~/gitencrypt/diff_filter``:

..  code-block:: sh

    !/bin/sh

    . ${0%/*}/passphrase
    openssl enc -d -base64 -aes-256-ecb -k "$pass" -in "$1"

and in my ``.git/config`` I add:

..  code-block:: ini

    [filter "openssl"]
        smudge = /tmp/gitencrypt/smudge_filter
        clean = /tmp/gitencrypt/clean_filter
    [diff "openssl"]
        textconv = /tmp/gitencrypt/diff_filter
    [merge]
        renormalize = true


Then I add the same file I did previously::

    $ git add DavidCopperfield.txt
    $ du -sk .git
    1036	.git

I do the same sentence change than previously, then::


    $ git diff --numstats
    1       0       DavidCopperfield.txt
    $ git add DavidCopperfield.txt
    $ du -sk .git
    1928	.git
    $ git gc --prune
    Counting objects: 2, done.
    Writing objects: 100% (2/2), done.
    Total 2 (delta 0), reused 0 (delta 0)
    $ du -sk .git
    1928	.git

Git cannot pack gpg encoded files because even if only a sentence
differ every block of encrypted file is completely different.

The approach used here is almost identical to the one proposed by
Woody Gilk in
`Transparent Git Encryption
<https://gist.github.com/shadowhand/873637>`_
accompanied with a set of scripts
`git-encrypt
<https://github.com/shadowhand/git-encrypt>`_

A similar, more polished approach is `Andrew Ayer git-crypt
<https://github.com/AGWA/git-crypt>`_.

The inability to pack an encrypted directory was signaled by
Junio Hamano in the article:
`Re: Transparently encrypt repository contents with GPG
<http://article.gmane.org/gmane.comp.version-control.git/113221>`_
or look at the `article thread
<http://thread.gmane.org/gmane.comp.version-control.git/113124/focus=113221>`_.

Using git-wip
-------------

Refs:
    `git-wip repository and README <https://github.com/bartman/git-wip>`_

**git-wip** is a script that will manage Work In Progress
branches.

To show the log of the commits in wip/master and not in master::

    $ git log master..wip/master

You can add ``-p`` to see what is added::

    $ git log -p master..wip/master

Here as usual for a git revision range ``master..wip/master``
means all the commit in ``wip/master`` which are not in ``master``.


To see the what is in wip and not committed to master you do::

    $ git diff master...wip/master

This shows the diff between the common ancestor of master and
wip/master and master.

::

    $ git diff master..wip/master

is the same than::

    $ git diff master wip/master

And represent the difference beetween master and wip/master, this is
probably **not what you want** because if you have committed something
since the last *wip*, master is not an ancestor of wip/master, so this
diff will also undo whatever is committed since the common ancestor.
