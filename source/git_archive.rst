Git keyword expansion
=====================

We can archive the files using keywords defined in
:gitdoc:`gitattributes <gitattributes.html>` by using the command
:gitdoc:`git archive <git-archive.html>`.

We archive files by::

    git archive --format=tgz --worktree-attributes HEAD file1 file2 ...

The :gitdoc:`attributes <gitattributes.html>` are taken from the tree, and
with the option ``--worktree-attributes`` in the checked out worktree.

Git also look at the repository local file
``$GIT_DIR/info/attributes``

If the attribute ``export-subst`` is set for a file then git will expand
several placeholders  with the syntax ``$Format:PLACEHOLDERS$``.

The place holders are defined in
:gitdoc:`git log pretty formats <git-log.html#_pretty_formats>`

I use in my C source files::

  /* @date   $Format: %ci$
   * @version Commit: $Format: %h$
   * Tree: $Format: %t$
   */

To exports the committer date ``%ci``, the abbreviated tree hash
``<%t``, and the abbreviated commit hash ``%h``

..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
