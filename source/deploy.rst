Git for site deployment
=========================

On the remote::

  $ mkdir website.git && cd website.git
  $ git init --bare
  Initialized empty Git repository in /home/user/website.git/

Define a post-receive hook::

  $ cat > hooks/post-receive
  #!/bin/sh
  GIT_WORK_TREE=/var/www/www.example.org git checkout --force
  $ chmod +x hooks/post-receive

On the main site  define a remote::

  $ git remote add web ssh://server.example.org/home/user/website.git
  $ git config remote.web.push "+master:refs/heads/master"
  $ git push web


Reference: `Using Git to manage a web site
<http://toroid.org/ams/git-website-howto>`_ by Abhijit Menon-Sen

Ian Bicking does not like this strategy and prefer to:

    copy the working directory to a new location (minus .git/), commit
    that, and push the result to your deployment remote.  You can send
    modified and untracked files, and you can run a build script
    before committing and push the result of the build script, all
    without sullying your "real" source control.

    -- Ian Bicking `in this blog
    <http://blog.ianbicking.org/2012/02/14/git-as-sync-not-source-control-as-deployment/>`_

He writed `git-sync <https://github.com/ianb/git-sync>`_ automating
this task

..  local variables

    Local Variables:
    rst-indent-width: 4
    rst-indent-field: 4
    rst-indent-literal-normal: 4
    rst-indent-comment: 4
    ispell-local-dictionary: "english"
    End:
