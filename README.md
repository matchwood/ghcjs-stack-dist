# GHCJS, repacked for stack lts8.0+

The ghcjs versions referred to in [the stack docs](https://docs.haskellstack.org/en/stable/ghcjs/) do not cover stackage lts-8.0 + .

I have forked [ghcjs](https://github.com/matchwood/ghcjs), [ghcjs-base](https://github.com/matchwood/ghcjs-base) and [ghcjs-boot](https://github.com/matchwood/ghcjs-boot), altered a few patches and so forth and put together a working archive for use with stack snapshots lts-8.0+. 

I have only tested these on linux, they have **not been tested on Windows**. If you'd like to use them on Windows then please give them a go, and let me know if you encounter issues.

## How to use
In your stack.yaml, instead of using eg `http://ghcjs.tolysz.org/ghc-8.0-2017-01-11-lts-7.15-9007015.tar.gz` simply link to the archive in this git repo.
Eg: 

    resolver: lts-8.0
    compiler: ghcjs-0.2.1.9008000_ghc-8.0.2
    compiler-check: match-exact
    setup-info:
      ghcjs:
        source: |
          ghcjs-0.2.1.9008000_ghc-8.0.2:
            url: https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008000.tar.gz
            sha1: 815f26834ac25d72f58792524740fe3cca0ce3a6

The following snapshots are currently supported:

| Resolver | Url | sha1 |
| --- | --- | --- |
| lts-8.0 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008000.tar.gz | 815f26834ac25d72f58792524740fe3cca0ce3a6 |
| lts-8.5 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008005.tar.gz | 903ef8646719b688ccf6cd416e4d588a7d4ec911 |

## Cautionary notes
While I have used these repacked versions of ghcjs to successfully compile working applications (including reflex and reflex-dom) they do fail a couple of tests - see this issue for more information https://github.com/ghcjs/ghcjs/issues/571 . 
