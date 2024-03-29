Git Overview
------------

git is a version management tool. A repository is a directory tree
with a `.git` directory at the root. Git stores all its data in
`.git` directory.  Files within that directory tree can be added to
git. Contents of the files added to git are stored by git in `.git`
directory. When one or more files in the tree are modified, git keeps
the old versions of the files intact. We can view the new changes
as a diff wrt to the old versions. New changes to the files can be
`committed` to the git repository as a single changeset which creates
new versions of all the modified files keeping the old versions
intact. Each such changeset or commit is given a commit-id and it can
be viewed later as a diff from the old versions. All changes to the
repository from its beginning to present time can be viewed as a log.

Creating a new repository
-------------------------

::

    $ cd repo    # go to the dir to be made into a repo
    $ git init   # create a new repo
    $ git add .  # add the current dir tree for committing to the repo
    $ git commit # commit the added changes to the repo

The directory ``repo`` is also called a workspace. ``git`` stores all
its data in the `.git` directory at the root of the workspace. If you
remove the `.git` directory, the workspace just becomes a plain regular
directory tree with no git awareness.

Cloning an existing repository
------------------------------

::

    # clone streamly repo on github to "streamly" dir in current dir
    $ git clone https://github.com/composewell/streamly

    # clone a local repo
    $ git clone repo repo1

The newly cloned repo is known as the "local" repository and the one
you cloned from is the "remote" repository. The remote repository is
associated to the local repo by the name `origin` by default::

    $ git remote -v

After cloning both the repos have exactly the same content. Later, both the
repos can change their contents independently. Someone can make new changes to
the remote repo, and we can make some new changes to our local repo. We can
push our local changes to the remote repo by using `git push` and we can pull
new changes from the remote repo using `git pull`::

    $ git push origin master    # push changes on branch master to "origin"
    $ git push origin my-branch # push the changes on  branch my-branch to "origin"
    $ git pull                  # pulls from origin by default

Set ``git config --global pull.ff only`` so that we do not accidentally merge.

Modifying files
---------------

Change the files in a repo just as usual using your editor. When you modify a
file only the "checked out" copy in your dir tree is modified. The "checked in"
or "committed" copy that is stored in the ".git" directory remains intact. To
see what all is modified::

    $ git status

You can see the difference between the committed copy and the checked
out copy using::

    $ git diff          # textual diff of all modified files
    $ git diff file     # textual diff of the specified file
    $ git difftool      # diff all modified files in your workspace
    $ git difftool file # diff just the specified file

Committing changes
------------------

The modified files can be committed to the repository to make the changes
permanent and recorded as a revision in the repository::

    $ git add file1  # Add the file to the set of files to be committed
    $ git add file2  # Add the file to the set of files to be committed
    $ git status     # See which files are to be committed
    $ git commit     # commit the files

If you want to discard the changes made to a file::

    $ git checkout file

It will discard any changes made and restore the file to the previously
committed version.

To list all files added to the repository::

    $ git ls-files

To see the commit history of a file::

    $ git log file

The log shows the commit-ids of the commits to the file till now.  To
check the diff from a particular commit till now::

    $ git difftool commit-id

To check the diff from the commit previous to a give commit and that commit::

    $ git difftool commit-id^ commit-id

To check the diff between two commits::

    $ git commit-id1 commit-id2

Diff of the staged changes::

  $ git difftool --staged

Commit Messages
---------------

Read these:

* https://commit.style/
* https://chris.beams.io/posts/git-commit/#seven-rules

If you want to add co-authors to a commit you can add one or more lines
to the commit message in this format (recognized by github and gitlab)::

  Co-authored-by: Author Name <author@example.com>

Creating branches
-----------------

By default there is only one branch in the repository called "master"
or "main" in newer versions of git. By default you are working on the
master branch. To see which branch you are on::

    $ git status

When new changes are made to files in the repository they are recorded
as changes on the current branch. The current branch moves forward as new
changes are committed. The latest commit on the branch is called the HEAD
commit.

You can see all the commits to the repository on the current branch using::

    $ git log

All the commits to a specific file using::

    $ git log file

You can create a new branch off some commit on the current branch (or
any branch). This means all the files/commits up to that point will also
be available on the new branch. When we make any changes to the files
on a branch no other branches are affected, those changes are visible
only on that branch. In other words, we have cloned a branch from the
beginning to the given point including all the commit history of that
branch and now we can make changes to it independently. To create a
branch::

    $ git branch test  # creates a branch from the current branch HEAD commit

By default we are on the "master" branch. To switch to a branch::

    $ git checkout test -- switch to test branch

To know which branch we are on::

    $ git status

When you switch to a new branch all the files in the workspace get replaced by
the latest versions on that branch. To switch back to master branch::

    $ git checkout master -- switch to master branch

To see the commits different between master and your branch::

    $ git log master..test

To see the history and relationships of all branches and which commit
the branch is forked from etc. use this command::

    $ git log --graph --decorate --pretty=format:'%C(auto)%h %cd %d %s' --date=short --all 

If you  want to see the difference of the current branch from some other
branch e.g. master::

    $ git difftool master # show the diff of current with master

Forking on github
-----------------

On github when you fork a repository, your fork would have the original
repository set as a remote named `upstream`. When you clone your fork
then you have two remotes in the cloned repo, the original repository is
named `upstream` and your forked repository is named `origin`.

Collaborating
-------------

Working on the fork of a repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After forking/cloning a repo you would want to make changes to it. Clone the
forked repository to your local machine. Identify the branch on which you want
to make changes, make the changes and push the changes to your fork. Then go to
the fork or the parent repository and create a pull request from your changes.

Working on the original repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have access to the original repository you can work directly on the
original repo instead of working on a fork. Clone the original repository (or
add it as a remote in your existing local clone), then identify the branch you
want to base your changes on, create a new branch from the tip of that branch,
make your changes and push the new branch to the remote repository. After
pushing the branch create a pull request or merge request asking to merge your
changes to the base branch. Add appropriate reviewers to the PR.

Rebasing your branch
~~~~~~~~~~~~~~~~~~~~

The base branch from which you created your branch may move forward when
others add more commits to it. When you pull the base branch from the
remote repo you may find that your branch is forked off a previous point
from the base branch.  In that case you should rebase your branch on top
of the latest base branch before sending it for review or before the
merge::

  $ git rebase -i <base branch>

You may have to resolve any conflicts during the rebase.  You can also
choose to squash, drop, reorder commits during the rebase.

A common mistake by beginners is to merge the base branch in your branch
instead of rebasing it. That is not recommended, rebase is the preferred
method.

After the rebase you need to force push your branch to the remote because your
branch has now diverged from the branch on remote.

Force pushing a branch
~~~~~~~~~~~~~~~~~~~~~~

After you have created the pull request reviewers are looking at your
changes, they may clone your branch or may even push some fixes to your
branch. In general, you should not force push on this branch if others
may have cloned it or may be working on it.

The best practice is to always push only new commits to the branch and
never rebase or force push until the reviewers are done or unless there
is an agreement between the author and reviewers. Though, there is no
hard and fast rule, you can use your judgement whether to force push or
not based on the current context.  You can force push on a draft PR or
if you know reviewers have not yet started looking at it.

Once the review is done you can rebase/squash your changes and force
push.

Engagement rules
~~~~~~~~~~~~~~~~

* Do not directly push changes to the master branch or for that matter
  to any collaboration branch. Collaboration branches should be pushed
  only when merging.
* Never force push on collaboration branches. They always go in the
  forward direction, they are never rebased or reset to previous points
  except in very unusual cases.  If something has to be reverted on these
  branches it has to be committed as another forward commit. A forced change on
  collaboration branches can create havoc as everyone working on the branch
  will be surprised and will have to rework their changes.

Merging
~~~~~~~

The most common case of non-maintainer merge is during rebase.

Stashing
--------

* git stash
* git stash list
* git stash pop
* git stash apply

Submodules
----------

A submodule is a reference to a specific commit in another repository.
The external repository is attached to a specified folder path in the
super repository.

``.gitmodules`` file at the super repo root contains the list of
submodules recorded in the repo. This file is automatically managed by
the ``git submodule`` command.

Using repos with submodules
~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you clone a repo its submodules are not fetched automatically, it
only contains the information to fetch them. To fetch the foreign repos
you need to initialize and update the submodules::

  $ git submodule update --init --recursive

Alternatively you could clone with ``--recursive`` option to update the
modules at the time of cloning itself.

The submodule folder will now contain a checkout of the foreign repo
at the commit tag recorded in the super repo. The submodule folder is an
independent git repo in itself. You can run usual git commands in this
repo. However, it is not exactly the same as a regular repo, especially
note that ``.git`` in this repo is a text file with its contents
pointing to a ``.git`` directory of the super repo, it contains
something like this::

  gitdir: ../.git/modules/nix-bundle-init

To see the status of all submodules in the super repo::

  $ git submodule status --recursive

If you create new commits or change the head commit inside the submodule
folder, ``git status`` in the super repo will show the submodule folder
as modified::

        modified:   streamly (new commits)

``git diff`` will show the commit change to be recorded for the
submodule folder::

  Subproject commit 04e3781d5d3d8f9ae2a8fedc90ea62c31a8a5100

You can commit this change to record the new commit for the submodule.
To discard the changes and reset the submodule to the commit recorded in
the super repo::

  $ git submodule update --checkout

Adding Submodules to a repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~

To add ``streamly`` as a submodule in your project at the path
``./streamly`` ::

  $ git submodule add git@github.com:composewell/streamly.git streamly

It will make the changes in the super repo for adding the submodule and
clone the streamly repo in ``./streamly`` folder and stage the changes
for commit. You can then commit it::

  $ git status
  Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   .gitmodules
        new file:   streamly
  $ git commit

The commit freezes the submodule at the commit which is checked out in the
submodule repo.

For more details try ``git submodule --help``.

Advanced
~~~~~~~~

When you add the submodule an entry is added to ``.gitmodules`` and the
folder path in which the submodule is to be checked out is committed to the
repo. This information is global to the git repo and is pushed to remotes on a
push.

``git submodule init``, adds an entry in ``.git/config``. ``git
submodule update`` fetches the remote repo in ``.git/modules`` and
checks it out in the submodule folder. All this information is local to
the current repo and is not pushed to remotes when the current repo is
pushed.

``git submodule add`` does all of the above. In a cloned repo the first
part is present in the repo being cloned, and you need to do ``git
submodule init`` and ``git submodule update`` to make the local repo
changes.

When you ``deinit`` the submodule git removes the entry from ``.gitmodules``
and removes the folder as well but the metadata in ``.git/config`` and
in ``.git/modules`` may remain. If you want to cleanup everything you
can remove those manually.

Who changed a line and why?
---------------------------

Find out the commit in which the line was last changed::

  $ git blame <file>

Show that commit::

  $ git show <commit>

Note the text in the line you are chasing, then find all commits that
include the text::

  $ git log --source --all -S <string>
  $ git log --source --all -G <pattern>

Select the commit you are interested in and then find the older text from the
diff of that commit and then you can chase in the same way who introduced that
and so on.

Other Commands
--------------

* git rm
* git mv
* git reset
* git show
* git cherry-pick

Frequently Asked Questions
--------------------------

Q. How to find when a particular line in the code was introduced?
A. https://stackoverflow.com/questions/5816134/how-to-find-the-git-commit-that-introduced-a-string-in-any-branch/5816177#5816177

References
----------

* https://education.github.com/git-cheat-sheet-education.pdf
* https://git-scm.com/docs/gittutorial
* https://git-scm.com/docs/giteveryday
* https://git-scm.com/docs/gitworkflows
* https://git-scm.com/docs reference
* https://git-scm.com/doc all docs
* https://ohshitgit.com/ Some common problems and solutions
