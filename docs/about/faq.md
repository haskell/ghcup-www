# FAQ

## Is ghcup really the main installer?

This is based on the Haskell survey results from 2022, which show that more
than half of survey participants use GHCup: https://taylor.fausak.me/2022/11/18/haskell-survey-results/

## Why reimplement stack?

GHCup is not a reimplementation of stack. The only common part is automatic installation of GHC,
but even that differs in scope and design.

## Why should I use ghcup over stack?

GHCup is not a replacement for stack. Instead, it supports installing and managing stack versions.
It does the same for cabal, GHC and HLS. As such, It doesn't make a workflow choice for you.

## Why should I let ghcup manage stack?

You don't need to. However, some users seem to prefer to have a central tool that manages cabal and stack
at the same time. Additionally, it can allow better sharing of GHC installation across these tools.
Also see:

* https://docs.haskellstack.org/en/stable/configure/yaml/non-project/#system-ghc
* https://github.com/commercialhaskell/stack/pull/5585

## Why does ghcup not use stack code?

1. GHCup started as a shell script. At the time of rewriting it in Haskell, the authors didn't even know that stack exposes *some* of its [installation API](https://hackage.haskell.org/package/stack-2.5.1.1/docs/Stack-Setup.html)
2. it doesn't support cabal installation, which was the main motivation behind GHCup back then
3. depending on a codebase as big as stack for a central part of one's application without having a short contribution pipeline would likely have caused stagnation or resulted in simply copy-pasting the relevant code in order to adjust it
4. it's not clear how GHCup would have been implemented with the provided API. It seems the codebases are fairly different. GHCup does a lot of symlink handling to expose a central `bin/` directory that users can easily put in PATH, without having to worry about anything more. It also provides explicit removal functionality, GHC cross-compilation, a TUI, etc etc.

## Why not unify...

### ...stack and Cabal and do away with standalone installers

GHCup is not involved in such decisions. cabal-install and stack might have a
sufficiently different user experience to warrant having a choice.

### ...installer implementations and have a common library

This sounds like an interesting goal. However, GHC installation isn't a hard engineering problem
and the shared code wouldn't be too exciting. For such an effort to make sense, all involved
parties would need to collaborate and have a short pipeline to get patches in.

It's true this would solve the integration problem, but following unix principles, we can
do similar via **hooks**. Both cabal and stack can support installation hooks. These hooks
can then call into ghcup or anything else, also see:

* https://github.com/haskell/cabal/issues/7394
* https://github.com/commercialhaskell/stack/pull/5585

### ...installers (like, all of it)

So far, there hasn't been an open discussion about this. Is this even a good idea?
Sometimes projects converge eventually if their overlap is big enough, sometimes they don't.

While unification sounds like a simplification of the ecosystem, it also takes away choice.
Take `curl` and `wget` as an example.

## Why not support windows?

Windows is supported since GHCup version 0.1.15.1.

## Why the haskell reimplementation?

GHCup started as a portable posix shell script of maybe 50 LOC. GHC installation itself can be carried out in
about ~3 lines of shell code (download, unpack , configure+make install). However, much convenient functionality
has been added since, as well as ensuring that all operations are safe and correct. The shell script ended up with
over 2k LOC, which was very hard to maintain.

The main concern when switching from a portable shell script to haskell was platform/architecture support.
However, ghcup now re-uses GHCs CI infrastructure and as such is perfectly in sync with all platforms that
GHC supports.
