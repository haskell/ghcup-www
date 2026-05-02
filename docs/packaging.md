# Packaging

Since version 0.2.1.0, GHCup allows to package arbitrary binaries and configure/Makefile based bindists.
New tools can be added ad-hoc to the existing metadata with an "install specification" that tells
ghcup how to install and symlink these tools.

## Example

```yml
ghcupDownloads:
  dhall:
    1.42.2:
      viTags:
        - Latest
        - Recommended
      viArch:
        A_64:
          Linux_UnknownLinux:
            unknown_versioning:
              dlUri: https://github.com/dhall-lang/dhall-haskell/releases/download/1.42.2/dhall-1.42.2-x86_64-linux.tar.bz2
              dlHash: b2509bbe07772f268ef193d9c36b18aa13bd32ecec76fd4b21782715b38386a3
              dlInstallSpec: &dhall-install-spec1
                exeRules:
                  - installSource: "bin/dhall"
                exeSymLinked:
                 - linkName: "dhall-${PKGVER}"
                   pVPMajorLinks: true
                   setName: "dhall"
                   target: "bin/dhall"
          Darwin:
            unknown_versioning:
              dlUri: https://github.com/dhall-lang/dhall-haskell/releases/download/1.42.2/dhall-1.42.2-x86_64-darwin.tar.bz2
              dlHash: b1c5fd87b018d13d3a07db0561d1ffb906b7b840151fdb8ce00dc9f2f8dffe4a
              dlInstallSpec: *dhall-install-spec1
          Windows:
            unknown_versioning:
              dlUri: https://github.com/dhall-lang/dhall-haskell/releases/download/1.42.2/dhall-1.42.2-x86_64-windows.zip
              dlHash: c2447b8406d11611f43c47366fc93baa8061961736b46b9822077625cbd8ff9e
              dlInstallSpec: &dhall-install-win-spec1
                exeRules:
                  - installSource: "bin/dhall.exe"
                exeSymLinked:
                 - linkName: "dhall-${PKGVER}.exe"
                   pVPMajorLinks: true
                   setName: "dhall.exe"
                   target: "bin/dhall.exe"
```

Here we introduce the tool `dhall` version `1.42.2`. The main difference to previous metadata's is the following:

```yml
              dlInstallSpec: &dhall-install-spec1
                exeRules:
                  - installSource: "bin/dhall"
                exeSymLinked:
                 - linkName: "dhall-${PKGVER}"
                   pVPMajorLinks: true
                   setName: "dhall"
                   target: "bin/dhall"
```

We'll be explaining the format in detail next. All the objects described live under `dlInstallSpec`.

Also check the files in the [ghcup-metadata repository](https://github.com/haskell/ghcup-metadata) for more examples.

## Format

### Binaries

* `exeRules`: which executables to install
* `dataRules`: which data files to install
* `exeSymLinked`: how to symlink binaries into `~/.ghcup/bin`
* `configure`: whether and how to run a configure script
* `make`: whether and how to run a makefile (happens after configure)
* `preserveMtimes`: a bool, whether to preserve modification times of files (e.g. important for GHC)

`exeRules` and `dataRules` share the same structure. We can either install specific files, like so:

```yml
exeRules:
  - installSource: "dhall"
    installDest: "bin/dhall"
  - installSource: "dhall-to-json"
    installDest: "bin/dhall-to-json"
dataRules:
  - installSource: "dhall.1"
    installDest: "share/man1/dhall.1"
```

`installSource` describes the source file inside the tarball we extracted. `installDest` describes
where we install it. In the above case, the binary `dhall` would end up
in `~/.ghcup/dhall/1.42.2/bin/dhall`. We can omit `installDest`, then the file would be installed
in the same sub-path (in this case `~/.ghcup/dhall/1.42.2/dhall`).

The other way to specify is via wildcard patterns (these follow the rules from the [filepattern package](https://hackage.haskell.org/package/filepattern-0.1.3/docs/System-FilePattern.html#v:-63--61--61-)). This allows us to capture multiple files at once.
The downside is that you can't specify a different install destination. As an example:

```yml
dataRules:
  - installPattern: ["data/**"]
```

Say the tarball includes `data/share/man1/dhall.1` and `data/docs/README.md`, then they would be installed as:

* `~/.ghcup/dhall/1.42.2/dhall/data/share/man1/dhall.1`
* `~/.ghcup/dhall/1.42.2/dhall/data/docs/README.md`

These patterns also work for `exeRules`.

### Symlinks for binaries

We also need to tell ghcup how to symlink binaries into `~/.ghcup/bin/`. From the example above that is:

```yml
exeSymLinked:
  - target: "bin/dhall"
    linkName: "dhall-${PKGVER}"
    setName: "dhall"
    pVPMajorLinks: true
```

* `target`: where all the symlinks will point to
    - in this case `~/.ghcup/dhall/1.42.2/bin/dhall`
* `linkName` is what ends up in `~/.ghcup/bin/`
    - in this case: `~/.ghcup/bin/dhall-1.42.2`
* `setName`: which symlink to create when we run `ghcup set dhall 1.42.2`
    - in this case: `~/.ghcup/bin/dhall`
* `pVPMajorLinks`: whether to create the PVP symlinks
    - in this case: `~/.ghcup/bin/dhall-1.42`

`${PKGVER}` resolves to the package version.

If there are multiple binaries, it's important to understand that the first in the list will
be used for `ghcup whereis <tool>`.

#### Symlink patterns (advanced)

We can also specify symlinks as patterns, which is rather involved. This is used for complex things like HLS:

```yml
exeSymLinked:
- targetPattern: ["bin/haskell-language-server-wrapper*"]
  linkName: "${TARGETFN}-${PKGVER}"
  setName: "${TARGETFN}"
- targetPattern: ["bin/haskell-language-server-*"]
  targetPatternIgnore: ["bin/haskell-language-server-wrapper*"]
  linkName: "${TARGETFN}~${PKGVER}"
  setName: "${TARGETFN}"
```

Here `${TARGETFN}` resolves to the matched filename of each pattern in `targetPattern`. So the above actually
creates mappings for more than two binaries.

### Running make

Running make is as simple as:

```yml
make:
  makeArgs:
    - DESTDIR=${TMPDIR}
    - PREFIX=${PREFIX}
    - install
```

Any make build system supporting ghcup must support:

- `DESTDIR`: a standard make variable that is used to install into a temporary directory before merging to the actual destination (this path is prepended to every installed file)

If it's a makefile without configure, then we also must support:

- `PREFIX`: (or something equivalent)... this will be filled in by ghcup as `~/.ghcup/dhall/1.42.2/`

If we use a standard autotools configure + make, then we don't need `PREFIX`.

### Running configure

Configure is run before make. That allows standard autoconf systems, such as for GHC:

```yml
configure:
  configFile: configure
  configArgs:
    - --prefix=${PREFIX}
make:
  makeArgs:
    - DESTDIR=${TMPDIR}
    - install
```

`configFile` is optional.

It is also possible to only have configure and no make. Although I have not run across such a bindist yet.

### Make/Configure environment

Both make and configure can specify an environment, like so:

```yml
configure:
  configFile: configure
  configArgs:
    - --prefix=${PREFIX}
  configEnv:
    env:
      - ["CC", "clang"]
    union: PreferSpec
make:
  makeArgs:
    - DESTDIR=${TMPDIR}
    - install
  makeEnv:
    env:
      - ["SED", "gsed"]
    union: PreferSystem
```

`union` can be:

- `PreferSystem`: will not overwrite existing env variables
- `PreferSpec`: will overwrite existing env variables
- `OnlySpec`: will only consider env variables specified here

### Tool details

Unlike all the previous objects, this one doesn't live below `dlInstallSpec`, but directly under
the tool name.

In the above dhall example we have the yaml:

```yaml
ghcupDownloads:
  dhall:
    1.42.2:
      viTags:
        - Latest
        - Recommended
      viArch:
        A_64:
          Linux_UnknownLinux:
          # ... etc.
```

And so on. This is still valid yaml. But we can add information to the tool, such as
description and author, for that, we need to inject it like so:

```yaml
ghcupDownloads:
  dhall:
    toolDetails:
      toolHomepage: "https://dhall-lang.org/"
      toolRepository: "https://github.com/dhall-lang/dhall-lang"
      toolDescription: "Maintainable configuration files"
      toolAuthor: "Gabriella Gonzalez"
      toolMaintainer: "Gabriella Gonzalez"
      toolContact: "some-email@gmail.com"
    toolVersions:
      1.42.2:
        viTags:
          - Latest
          - Recommended
        viArch:
          A_64:
            Linux_UnknownLinux:
          # ... etc.
```

## What to do and what not to do

- do not compile during installation
    - GHCup is an installer and artifacts must be reproducible
    - compilation during autoconf is acceptable to figure out configuration values (GHC does this too)

## Using Dhall

GHCup now supports using Dhall instead of yaml. GHCup can output the schema:

```sh
ghcup generate-dhall-schema
```

We will also host the types in the [ghcup-metadata repository](https://github.com/haskell/ghcup-metadata/tree/develop/dhall).

Make sure to create a resolved dhall file (with no imports). GHCup will reject expressions that contain imports.
Dhall binary format is also supported.

