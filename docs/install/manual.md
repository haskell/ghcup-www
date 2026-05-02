# Manual installation

## Unix

Download the binary for your platform at [https://downloads.haskell.org/~ghcup/](https://downloads.haskell.org/~ghcup/)
and place it into your `PATH` anywhere.

If you want to GPG verify the binaries, import the following keys first:

```sh
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys 7D1E8AFD1D4A16D71FADA2F2CCC85C0E40C06A8C
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys FE5AB6C91FEA597C3B31180B73EDE9E8CFBAEF01
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys 88B57FCF7DB53B4DB3BFA4B1588764FBE22D19C4
gpg --batch --keyserver keyserver.ubuntu.com --recv-keys EAF2A9A722C0C96F2B431CA511AAD8CEDEE0CAEF
```

Then adjust your `PATH` in `~/.bashrc` (or similar, depending on your shell) like so:

```sh
export PATH="$HOME/.cabal/bin:$HOME/.ghcup/bin:$PATH"
```

## Windows

1. Install ghcup binary
    - choose a base directory for installation, e.g. `C:\` that has sufficient space
    - then create the directory, e.g. `C:\ghcup\bin`
    - download the binary: https://downloads.haskell.org/~ghcup/x86_64-mingw64-ghcup.exe
    - place it as `ghcup.exe` into e.g. `C:\ghcup\bin`
2. Install MSYS2
    - download https://repo.msys2.org/distrib/msys2-x86_64-latest.exe and execute it
    - remember the installation destination you choose (default is `C:\msys64`)
    - finish the installation
* Add environment variables and update `Path`
    - open search bar and type in "Edit the system environment variables", then open it
    - click on "Environment Variables..." at the near bottom
    - in the upper half, select `Path` variable and double click on it
    - in the new window, click "New", type in `C:\ghcup\bin` (depending on step 1.) and press enter
    - click "OK" at the bottom
    - in the upper half, click on "New..."
    - enter `GHCUP_MSYS2` under "Variable name" and the installation destination from step 2. under "Variable value"
    - click "OK" at the bottom
    - in the upper half, click on "New..."
    - enter `GHCUP_INSTALL_BASE_PREFIX` under "Variable name" and based on the installation destination from step 1. enter the device directory (default `C:\`)
    - click "OK" at the bottom
    - in the upper half, click on "New..."
    - enter `CABAL_DIR` under "Variable name" and based on the installation destination from step 1. enter the device directory + `cabal` subdir (default `C:\cabal`)
    - click "OK" at the bottom
    - click "OK" at the bottom
    - click "OK" at the bottom
3. Install tools
    - open powershell
    - run `ghcup install ghc --set recommended`
    - run `ghcup install cabal latest`
    - run `ghcup install stack latest`
    - run `ghcup install hls latest`
    - run `cabal update`
4. Update msys2
    - run `ghcup run -m -- pacman --noconfirm -Syuu`
    - run `ghcup run -m -- pacman --noconfirm -Syuu`
    - run `ghcup run -m -- pacman --noconfirm -S --needed curl autoconf mingw-w64-x86_64-pkgconf`
    - run `ghcup run -m -- pacman --noconfirm -S ca-certificates`
5. Update cabal config
    - go to e.g. `C:\cabal` (based on device you picked in 1.)
    - open file `config`
    - uncomment `extra-include-dirs` (the `-- `) and add the value (depending on installation destination you chose in 2.), e.g. `C:\msys64\mingw64\include`... so the final line should be `extra-include-dirs: C:\msys64\mingw64\include`
    - uncomment `extra-lib-dirs` and do the same, adding `C:\msys64\mingw64\lib`
    - uncomment `extra-prog-path` and set it to `C:\ghcup\bin, C:\cabal\bin, C:\msys64\mingw64\bin, C:\msys64\usr\bin`, depending on your install destinations from 1. and 2.
6. Set up msys2 shell
    - run `ghcup run -m -- sed -i -e 's/db_home:.*$/db_home: windows/' /etc/nsswitch.conf` to make the HOME in your msys2 shell match the one from windows
    - make a desktop shortcut from `C:\msys64\msys2_shell.cmd`, which will allow you to start a proper msys2 shell
    - run `ghcup run -m -- sed -i -e 's/#MSYS2_PATH_TYPE=.*/MSYS2_PATH_TYPE=inherit/' /c/msys64/msys2.ini`
    - run `ghcup run -m -- sed -i -e 's/rem set MSYS2_PATH_TYPE=inherit/set MSYS2_PATH_TYPE=inherit/' /c/msys64/msys2_shell.cmd`

All set. You can run `cabal init` now in an empty directory to start a project.
