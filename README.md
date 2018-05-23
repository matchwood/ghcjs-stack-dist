# GHCJS, repacked for stack lts8.x/lts9.x

The ghcjs versions referred to in [the stack docs](https://docs.haskellstack.org/en/stable/ghcjs/) do not cover stackage lts-8+ .

I have forked [ghcjs](https://github.com/matchwood/ghcjs), [ghcjs-base](https://github.com/matchwood/ghcjs-base) and [ghcjs-boot](https://github.com/matchwood/ghcjs-boot), altered a few patches and so forth and put together a working archive for use with stack snapshots lts-8.11 and lts-9.21 (though they should work reasonably well for any 8.x and 9.x snapshots)

I have only tested these on linux, they have **not been tested on Windows**. If you'd like to use them on Windows then please give them a go, and let me know if you encounter issues.


## How to use
In your stack.yaml, instead of using eg `http://ghcjs.tolysz.org/ghc-8.0-2017-01-11-lts-7.15-9007015.tar.gz` simply link to the archive in this git repo. Please be aware of possible issues with `nodenv` and `cabal` - see below for more details.
Eg:

    resolver: lts-8.11
    compiler: ghcjs-0.2.1.9008011_ghc-8.0.2
    compiler-check: match-exact
    setup-info:
      ghcjs:
        source:
          ghcjs-0.2.1.9008011_ghc-8.0.2:
            url: https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008011.tar.gz
            sha1: a72a5181124baf64bcd0e68a8726e65914473b3b


The following snapshots are currently supported:

| Resolver | Compiler | Url | sha1 |
| --- | --- | --- | --- |
| lts-8.11 | ghcjs-0.2.1.9008011_ghc-8.0.2 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008011.tar.gz | a72a5181124baf64bcd0e68a8726e65914473b3b |
| lts-9.21 | ghcjs-0.2.1.9009021_ghc-8.0.2 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9009021.1.tar.gz | db8cd3252e47dbef3db7d1cf0d6d997e8d029f84 |

There was a [bytestring issue](https://github.com/matchwood/ghcjs-stack-dist/issues/11) in the original lts9.21 release.

## Cautionary notes
While I have used these repacked versions of ghcjs to successfully compile working applications (including reflex and reflex-dom) they do fail a couple of tests - see these issues for more information https://github.com/ghcjs/ghcjs/issues/571 & https://github.com/ghcjs/ghcjs/issues/644.

## IMPORTANT: note on Aeson for lts8.x
GHCJS depends on Aeson as a boot package. The version of Aeson in lts-8* includes some C code, and there is not currently a fully operational JS shim for it - see [this issue](https://github.com/bos/aeson/issues/471) for more information. Starting from 1.1.1.0 aeson offers a pure Haskell implementation via a flag. So from the perspective of ghcjs, you have two options -
  1) Implement your own JS shim
  2) Use aeson 1.1.1.0 +

I have gone for the second option for these distributions of ghcjs, so they come with aeson 1.1.1.0 as a boot package. This means that, firstly, you might need `allow-newer: true` in your stack.yaml, and secondly that stack will give you warnings about this, eg:

    Ignoring that the GHCJS boot package "aeson" has a different version, 1.1.1.0, than the resolver's wanted version, 1.0.2.1

You may also get out of range warnings from other packages, like

    WARNING: Ignoring out of range dependency (allow-newer enabled): aeson-1.1.1.0. reflex-dom requires: >=0.8 && <1.1

This is far from ideal - the api changes in aeson 1.1.0.0 are fortunately [not massive](https://github.com/bos/aeson/blob/master/changelog.md) but may break some packages (only the addition of instances for the `UUID` type should be an issue). lts-9 includes a new version of aeson, and this issue is not a problem with the lts-9.21 release.

### Consistency with server side code if using lts8.x
Bear in mind that if you are also compiling code with ghc (sharing code between server and client for instance) then you will probably want to use the same version of aeson in your server project stack.yaml, i.e. add `- aeson-1.1.1.0` to your `extra-deps` section.

### Cabal version
The ghcjs code used in the 9.21 distribution makes use of some features of cabal 2, despite the fact that the cabal version in the lts-9.21 snapshot is at 1.24. If you try to install this distribution when you either have cabal installed in your working directory or a global cabal < 2 then you will run into an error that looks like 

```
    fatal: cabal-install program /Users/user/.local/bin/cabal does not support --allow-boot-library-installs (requires version 2.0.0.0 or newer)
    Booting GHCJS (this will take a long time) ...Process exited with ExitFailure 1: /Users/user/.stack/programs/x86_64-osx/ghcjs-0.2.1.9009021_ghc-8.0.2/src/.stack-work/install/x86_64-osx/lts-9.21/8.0.2/bin/ghcjs-boot --clean

```

All you need to do is to ensure that you have cabal 2 installed globally, and if necessary temporarily rename the cabal executable in your local `.stack-work` folder.

### Issues with Nodenv
If you are using `nodenv` to manage your node installations, you are better off disabling this before doing your first `stack setup` (or `stack build`), which will cause GHCJS to be downloaded and set up.

Related to [this issue](https://github.com/ghcjs/ghcjs/issues/398), this is because GHCJS will catch the path to the `nodenv` shim, which causes issues down the line with `var: command not found` errors, typically looking something like,

```bash
    /Users/tehnix/.stack/setup-exe-cache/x86_64-osx/Cabal-simple_mPHDZzAJ_1.24.2.0_ghcjs-0.2.1.9009021_ghc-8.0.2: line 40: var: command not found
    /Users/tehnix/.stack/setup-exe-cache/x86_64-osx/Cabal-simple_mPHDZzAJ_1.24.2.0_ghcjs-0.2.1.9009021_ghc-8.0.2: line 41: syntax error near unexpected token `h$RTSD_0'
    /Users/tehnix/.stack/setup-exe-cache/x86_64-osx/Cabal-simple_mPHDZzAJ_1.24.2.0_ghcjs-0.2.1.9009021_ghc-8.0.2: line 41: `function h$getReg(h$RTSD_0)'
```

It should be sufficient to simply disable it during the setup process.
