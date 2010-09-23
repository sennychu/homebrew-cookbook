# Anatomy of a Formula

*A typical autotools-based formula.*

    require 'formula'

    class Aacgain <Formula
      url 'http://altosdesign.com/aacgain/alvarez/aacgain-1.8.tar.bz2'
      homepage 'http://altosdesign.com/aacgain/'
      md5 '61ce9e648fa1773adb3d4b3c84c6e4ca'
    
      def install
        system "./configure", "--disable-debug", "--disable-dependency-tracking",
                              "--prefix=#{prefix}"
        system "make install"
      end
    end

At a minimum, a formula contains:

 - A `url`
 - A `homepage`
 - An `md5` or `sha1` checksum
 - A `def install` method

All formulae ultimately derive from the `formula` class in formula.rb. There
are also a couple of special-case subclasses at the end of that file.

The `formula` class provides "DSL-style" methods for providing information
about the software being built, and some methods that may be overridden to
customize build behavior and provide information to the user.

## Method Summary

DSL-style methods:

 - `url` specifies a stable download (This or head required)
 - `head` specifies an unstable, dev, trunk, etc. download
 - `version` explicitly sets a version number (Required if version not detected from url)
 - `homepage` (Required)
 - `md5` and `sha1` provide verification (Required for file-based URLs)
 - `depends_on` specifies a dependency
 - `skip_clean` specifies files and folders to skip during the cleaning step
 - `keg_only` defines a formula as "keg-only" and provides a reason

Methods to be overridden:

 - `install` performs the install (puts files in the Cellar) (Required)
 - `options` documents formula-specific options
 - `patches` specifies patches
 - `caveats` provides warnings and additional information

### homepage

`homepage` is simply a URL for the main page of the project. Some software
doesn't really have a homepage per se. In those cases, be creative.

To open the homepage in a browser:

    $ brew home foo

### url and head

`url` and `head` are used to define the "stable" and "unstable" downloads.

Only one is required; if both are provided then building the unstable
version is done with:

    $ brew install --HEAD foo

At a minimum, these methods are passed the URL to download the software.

    url 'http://altosdesign.com/aacgain/alvarez/aacgain-1.8.tar.bz2'

Remember that "download" may mean using wget to get a file, or it may mean
doing a checkout from a public version control repository.

    head 'git://repo.or.cz/dvtm.git'

#### Download Strategies

Homebrew tries to determine the correct download strategy to use for a URL,
using the `detect_download_strategy` method in download\_strategy.rb. Some
"http" URLs for widely-used source hosts are translated by this function
into VCS checkouts.

If the wrong download strategy is detected, or a formula needs to use a
custom strategy, it can be specified with the `:using` keyword.

The value of the `:using` keyword should either be a known VCS symbol as
defined in the `VCS_SYMBOLS` mapping in formula.rb, or a class that derives
from DownloadStrategy.

*Overriding an HTTP download to a SVN checkout.*

    head 'http://otx.osxninja.com/builds/trunk/', :using => :svn

*Overriding a Google Code URL to a file download.*

    url 'http://lorem.googlecode.com/svn-history/r4/trunk/lorem',
          :using => :curl

*Using a custom download strategy.*

    url 'http://downloads.sourceforge.net/project/FreeImage3131.zip',
          :using => FreeimageHttpDownloadStrategy

See: Download Strategies

#### VCS tags and revisions

When sourcing form a version control system, we will still want to use a
stable point for `url` builds, and may also need to use one for `head` builds
if the very latest version is known not to work on OS X.

To specific a tag, branch, revision, etc., pass an appropriate keyword with
a string value. The exact keywords supported depend on the specific VCS
download strategy being used.

*Git tag.*

    url "git://github.com/erlang/otp.git", :tag => "OTP_R13B04"

*Git branch.*

    head 'git://github.com/adamv/adamem-osx.git', :branch => 'osx-brew'

*Subversion revision.*

    url 'http://xu4.svn.sourceforge.net/svnroot/xu4/trunk/u4',
          :revision => '2725'

The Subversion download strategy also has support for specifying
separate revisions for the master and externals. This was needed for
ffmpeg, but is not currently used by any formulae.

### version

Homebrew has code to detect version numbers from URLs.

    url 'http://altosdesign.com/aacgain/alvarez/aacgain-1.8.tar.bz2'

In the above case the detected version will be "1.8". If the version cannot be
determined from the URL, or you want to override what was detected, it must be
provided explicitly.

    url 'http://ftp.gnu.org/gnu/bash/bash-4.1.tar.gz'
    version '4.1.7'

Head URLs do not have a version and are always set to "HEAD".

### md5 and sha1

Formulae that use file downloads (http, https, ftp) instead of VCS checkouts,
which is most formulae, need to include a checksum. Supported hash methods are
md5 (default), sha1 (used when the project provides this), and
sha256 (rarely used.)

The purpose of the checksum is to make sure that the software being
downloaded hasn't been changed since the formula was created. This
includes malicious tampering, but usually it is just to check for silent
version updates to the downloaded software, which may require updating the
formula.

### depends_on

`depends_on` is used to define dependencies.

A dependency can also be marked as "recommended" or "optional", but these
currently only act as documentation. Homebrew treats all types of
dependencies as required.

*Some depedencies from ScummVM.*

    depends_on 'sdl'
    depends_on 'flac' => :recommended
    depends_on 'fluid-synth' => :optional

**Note:** You can tell Homebrew to turn off dependency checking:

    $ brew install --ignore-dependencies foo

#### External Dependencies

Homebrew has limited support for specifying external language-specific
dependencies. Remember that Homebrew encourages the use of language-specific
package managers such as pip and gem to install language-specific libraries.

If a language-specific dependency is given, Homebrew will try to import that
module, and give the user an error if it is not installed.

Supported languages are: :perl, :ruby, :jruby, :python

*RabbitMQ depends on the simplejson module, but only on Python 2.4.*

    depends_on 'simplejson' => :python if MACOS_VERSION < 10.6

*Maatkit requires Perl's MySQL driver.*

    depends_on 'DBD::mysql' => :perl

Note that Homebrew will not automatically install these external dependencies.
Further, formulae that are submitted with a large number of externals are not
likely to be included in the master branch. (Having lots of externals
increases the maintenance cost of a formula.)

### skip_clean

`skip_clean` tells Homebrew not to strip binaries and libraries or remove
empty folders.

 - files, folders, :all

### keg_only

`keg_only` marks a formula as "keg-only", meaning it won't be symlinked into
the HOMEBREW\_PREFIX. Such a formula only exists in the Cellar. This is
typically used when software overrides OS X provided libraries (such as
readline) or when we don't want packages to be picked up by accident by
other configure scripts (such as gettext).

 - reasons, :provied\_by\_osx

### install

### options

### patches

### caveats
