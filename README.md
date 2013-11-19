Git-Trac Integration
====================

About
-----

This module implements a "git trac" subcommand of the git suite that
interfaces with trac over XMLRPC.
 

Installation
------------

The easiest way to use the code from this repo is to clone it and
run ``setup.py``:

    $ git clone https://github.com/sagemath/git-trac-command.git
    $ cd git-trac-command
    $ python setup.py install

Alternatively you can symlink ``git-trac`` to somewhere in your path:

    $ git clone https://github.com/sagemath/git-trac-command.git
    $ cd git-trac-command
    $ ln -s `pwd`/git-trac ~/bin/


Usage
-----

* Print the trac ticket information using ``git trac get
  <ticket_number>``. 

      $ git trac get 12345
      ==============================================================================
      Trac #12345: Title of ticket 12345
      ...
      ==============================================================================

  Alternatively, you can pass a remote branch name, in which case trac
  is searched for a ticket whose (remote) "Branch:" field equals the
  branch name.  If that fails, the ticket number will be deduced from
  the branch name by scanning for a number. If you neither specify a
  ticket number or branch name, the local git branch name is used:

      $ git branch
      /u/user/description
      $ git trac get
      ==============================================================================
      Trac #nnnnn: Title
      <BLANKLINE>
      Description
      Status: Status                          Component: Component                
      ...
      Branch: u/user/description
      ==============================================================================
  

* Pull (= fetch + merge) from the branch
  on a ticket:

      $ git trac pull 12345

  You can omit the ticket number, in which case the script will try to
  search for the ticket having the local branch name attached. If that
  fails, an attempt is made to deduce the ticket number from the local
  branch name.


Configuration
-------------

The scripts assume that the trac remote repository is set up as the
remote ``trac`` in the local repo. That is, you should have the
following for the Sage git server:

    $ git remote add trac http://trac.sagemath.org/sage.git      # read-only
    $ git remote add trac ssh://git@trac.sagemath.org/sage.git   # read-write
    $ git remote -v
    trac	ssh://git@trac.sagemath.org/sage.git (fetch)
    trac	ssh://git@trac.sagemath.org/sage.git (push)

Trac username and password are stored in the local repo (the
DOT_GIT/config file):
  
    $ git trac config --user=Myself --pass=s3kr1t
    Trac xmlrpc URL:
        http://trac.sagemath.org/xmlrpc (anonymous)
        http://trac.sagemath.org/login/xmlrpc (authenticated)
    Username: Myself
    Password: s3kr1t


Sage-Trac Specifics
-------------------

Some of the functionality depends on the special trac plugins (see
https://github.com/sagemath/sage_trac), namely:

* Searching for a trac ticket by branch name requires the
  ``trac_plugin_search_branch.py`` installed in trac and a custom trac
  field named "Branch:":

      $ git trac search --branch=u/vbraun/toric_bundle
      15328

* SSH public key management requries the ``sshkeys.py`` trac 
  plugin:

      $ git trac ssh-keys
      $ git trac ssh-keys --add=~/.ssh/id_rsa.pub
      This is not implemented yet