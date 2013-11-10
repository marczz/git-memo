Git Hooks
=========
Hooks are little scripts you can place in `$GIT_DIR/hooks` directory

refs: :gitdoc:`githooks <githooks.html>`


prepare_commit_msg
------------------

:gitdoc:`gitdoc: prepare_commit_msg<githooks.html#_prepare_commit_msg>`

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
