# About

All you wanted to know about GHCup.

## How to help

* if you want to contribute code or documentation, check out the [issue tracker](https://github.com/haskell/ghcup-hs/issues) and the [Development guide](./dev.md)
* if you want to propose features or write user feedback, feel free to [open a ticket](https://github.com/haskell/ghcup-hs/issues/new)
* if you want to donate to the project, visit our [opencollective](https://opencollective.com/ghcup#category-CONTRIBUTE) page

## Design goals

1. simplicity
2. non-interactive CLI interface
3. portable
4. do one thing and do it well (UNIX philosophy)

## Non-goals

1. invoking `sudo`, `apt-get` or *any* package manager
2. handling system packages
3. handling cabal projects
4. being a stack alternative

## Distribution policies

Like most Linux distros and other distribution channels, GHCup also
follows certain policies. These are as follows:

1. The end-user experience is our primary concern
    - ghcup in CI systems as a use case is a first class citizen
2. We strive to collaborate with all maintainers of all the tools we support and maintain a good relationship
3. We may fix build system or other distribution bugs in upstream bindists
    - these are always communicated upstream
4. We may even patch source code of supported tools in very rare cases if that is required to ensure that the end-user experience does not break
    - we'll first try to upstream any such required patch and request a new release to avoid downstream patching
    - patches will be communicated to the maintainers either way and we'll strive to get their review
    - they will also be communicated to the end-user
    - they will be uploaded along with the bindist
    - we will avoid maintaining long-running downstream patches (currently zero)
5. We may add bindists for platforms that upstream does not support
    - this is currently the case for GHC for e.g. Alpine and possibly FreeBSD in the future
    - this is currently also the case for stack on darwin M1
    - we don't guarantee for unofficial bindists that the test suite passes at the moment (this may change in the future)
6. We GPG sign all the GHCup metadata as well as the unofficial bindists
    - any trust issues relating to missing checksums or GPG signatures is a bug and given high priority

## How

Installs a specified GHC version into `~/.ghcup/ghc/<ver>`, and places `ghc-<ver>` symlinks in `~/.ghcup/bin/`.

Optionally, an unversioned `ghc` link can point to a default version of your choice.

This uses precompiled GHC binaries that have been compiled on fedora/debian by [upstream GHC](https://www.haskell.org/ghc/download_ghc_8_6_1.html#binaries).

Alternatively, you can also tell it to compile from source (note that this might fail due to missing requirements).

cabal-install/HLS/stack are installed in `~/.ghcup/bin/<tool>-<ver>` and have unversioned symlinks to the latest version by default (`~/.ghcup/bin/<tool>-<ver>`).

## Known users

* CI:
    - [Github actions/virtual-environments](https://github.com/actions/virtual-environments)
    - [Github haskell/actions/setup](https://github.com/haskell/actions/tree/main/setup)
    - [haskell-ci](https://github.com/haskell-CI/haskell-ci)
* mirrors:
    - [sjtug](https://mirror.sjtu.edu.cn/docs/ghcup)
* tools:
    - [vscode-haskell](https://github.com/haskell/vscode-haskell)
    - [nvim-lsp-installer](https://github.com/williamboman/nvim-lsp-installer)
    - [vabal](https://github.com/Franciman/vabal)

## Sponsors

* All [opencollective](https://opencollective.com/ghcup#category-CONTRIBUTE) contributors
* [haskell.org](https://www.haskell.org/haskell-org-committee/) via CI and infrastructure
* [Haskell Foundation](https://haskell.foundation/affiliates/) via affiliation
