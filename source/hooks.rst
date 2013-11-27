Git Hooks
=========
Hooks are little scripts you can place in `$GIT_DIR/hooks` directory

refs: :gitdoc:`githooks <githooks.html>`, :progit:`Git Hooks`


prepare_commit_msg
------------------*

Ref: :gitdoc:`gitdoc:
prepare_commit_msg<githooks.html#_prepare_commit_msg>`

This hook is invoked by git commit  after preparing the default
message, and before the editor is started. It takes one to three
parameters.

-   The name of the file that contains the prepared message.
-   The second is ``message`` if a ``-m`` or ``-F`` option was given,
    ``template`` with a ``-t`` option or configuration
    ``commit.template``, ``commit``
    if a ``-c``, ``-C`` or ``--amend`` option was given,
    ``merge`` for a merge commit or with a ``.git/MERGE_MSG`` file;
    and ``squash`` if a ``.git/SQUASH_MSG`` file exists.
-   The third is only for a ``commit`` and is the commit SHA-1.

A non-zero exit means a failure of the hook and aborts the commit.



.. code:: bash

    #!/bin/sh
    num_messages=5
    format="# %h %s [%an]"
    log="$(git log --pretty="${format}" -${num_messages})"
    header="#
    # last ${num_messages} messages
    # ----------------------"
    template() {
        echo "${header}"
        echo "${log}"
    }
    case "$2" in
    merge|template|squash) ;;
    ""|commit) template >> $1;;
    *)  echo "error in prepare-commit-msg hook" >&2
        exit 1
        ;;
    esac
