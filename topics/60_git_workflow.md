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

Of interest here:

 * [gh-pick](http://github.com/adamv/dotfiles/blob/master/bin/gh-pick)  
   Cherry-picks commits from GitHub commit URLs.
 * [git-ff](http://github.com/adamv/dotfiles/blob/master/bin/git-ff)  
   Tool for doing fast-forward updates from a remote.
 * [git-wipe](http://github.com/adamv/dotfiles/blob/master/bin/git-wipe)  
   Deletes all remotes not named "origin" or "upstream".

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

Finally, to actaully install brew, I symlink into /usr/local:

    $ mkdir -p /usr/local/bin
    $ ln -s /Users/adamv/homebrew/bin/brew /usr/local/bin/brew


### Pulling Commits
