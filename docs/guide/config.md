# Configuration

A configuration file can be put in `~/.ghcup/config.yaml`. The default config file
explaining all possible configurations can be found in this repo: [config.yaml](https://github.com/haskell/ghcup-hs/blob/master/data/config.yaml).

Partial configuration is fine. Command line options always override the config file settings.

## Overriding distro detection

If you're running e.g. an Ubuntu derivative based on 18.04 and ghcup is picking bindists that
don't work well, you could do this in `config.yaml`:

```yml
platform-override:
  arch: A_64
  platform:
    contents: Ubuntu
    tag: Linux
  version: '18.04'
```

## Env variables

This is the complete list of env variables that change GHCup behavior:

* `GHCUP_USE_XDG_DIRS`: see [XDG support](#xdg-support) below
* `GHCUP_INSTALL_BASE_PREFIX`: the base of ghcup (default: `$HOME`)
* `GHCUP_CURL_OPTS`: additional options that can be passed to curl
* `GHCUP_WGET_OPTS`: additional options that can be passed to wget
* `GHCUP_GPG_OPTS`: additional options that can be passed to gpg
* `GHCUP_SKIP_UPDATE_CHECK`: Skip the (possibly annoying) update check when you run a command
* `NO_COLOR`: Disables ANSI color escape codes, also see [https://no-color.org](https://no-color.org/)
* `CC`/`LD` etc.: full environment is passed to the build system when compiling GHC via GHCup

On windows, there's additionally:

* `GHCUP_MSYS2`: Has to point to the root of an existing MSYS2 installation (when installed by GHCup, that's e.g. `C:\ghcup\msys64`). GHCup bootstrap takes care of this usually.
* `GHCUP_MSYS2_ENV`: The [MSYS2 environment](https://www.msys2.org/docs/environments/) to use when executing e.g. `ghcup run --mingw-path`. Possible values are `MSYS`, `UCRT64`, `CLANG64`, `CLANGARM64`, `CLANG32`, `MINGW64`, `MINGW32`. Defaults to `MINGW64`, `MINGW32` or `CLANGARM64`, depending on the architecture. `MSYS` is always added as the last component. If you change this value after running the bootstrap script, you may need to make sure that the cabal config reflects this change, more specifically `extra-prog-path`, `extra-include-dirs` and `extra-lib-dirs`. (**NOTE: specifying anything other than the default is considered experimental**)

## XDG support

To enable XDG style directories, set the environment variable `GHCUP_USE_XDG_DIRS` to anything.

Then you can control the locations via XDG environment variables as such:

* `XDG_DATA_HOME`: GHCs will be unpacked in `ghcup/ghc` subdir (default: `~/.local/share`)
* `XDG_CACHE_HOME`: logs and download files will be stored in `ghcup` subdir (default: `~/.cache`)
* `XDG_BIN_HOME`: binaries end up here (default: `~/.local/bin`)
* `XDG_CONFIG_HOME`: the config file is stored in `ghcup` subdir as `config.yaml` (default: `~/.config`)

**Note that `ghcup` makes some assumptions about structure of files in `XDG_BIN_HOME`. So if you have other tools
installing e.g. stack/cabal/ghc into it, this will likely clash. In that case consider disabling XDG support.**

## Bubblewrap support

You can configure ghcup to execute `make` and `configure` through bubblewrap.
This can be useful if you're using 3rd-party channels and want to be protected
from build system bugs.

E.g. if your home directory is `/home/wurst`, then you'd configure it as such:

```yml
build-wrapper:
  cmd: bwrap
  cmdArgs:
    [ --ro-bind, /, /
    , --bind, /home/hasufell/.ghcup, /home/hasufell/.ghcup
    , --bind, /home/hasufell/.cabal, /home/hasufell/.cabal
    , --dev, /dev
    , --proc, /proc
    , --tmpfs, /tmp
    ]
```
