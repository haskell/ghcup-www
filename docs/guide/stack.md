# Stack integration

By default, Stack tries to manage GHC versions itself. However, Stack can use
GHC versions installed by GHCup. There are two strategies for doing so.

## Strategy 1 (recommended): Hook up Stack to use GHCup

Stack (since v2.9.1) can be configured to try to use GHCup to manage GHC
versions, using a 'hook'.

The commands to [install GHCup and the Haskell Toolchain](../install/index.md) include an
option to create that hook. That is the recommended way to do so. They can
safely be run again at any time.

A hook can also be created manually, by placing a suitable `sh` script named
`ghc-install.sh` in a `hooks` directory in the Stack root. On Unix-like
operating systems, the script file should be marked as executable.

You can create a hook manually with the following steps:

```text
# Change to the Stack root directory:
cd "$(stack path --stack-root)"
# Make a hooks directory (if it does not exist):
mkdir hooks
# Change to that hooks directory:
cd hooks
# Download GHCup's ghc-install.sh file:
curl -O https://raw.githubusercontent.com/haskell/ghcup-hs/master/scripts/hooks/stack/ghc-install.sh
# On Unix-like operating systems only, mark file as executable:
chmod +x ghc-install.sh
# Hooks are only run when 'system-ghc: false' (Stack's default):
stack config set system-ghc false --global
```

If the hook fails, by default, Stack will try to manage GHC versions itself.
Stack can be configured not to do that for any project (globally). To do so,
command:

```text
stack config set install-ghc false --global
```

For more information about Stack hooks, see Stack's
[GHC installation customisation](https://docs.haskellstack.org/en/stable/configure/customisation_scripts/#ghc-installation-customisation)
documentation.

## Strategy 2 (alternative): Use only GHC on the PATH

Stack can be configured for all projects (globally) to try to use the 'system'
GHC on the PATH (including one installed by GHCup) and not to try to manage GHC
versions itself. To do so, command:

```text
stack config set system-ghc true --global
stack config set install-ghc false --global
```

## Using stack's setup-info metadata to install GHC

You can now use stack's [setup-info metadata](https://github.com/commercialhaskell/stackage-content/blob/master/stack/stack-setup-2.yaml)
to install GHC. For that, you can invoke ghcup like so as a shorthand:

```sh
# ghcup will only see GHC now
ghcup -s StackSetupURL install ghc 9.4.7
# this combines both ghcup and stack metadata
ghcup -s '["GHCupURL", "StackSetupURL"]' install ghc 9.4.7
```

To make this permanent and combine it with the GHCup metadata, you can add the following to your `~/.ghcup/config.yaml`:

```yaml
url-source:
  - GHCupURL
  # stack versions take precedence
  # you'll still have access to GHCup provided versions and tools in case they don't exist in stack metadata
  - StackSetupURL
```

You can customize or add sections to the setup-info similar to how the
[stack documentation](https://docs.haskellstack.org/en/stable/configure/yaml/non-project/#setup-info)
explains it. E.g. to change the 9.4.7 bindist, you might do:

```yaml
url-source:
  - GHCupURL
  - StackSetupURL
  - setup-info:
      ghc:
        linux64-tinfo6:
          9.4.7:
            url: "https://downloads.haskell.org/~ghc/9.4.7/ghc-9.4.7-x86_64-fedora27-linux.tar.xz"
            content-length: 179117892
            sha256: 216b76b7c6383e6ad9ba82533f323f8550e52893a8b9fa33c7b9dc4201ac766a
```

### Caveats

The main caveat with using this method is that there's no guarantee that GHCup will pick a compatible HLS bindist
when you try to install HLS.

Another potential usability issue is that the `latest` and `recommended` shorthands won't work anymore, since
Stack metadata doesn't have a concept of those and we don't try to be smart when combining the metadatas.

## Windows

### Using GHCup's MSYS2 installation

Stack usually maintains its own msys2 installation. However, you can instruct it to use GHCup's MSYS2 or any other. E.g. if you
had GHCup install msys2 into `C:\ghcup\msys64\`, then you would add the following config to stack's `config.yaml`
(you can find its location via `stack path --stack-root`):

```yaml
skip-msys: true
extra-lib-dirs:
- C:\ghcup\msys64\mingw64\lib
- C:\ghcup\msys64\mingw64\bin
extra-path:
- C:\ghcup\msys64\mingw64\bin
- C:\ghcup\msys64\usr\bin
- C:\ghcup\msys64\usr\local\bin
extra-include-dirs:
- C:\ghcup\msys64\mingw64\include
```

Also check out:
[https://docs.haskellstack.org/en/stable/configure/yaml/non-project](https://docs.haskellstack.org/en/stable/configure/yaml/non-project)
