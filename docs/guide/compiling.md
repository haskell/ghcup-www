# Compiling from source

## GHC

Compiling from source is supported for both source tarballs and arbitrary git refs. See `ghcup compile ghc --help`
for a list of all available options.

If you need to overwrite the existing `build.mk`, check the default files
in [data/build_mk](https://github.com/haskell/ghcup-hs/tree/master/data/build_mk), copy them somewhere, adjust them and
pass `--config path/to/build.mk` to `ghcup compile ghc`.
Common `build.mk` options are explained [here](https://gitlab.haskell.org/ghc/ghc/-/wikis/building/using#build-configuration).

Make sure your system meets all the [prerequisites](https://gitlab.haskell.org/ghc/ghc/-/wikis/building/preparation).

## HLS

There are 3 main ways to compile HLS from source.

1. from hackage (should have up to date version bounds)
    - `ghcup compile hls --version 1.7.0.0 --ghc 9.2.3`
2. from git (allows to build latest sources and PRs)
    - `ghcup compile hls --git-ref master --ghc 9.2.3`
    - `ghcup compile hls --git-ref a32db0b --ghc 9.2.3`
    - `ghcup compile hls --git-ref 1.7.0.0 --ghc 9.2.3`
3. from source distribution that's packaged during release from the corresponding git sources
    - `ghcup compile hls --source-dist 1.7.0.0 --ghc 9.2.3`

All these use `cabal v2-install` under the hood, so all build components are cached.
You can pass arbitrary arguments to cabal, e.g. set the index state like so:

```sh
ghcup compile hls --git-ref master --ghc 9.2.3 -- --index-state=2022-06-12T00:00:00Z --allow-newer
```

You can pass `--ghc <ver>` multiple times to install for many GHCs at once.

When building from git sources, ghcup will auto-detect the HLS version that the git commit corresponds to
from the `haskell-language-server.cabal` file. This version might not have been updated since the last release.
If you want to avoid overwriting the existing installed HLS version, you can instruct ghcup to use `git describe`
to set the HLS version instead:

```sh
ghcup compile hls --git-ref master --ghc 9.2.3 --git-describe-version
```

You can also set the version explicitly:

```sh
ghcup compile hls --git-ref master --ghc 9.2.3 --overwrite-version 1.7.0.0-p1
```

To instruct cabal to run `cabal update` before building, run `ghcup compile hls --version 1.7.0.0 --ghc 9.2.3 --cabal-update`

As always, check `ghcup compile hls --help`.

### Updating HLS for a new GHC version

First try to build from hackage with some tricks:

```sh
ghcup compile hls --version 1.7.0.0 --ghc 9.2.4 --cabal-update -- --allow-newer --index-state=2022-06-12T00:00:00Z
```

This augments the currently installed 1.7.0.0 official bindists in ghcup with new GHC versions support.

If that fails (since `--allow-newer` is quite brutal), you can install from HLS master branch (which may contain new fixes) like so:
```
ghcup compile hls --git-ref master --git-describe-version --ghc 8.10.7 --ghc 9.2.4 --cabal-update
```

This however will create a new HLS version in ghcup, e.g. `1.7.0.0-105-gdc682ba1`, for both 8.10.7 and 9.2.4. If you want to switch back to the official bindists, run `ghcup set hls 1.7.0.0`.
