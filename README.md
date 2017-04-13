# GHCJS, repacked for stack lts8.0+

The ghcjs versions referred to in [the stack docs](https://docs.haskellstack.org/en/stable/ghcjs/) do not cover stackage lts8.0 + .

I have forked [ghcjs](https://github.com/matchwood/ghcjs), [ghcjs-base](https://github.com/matchwood/ghcjs-base) and [ghcjs-boot](https://github.com/matchwood/ghcjs-boot), altered a few patches and so forth and put together a working archive for use with stack snapshots lts8.0+. 

## How to use
In your stack.yaml, instead of using eg `http://ghcjs.tolysz.org/ghc-8.0-2017-01-11-lts-7.15-9007015.tar.gz` simply link to the archive in this git repo.
Eg: 

    resolver: lts-8.0
    compiler: ghcjs-0.2.1.9008000_ghc-8.0.2
    compiler-check: match-exact
    setup-info:
      ghcjs:
        source:
          ghcjs-0.2.1.9008000_ghc-8.0.2:
            url: https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008000.tar.gz

The following snapshots are currently supported:

| Resolver | Url |
| --- | --- |
| lts8.0 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008000.tar.gz |
| lts8.5 | https://github.com/matchwood/ghcjs-stack-dist/raw/master/ghcjs-0.2.1.9008005.tar.gz |

