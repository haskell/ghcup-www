# Known problems

## Custom ghc version names

When installing ghc bindists with custom version names as outlined in
[installing custom bindists](guide/advanced_install.md/#installing-custom-bindists), then cabal might
be unable to find the correct `ghc-pkg` (also see [#73](https://gitlab.haskell.org/haskell/ghcup-hs/-/issues/73))
if you use `cabal build --with-compiler=ghc-foo`. Instead, point it to the full path, such as:
`cabal build --with-compiler=$HOME/.ghcup/ghc/<version-name>/bin/ghc` or set that GHC version
as the current one via: `ghcup set ghc <version-name>`.

This problem doesn't exist for regularly installed GHC versions.

## Limited distributions supported

Currently only GNU/Linux distributions compatible with the [upstream GHC](https://www.haskell.org/ghc/download_ghc_8_6_1.html#binaries) binaries are supported.

## Precompiled binaries

Since this uses precompiled binaries you may run into
several problems.

### Missing libtinfo (ncurses)

You may run into problems with *ncurses* and **missing libtinfo**, in case
your distribution doesn't use the legacy way of building
ncurses and has no compatibility symlinks in place.

Ask your distributor on how to solve this or
try to compile from source via `ghcup compile <version>`.

### Libnuma required

This was a [bug](https://ghc.haskell.org/trac/ghc/ticket/15688) in the build system of some GHC versions that lead to
unconditionally enabled libnuma support. To mitigate this you might have to install the libnuma
package of your distribution. See [here](https://gitlab.haskell.org/haskell/ghcup/issues/58) for a discussion.

## Compilation

Although this script can compile GHC for you, it's just a very thin
wrapper around the build system. It makes no effort in trying
to figure out whether you have the correct toolchain and
the correct dependencies. Refer to [the official docs](https://ghc.haskell.org/trac/ghc/wiki/Building/Preparation/Linux)
on how to prepare your environment for building GHC.

## Stack support

There may be a number of bugs when trying to make ghcup installed GHC versions work with stack,
such as:

- https://gitlab.haskell.org/haskell/ghcup-hs/-/issues/188

Further, stack's upgrade procedure may break/confuse ghcup. There are a number of integration
issues discussed here:

- https://gitlab.haskell.org/haskell/ghcup-hs/-/issues/153

## Windows support

Windows support is in early stages. Since windows doesn't support symbolic links properly,
ghcup uses a [shimgen wrapper](https://github.com/71/scoop-better-shimexe). It seems to work
well, but there may be unknown issues with that approach.

Windows 7 and Powershell 2.0 aren't well supported at the moment, also see:

- https://gitlab.haskell.org/haskell/ghcup-hs/-/issues/140
- https://gitlab.haskell.org/haskell/ghcup-hs/-/issues/197
