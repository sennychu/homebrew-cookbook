# Git Workflow for Homebrew

Homebrew's preferred Git workflow is to end up with a linear sequence of
commits, with no merge commits. On our end, this means cherry-picking
commits from forks. To keep the history clean, we will also squash commits
if it hasn't already been done in the fork.

This is why Homebrew prefers "one commit per formula, one formula per commit."

We encourage forks that expect to be merged back to master to stay rebased
on mxcl/master, again to make it easier to pull individual formulae.


## Adam V's Workflow

I'm a committer to Homebrew, and I have brew installed different from the
recommended default.


### Local Environment

My OS X environment is on GitHub: http://github.com/adamv/dotfiles

Git aliases and utilities:

 * git b: aliased to "git checkout -b"
 * git remaster: aliased to "git rebase master"
 * [gh-pick](http://github.com/adamv/dotfiles/blob/master/bin/gh-pick):
   Cherry-picks commits from GitHub commit URLs.
 * [git-ff](http://github.com/adamv/dotfiles/blob/master/bin/git-ff):
   Tool for doing fast-forward updates from a tracked branch.
 * [git-wipe](http://github.com/adamv/dotfiles/blob/master/bin/git-wipe):
   Deletes all remotes not named "origin" or "upstream".

In my .gitconfig I set:

    [push]
      default = current

See also my [PROMPT\_COMMAND](http://github.com/adamv/dotfiles/blob/master/dot.bashrc#L187)
which adds some Git and Homebrew indicators.


### Developer Install of Homebrew

First, I fork "mxcl/homebrew" to "adamv/homebrew" on GitHub.

Then I check out my clone:

    $ cd ~
    $ git clone git@github.com:adamv/homebrew.git

This will put my Cellar at "~/homebrew/Cellar", out of the way of /usr/local.

Since I'll be pushing to "mxcl/homebrew", I need to add it as a remote:

    $ cd ~/homebrew
    $ git remote add upstream git@github.com:mxcl/homebrew.git

Calling this remote "upstream" is deliberate, as some of my scripts above
expect repos named "origin" and "upstream".

At this point, I delete "origin/master" (but only after pushing a dummy
branch) from GitHub. I tell my local "master" branch to track mxcl.

    $ git push origin :master
    $ git branch --set-upstream master upstream/master


Finally, to actually install brew, symlink into /usr/local:

    $ mkdir -p /usr/local/bin
    $ ln -s /Users/adamv/homebrew/bin/brew /usr/local/bin/brew


### Pulling Commits

To pull some commits, I make sure I have my "master" branch checked out
and up-to-date.

    $ cd ~/homebrew
    $ git checkout master
    $ git pull --rebase

At this point, find some interesting commits in the GitHub Issues or
Pull Requests 2.0, and copy a commit URL to the clipboard.

We'll test the commit in a separate branch so we can easily ammend or reject,
without having to worry about messing up master. Assume the new formula is
named "foo".

    $ git b b
    $ gh-pick <url>
    $ brew install -vd foo

(The top line is aliased to `git checkout -b`.)

When brewing foo, we check that it actually brews OK, but also keep an eye
on the configure output to see if any deps are missing, or being picked up
without being specified in the formula.

If the formula doesn't install, see if there's a quick fix, like adding
`ENV.j1` or separating `make` and `make install`.

If the formula can be fixed, commit to the branch. Othewise, gist the install
failure and report it back to the submitter.

Assuming a succesful install, try `otool -L` on binaries (and libraries) to
check for suspicious linkages. Things linked from the Cellar that weren't
specified as dependencies should be added.

It's a good idea to check the script for Homebrew style at this point.

    $ brew audit

When the formula is good in the branch, rebase against master and squash
into a single commit, then merge into master. Since we just rebased, the
merge will be a fast-forward. Push to master and delete the temporary branch.

    $ git remaster -i
    <squash all commits and reword if needed>
    $ git checkout master
    $ git merge b --ff-only
    $ git branch -d b
    $ git push upstream
    <close issue or pull request>

The "--ff-only" is just a sanity check, to make sure we won't generate
a merge commit.

After a lot of pulls, you end up with a lot of remote branches that you
probably don't care about (or want to see in GitX.) I nuke everything
except "origin" and "upstream" with:

    $ git wipe
