# How Installs Work

Formula installation happens in several steps:

 * Fetch
 * Stage
 * Patch
 * Install script
 * Meta Files
 * Clean
 * Audit
 * Link

## Fetching Software

To install some software, first you have to download it off the Internet.

 - url (or head)
 - wget for file downloads
 - common VCS support
 - see "download strategies"

 - verification (hash)
 - Homebrew Cache

## Staging

 - Copy from cache to temp folder

## Patches

 - Downloaded
 - DATA
 - should be commented
 - see "creating patches using install -i --git"

## Install Script

 - "def install" method

## Meta Files

 - Certain text files will be installed to the prefix for you

## Clean

 - Removes empty folders
 - Strips binaries

## Audit

A few checks are done during an install to warn formula authors about
common problems.

 - Misplaced man pages
 - Misplaced JARs

## Linking

 - Symlinks from Cellar to HOMEBREW\_PREFIX
