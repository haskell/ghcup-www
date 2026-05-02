# User Guide

This is a more in-depth guide specific to GHCup. `ghcup --help` is your friend.

## Basic usage

For the simple, interactive, text-based user interface (TUI), run:

```sh
ghcup tui
```

For the full functionality via cli:

```sh
# list available ghc/cabal versions
ghcup list

# install the recommended GHC version
ghcup install ghc

# install a specific GHC version
ghcup install ghc 8.2.2

# set the currently "active" GHC version
ghcup set ghc 8.4.4

# install cabal-install
ghcup install cabal

# update ghcup itself
ghcup upgrade
```

### Tags and shortcuts

GHCup has a number of tags and version shortcuts, that can be used as arguments to **install**/**set** etc.
All of the following are valid arguments to `ghcup install ghc`:

* `latest`, `recommended`
* `base-4.15.1.0`
* `9.0.2`, `9.0`, `9`

If the argument is omitted, the default is `recommended`.

Other tags include:

- `prerelease`: a prerelease version
- `latest-prerelease`: the latest prerelease version


## Manpages

For man pages to work you need [man-db](http://man-db.nongnu.org/) as your `man` provider, then issue `man ghc`. Manpages only work for the currently set ghc.
`MANPATH` may be required to be unset.

## Pager

You can have `ghcup list` use a pager, similar to git. E.g. run:

```sh
ghcup --paginate list
```

To set a specific pager you can use either `GHCUP_PAGER` or `PAGER` environment variable.

To make the changes permanent, you can add the following to your config:

```yaml
pager: most
```

Refer to the [config.yaml](https://github.com/haskell/ghcup-hs/blob/master/data/config.yaml) template for more fine-grained
control.

## Shell-completion

Shell completions are in [scripts/shell-completions](https://github.com/haskell/ghcup-hs/tree/master/scripts/shell-completions) directory of this repository.

For bash: install `shell-completions/bash`
as e.g. `/etc/bash_completion.d/ghcup` (depending on distro)
and make sure your bashrc sources the startup script
(`/usr/share/bash-completion/bash_completion` on some distros).

## Portability

`ghcup` is very portable. There are a few exceptions though:

1. legacy subcommands `ghcup install` (without a tool identifier) and `ghcup install-cabal` may be removed in the future

## Caching

GHCup has a few caching mechanisms to avoid redownloads. All cached files end up in `~/.ghcup/cache` by default.

### Downloads cache

Downloaded tarballs (such as GHC, cabal, etc.) are not cached by default unless you pass `ghcup --cache` or set caching
in your [config](config.md) via `ghcup config set cache true`.

### Metadata cache

The metadata files (also see [github.com/haskell/ghcup-metadata](https://github.com/haskell/ghcup-metadata))
have a 5 minutes cache per default depending on the last access time of the file. That means if you run
`ghcup list` 10 times in a row, only the first time will trigger a download attempt.

### Clearing the cache

If you experience problems, consider clearing the cache via `ghcup gc --cache`.

## Mirrors (proper)

Mirrors are now supported via configuration, instead of specifying alternative metadata files.

As an example, this would be a complete mirror configuration in `~/.ghcup/config.yaml`:

```yaml
mirrors:
  # yaml download location, would result in:
  #      https://raw.githubusercontent.com/haskell/ghcup-metadata/develop/ghcup-0.0.9.yaml
  #   -> https://mirror.sjtu.edu.cn/ghcup/yaml/haskell/ghcup-metadata/master/ghcup-0.0.9.yaml
  "raw.githubusercontent.com":
    authority:
      host: "mirror.sjtu.edu.cn"
    pathPrefix: "ghcup/yaml"
  # for stack and some older HLS versions, would result in e.g.
  #      https://github.com/haskell/haskell-language-server/releases/download/1.2.0/haskell-language-server-Windows-1.2.0.tar.gz
  #   -> https://mirror.sjtu.edu.cn/ghcup/github/haskell/haskell-language-server/releases/download/1.2.0/haskell-language-server-Windows-1.2.0.tar.gz
  "github.com":
    authority:
      host: "mirror.sjtu.edu.cn"
    pathPrefix: "ghcup/github"
  # for all haskell.org hosted bindists, would result in e.g.
  #      https://downloads.haskell.org/~ghc/9.8.1/ghc-9.8.1-x86_64-deb10-linux.tar.xz
  #   -> https://mirror.sjtu.edu.cn/ghcup/haskell-downloads/~ghc/9.8.1/ghc-9.8.1-x86_64-deb10-linux.tar.xz
  "downloads.haskell.org":
    authority:
      host: "mirror.sjtu.edu.cn"
    pathPrefix: "downloads.haskell.org"
```

The configuration depends on the host of the mirror and they have to provide the correct configuration.

## Linkers

The GHC bindist configure script by default doesn't honour the system `ld` that is set, but instead
probes for `ld.lld`, `ld.gold` and only then `ld` in order, see
[find_ld.m4](https://gitlab.haskell.org/ghc/ghc/-/blob/master/m4/find_ld.m4?ref_type=heads).

This is controlled by the configure switch `--enable-ld-override`/`--disable-ld-override`, which is enabled by default in GHC.
GHCup however [has decided](https://github.com/haskell/ghcup-hs/issues/1032) **to disable this switch by default**,
for reasons of stability and simplicity.

That means, when `--disable-ld-override` is passed, the linker is picked simply by:

* checking if `LD` env var is set, then use whatever is specified
* otherwise use `ld` binary in PATH (system/distro default)

You can restore the GHC vanilla default by adding this to your `~/.ghcup/config.yaml`:

```yaml
def-ghc-conf-options:
  - "--enable-ld-override"
```

## Continuous integration

On Windows, GHCup can be installed automatically on a CI runner
non-interactively, as below. The parameters to the PowerShell script are
specified positionally, after `-ArgumentList`:

```ps
$ErrorActionPreference = 'Stop';Set-ExecutionPolicy Bypass -Scope Process -Force;[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;try { & ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -Minimal -InBash -InstallDir "C:\" } catch { Write-Error $_ }
```

`$ErrorActionPreference = 'Stop'` here acts like `set -e` and stops execution if ghcup installation fails.

On linux/darwin/freebsd, run the following on your runner:

```sh
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | BOOTSTRAP_HASKELL_NONINTERACTIVE=1 BOOTSTRAP_HASKELL_MINIMAL=1 sh
```

This will just install `ghcup` and on Windows additionally MSYS2.

See the installation scripts referred to above for the full list of environment
variables and, in the case of Windows, parameters to tweak the script behavior.

### github workflows

We provide a GitHub action [haskell/ghcup-setup](https://github.com/marketplace/actions/ghcup-setup).

You may also be interested in my blog post on [Dynamic GHC matrix in GitHub CI](https://hasufell.github.io/posts/2026-01-22-dynamic-ghcup-action.html).

## GPG verification

GHCup supports verifying the GPG signature of the metadata file. The metadata file then contains SHA256 hashes of all downloads, so
this is cryptographically secure.

First, obtain the gpg keys:

```sh
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys 7D1E8AFD1D4A16D71FADA2F2CCC85C0E40C06A8C
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys FE5AB6C91FEA597C3B31180B73EDE9E8CFBAEF01
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys 88B57FCF7DB53B4DB3BFA4B1588764FBE22D19C4
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys EAF2A9A722C0C96F2B431CA511AAD8CEDEE0CAEF
```

Then verify the gpg key in one of these ways:

1. find out where I live and visit me to do offline key signing
2. figure out my mobile phone number and call me to verify the fingerprint
3. more boring: contact me on Libera IRC (`maerwald`) and verify the fingerprint

Once you've verified the key, you have to figure out if you trust me.

If you trust me, then you can configure gpg in `~/.ghcup/config.yaml`:

```yml
gpg-setting: GPGLax # GPGStrict | GPGLax | GPGNone
```

In `GPGStrict` mode, ghcup will fail if verification fails. In `GPGLax` mode it will just print a warning.
You can also pass the mode via `ghcup --gpg <strict|lax|none>`.

## Tips and tricks

### ghcup run

If you don't want to explicitly switch the active GHC all the time and are using
tools that rely on the plain `ghc` binary, GHCup provides an easy way to execute
commands with a certain toolchain prepended to PATH, e.g.:

```sh
ghcup run --ghc 8.10.7 --cabal latest --hls latest --stack latest --install -- code Setup.hs
```

This will execute vscode with GHC set to 8.10.7 and all other tools to their latest version.

