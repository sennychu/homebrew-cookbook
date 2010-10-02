# Handling Java software

Homebrew was originally designed to make compiling and installing C and C++
based software from source more enjoyable. But these days a lot of useful
software that people want to install is written in Java.

Java software distributions often come with their own mini-filesystem layout,
including bin, lib and other folders. The challenge for Homebrew is that
the `lib` folder is typically full of JAR files. If we link these into
/usr/local/lib, there will end up being conflicts when multiple Java-based
projects include the same jars.

To handle this, we typically install Java packages into `libexec`, and then
symlink or wrap files from `libexec/bin` to `bin`.

See:

 * ActiveMQ
 * JRuby
 * Jython
 * Others?

Todo:

 * Example code in-line in this topic.

