# Introduction

Homebrew is a source-based package manager for OS X.

Its purpose in life is to make installation of UNIX-style software bearable.
Possibly even easy.

## Homebrew Philosophy

Keep it simple.
 * No "databases" other than files, folders, and symlinks in the filesystem.

(Obviously, compiling and installing abitrary software isn't simple, which is
one reason why package managers sprout so much complexity.)

Homebrew embraces Ruby.
 * Homebrew itself is written in Ruby, and so are Formulae.

Homebrew embraces OS X.
 * Use system-provided facilities when possible and appropriate.

Homebrew embraces UNIX.
 * OS X comes with BSD-flavored UNIX utilities, so let's use them.
 * Symlinks are useful.

Homebrew embraces Git.
 * Master repository is on GitHub to encourage collaboration.
 * Git itself can be installed (and updated) via Homebrew.
 * Git is then used to keep Homebrew up-to-date.
