# Cross support

ghcup can compile a cross GHC for any target. However, this
requires that the build host has a complete cross toolchain and various
libraries installed for the target platform.

Consult the GHC documentation on the [prerequisites](https://gitlab.haskell.org/ghc/ghc/-/wikis/building/cross-compiling#tools-to-install).
For distributions with non-standard locations of cross toolchain and
libraries, this may need some tweaking of `build.mk` or configure args.
See `ghcup compile ghc --help` for further information.

Since ghcup version 0.1.20.0, we provide cross bindists for GHC JS and WASM. These can be installed conveniently.
However, these are intended as a developer preview only. By using these GHC variants, you are implicitly signing up to participate in GHC development!
If you run into bugs or missing behavior, join the dev chat at https://matrix.to/#/#GHC:matrix.org.

First, add the cross release channel:

```sh
ghcup config add-release-channel cross
```

The next sections explain how to install each cross bindist.

## GHC JS cross bindists (experimental)

You need the required emscripten JS toolchain. GHC JS cross bindists might require you to install a specific
version of emscripten. If that is the case, then ghcup will display the required emscripten version in the
pre install message. You can use the following commands to install the emscripten toolchain on your system,
substituting the required version for the bindist that you want to install.
(Cf. [GHC-MR 10918](https://gitlab.haskell.org/ghc/ghc/-/merge_requests/10918))

```sh
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install VERSION
./emsdk activate VERSION
source ./emsdk_env.sh
```

Instructions are also here: [Download and install — Emscripten documentation](https://emscripten.org/docs/getting_started/downloads.html).

To install you can either use the tui interface by invoking `emconfigure ghcup tui` or
you can install directly like so:

```sh
emconfigure ghcup install ghc --set javascript-unknown-ghcjs-9.12.2
```

You'll now have the compiler `javascript-unknown-ghcjs-ghc`. To build a hello world, do e.g.:

```sh
echo 'main = putStrLn "hello world"' > hello.hs
javascript-unknown-ghcjs-ghc -fforce-recomp hello.hs
./hello
```

You can follow the instructions [here](https://gitlab.haskell.org/ghc/ghc/-/wikis/javascript-backend/building#compiling-hello-world).

## GHC WASM cross bindists (experimental)

You need the required wasm toolchain:

```sh
git clone https://gitlab.haskell.org/ghc/ghc-wasm-meta.git
cd ghc-wasm-meta/
export SKIP_GHC=yes
./setup.sh
source ~/.ghc-wasm/env
```

**Note that some wasm bindists don't work with the master branch of ghc-wasm-meta. GHCup will warn you about such cases prior to installation and point you to the right commit.**

To install, we need to invoke ghcup like so also passing the `--host=<host>` flag (adjust as needed):

```sh
ghcup install ghc --set wasm32-wasi-9.6.3.20230927 -- --host=x86_64-linux --with-intree-gmp --with-system-libffi
```

Also check the documentation here: [Glasgow Haskell Compiler / ghc-wasm-meta](https://gitlab.haskell.org/ghc/ghc-wasm-meta).

You'll now have the compiler `wasm32-wasi-ghc`. To build a hello world, do e.g.:

```sh
echo 'main = putStrLn "hello world"' > hello.hs
wasm32-wasi-ghc hello.hs -o hello.wasm
wasmtime ./hello.wasm
```
