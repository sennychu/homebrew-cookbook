# Debugging Formuale

## Interactive installs

`brew install -i <foo>` will download a formula, apply patches, and then
open a shell to the temporary work folder.

## Generating patches

 * brew install -i --git
 * edit, edit, edit
 * git diff | pbcopy
 * pase into formula after \_\_END\_\_


## "Breakpoints"

To add a breakpoint to a formula, simply raise an exception and do a
debug install:

*We want to pause before 'make install' to inspect the Makefile.*

    require 'formula'

    class Aacgain <Formula
      url 'http://altosdesign.com/aacgain/alvarez/aacgain-1.8.tar.bz2'
      homepage 'http://altosdesign.com/aacgain/'
      md5 '61ce9e648fa1773adb3d4b3c84c6e4ca'

      def install
        system "./configure", "--disable-debug", "--disable-dependency-tracking",
                              "--prefix=#{prefix}"
        raise "Stop!"
        system "make install"
      end
    end

    $ brew install -vd aacgain
    ...
    $ mate Makefile
