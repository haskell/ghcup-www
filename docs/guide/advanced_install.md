# More on installation

## Customisation of the installation scripts

The scripts offered to install GHCup are available here:

* [bootstrap-haskell](https://github.com/haskell/ghcup-hs/blob/master/scripts/bootstrap/bootstrap-haskell#L7)
  for Unix-like operating systems
* [bootstrap-haskell.ps1](https://github.com/haskell/ghcup-hs/blob/master/scripts/bootstrap/bootstrap-haskell.ps1)
  for Windows (PowerShell). This will, in turn, run the final bootstrap script
  (by default, that for the Unix-like operating systems).

The effect of the scripts can be customised by setting one or more
`BOOTSTRAP_HASKELL_*` environment variables (as set out in the first script)
and, in the case of Windows, by specifying parameters (as set out in the
PowerShell script).

For example, you can toggle:

* non-interactive installation
* a more verbose installation
* whether to install only GHCup (and, on Windows, MSYS2)
* not to trigger the upgrade of GHCup
* whether to install the latest version of HLS
* whether to install the latest version of Stack
* whether to respect the XDG Base Directory Specification
* whether to adjust (prepend) the PATH in `bashrc`
* on Windows, whether to adjust MINGW paths in `cabal.config`

You can also specify:

* the GHC version to install
* the Cabal version to install
* which downloader to use (the default is `curl`)
* the base URL for the download of the GHCup binary distribution

On Windows, you can also use the parameters to:

* toggle whether to overwrite a previous installation
* specify the GHCup installation root directory
* specify the Cabal root directory
* specify the directory of an existing installation of MSYS2 (for example,
  the one supplied by Stack)
* specify the URL of the final bootstrap script
* toggle whether to run the final bootstrap script via `bash` (instead of in a
  new MSYS2 shell)

## Installing custom bindists

There are a couple of good use cases to install custom bindists:

1. manually built bindists (e.g. with patches)
    - example: `ghcup install ghc -u 'file:///home/mearwald/tmp/ghc-eff-patches/ghc-8.10.2-x86_64-deb10-linux.tar.xz' 8.10.2-eff`
2. GHC head CI bindists
    - example specifying a branch (`master`): `ghcup install ghc -u 'https://gitlab.haskell.org/ghc/ghc/-/jobs/artifacts/master/raw/ghc-x86_64-linux-fedora33-release.tar.xz?job=x86_64-linux-fedora33-release' head`
    - example specifying a job id (`1129565`): `ghcup install ghc -u ' https://gitlab.haskell.org/api/v4/projects/1/jobs/1129565/artifacts/ghc-x86_64-linux-alpine3_12-validate+fully_static.tar.xz' mr7847`
3. DWARF bindists
    - example: `ghcup install ghc -u 'https://downloads.haskell.org/~ghc/8.10.2/ghc-8.10.2-x86_64-deb10-linux-dwarf.tar.xz' 8.10.2-dwarf`

Since the version parser is pretty lax, `8.10.2-eff` and `head` are both valid versions
and produce the binaries `ghc-8.10.2-eff` and `ghc-head` respectively.
GHCup always needs to know which version the bindist corresponds to (this is not automatically
detected).

## Isolated installs

**Before using isolated installs, make sure to have at least GHCup version 0.1.17.8!**

Ghcup also enables you to install a tool (GHC, Cabal, HLS, Stack) at an isolated location of your choosing.
These installs, as the name suggests, are separate from your main installs and DO NOT conflict with them.


- No symlinks are made to these isolated installed tools, you'd have to manually point to them wherever you intend to use them.

- These installs, can also NOT be deleted from ghcup, you'd have to go and manually delete these.

You need to use the `--isolate` or `-i` flag followed by the directory path.

Examples:

1. install an isolated GHC version at location /home/user/isolated_dir/ghc/
    - `ghcup install ghc 8.10.5 --isolate /home/user/isolated_dir/ghc`

2. isolated install Cabal at a location you desire
    - `ghcup install cabal --isolate /home/username/my_isolated_dir/`

3. do an isolated install with a custom bindist
    - `ghcup install ghc --isolate /home/username/my_isolated_dir/ -u 'https://gitlab.haskell.org/ghc/ghc/-/jobs/artifacts/master/raw/ghc-x86_64-linux-fedora33-release.tar.xz?job=x86_64-linux-fedora33-release' head`

4. isolated install HLS
    - `ghcup install hls --isolate /home/username/dir/hls/`

5. you can even compile ghc to an isolated location.
    - `ghcup compile ghc -j 4 -v 9.0.1 -b 8.10.5 -i /home/username/my/dir/ghc`
