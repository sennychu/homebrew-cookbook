# Formula tests

Homebrew provides a test command that will run a `def test` method in the
specified formula, if one exists.

A good test excercises the installed software in some way. For applications,
running the software and checking the results ins appropriate.

    brew install zint
    brew test zint
    
    (A barcode appears.)


For libraries, advanced tests can try to compile against them.
See `brew cat msgpack`.
