# Installation

GHCup makes it easy to install specific versions of GHC on GNU/Linux,
macOS (aka Darwin), FreeBSD and Windows and can also bootstrap a fresh [Haskell developer environment](support.md#supported-tools) from scratch.
It follows the UNIX philosophy of [do one thing and do it well](https://en.wikipedia.org/wiki/Unix_philosophy#Do_One_Thing_and_Do_It_Well). Similar in scope to [rustup](https://github.com/rust-lang-nursery/rustup.rs), [pyenv](https://github.com/pyenv/pyenv) and [jenv](http://www.jenv.be).

## Availability of new versions

GHCup is a distribution channel. As such there may be a lag of 1-2 weeks after a release
of e.g. GHC has concluded. This involves manual QA, review of the metadata, creation of
unofficial bindists, etc.

GHCup is not involved in upstream's CI infrastructure or release process. It's a separate project.

Please do not ask when the new version of tool XY will be available.

## How to install

The following commands will use a `sh` script to download the `ghcup` binary into `~/.ghcup/bin` (or `C:\ghcup\bin` on windows) and then
run it to interactively install the [Haskell Toolchain](support.md#supported-tools). These commands should be run as **non-root/non-admin
user**.

These commands can safely be run again at any time.

For Linux, macOS, FreeBSD or Windows Subsystem 2 for Linux, run this in a terminal:

```sh
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```

For Windows, run this in a PowerShell session:

```psh
Set-ExecutionPolicy Bypass -Scope Process -Force;[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; try { & ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -Interactive -DisableCurl } catch { Write-Error $_ }
```

There's also a [youtube video](https://www.youtube.com/watch?v=bB4fmQiUYPw) explaining installation on windows.

If you want to know what these scripts do, check out the [source code at the repository](https://github.com/haskell/ghcup-hs/tree/master/scripts/bootstrap). Advanced users may want to perform a [manual installation](manual.md#manual-installation) and GPG verify the binaries.

### Which versions get installed?

GHCup has two main channels for every tool: **recommended** and **latest**. By default, it installs *recommended*.

*latest* follows the latest release of every tool, while *recommended* is at the discretion of the GHCup maintainers and based on community adoption (hackage libraries, tools like HLS, stackage support, etc.) and known bugs.

Also see [tags and shortcuts](../guide/index.md#tags-and-shortcuts) for more information.

## Uninstallation

On linux, just run `ghcup nuke`, then make sure any ghcup added lines in your `~/.bashrc` (or similar) are removed.

On windows, right click on the `Uninstall Haskell.ps1` PowerShell script on your Desktop and select *Run with PowerShell*.

## Next steps

1. Follow the [First steps guide](../steps/index.md) on how to build a "Hello world" program, use `ghc`, run an interactive REPL and create a Haskell project
2. To understand the difference and overlap of `stack` and `cabal`, read on [here](https://gist.github.com/merijn/8152d561fb8b011f9313c48d876ceb07)
3. To learn Haskell proper check out the links at [How to learn Haskell proper](../steps/where.md#how-to-learn-haskell-proper)
4. To learn more about Haskell Toolchain management, check out the [ghcup user guide](../guide/index.md)

## Integrations

### Vim integration

See [ghcup.vim](https://github.com/hasufell/ghcup.vim).

### VSCode integration
The developers of the Haskell Language Server offer an [extension](https://github.com/haskell/vscode-haskell) tightly integrated with the [Haskell Language Server](https://github.com/haskell/haskell-language-server). To get started:

1. Install GHCup. During installation, opt in to install the Haskell Language Server (HLS).
2. Install the extension (from VSCode: Ctrl + P and then `ext install haskell.haskell`).
3. Make sure your project uses the GHC version installed from GHCup (otherwise HLS is likely to fail on launch):
    - instructions for [stack](https://docs.haskellstack.org/en/stable/configure/yaml/non-project/#system-ghc)

On Linux, some users have reported an issue when VSCode is not launched from a terminal ("cannot find ghc version"). A solution is to [let HLS know about your GHCup on $PATH](https://github.com/haskell/vscode-haskell#stackcabalghc-can-not-be-found).

## Get help

* [Libera IRC chat on #haskell-ghcup or #haskell](https://kiwiirc.com/nextclient/irc.libera.chat/?nick=Guest%7C?#haskell,#haskell-ghcup)
* [GHCup issue tracker](https://github.com/haskell/ghcup-hs/issues/new)
* [Matrix](https://matrix.to/#/#ghcup:matrix.org)
* [Discord](https://discord.gg/WDqsWsnZfR)
