# Universal builds

By default, most formulae build for the most appropriate architecture for the
user's OS and CPU.

This means building 32-bit on Leopard and 32-bit CPUs running Snow Leopard,
and building 64-bit on 64-bit CPUs running Snow Leopard.

To enable a universal build, in the simple case,
`ENV.universal if ARGV.universal?` can be used in `def install` if the package
itself supports this method.

See: ENV.universal source code.

Other packages require more complicated steps for unviersal builds.

 * zeromq
 * other examples

TODO: Talk about subversion, universal, and its dependencies.
