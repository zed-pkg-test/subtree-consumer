# subtree-consumer

Vendors [`shared-schema`](https://github.com/zed-pkg-test/shared-schema) with
**`git subtree`** at `vendor/schema` — the same payload and the same consuming
code as [`submodule-consumer`](https://github.com/zed-pkg-test/submodule-consumer),
so the two are directly comparable.

```console
$ git subtree add --prefix vendor/schema \
    https://github.com/zed-pkg-test/shared-schema.git main --squash
```

## Why this one cannot fail the way the submodule does

A subtree is not a pointer. The upstream files are copied into this repository's
own history and are ordinary tracked content:

```console
$ git ls-files vendor/schema
vendor/schema/LICENSE
vendor/schema/README.md
vendor/schema/schema.json
vendor/schema/version.txt
```

`zed pack` walks the filesystem, and a plain `git clone` already materializes
every tracked file — so there is no initialization step to forget. Packing from
a **fresh clone**, the exact case that silently drops content from
`submodule-consumer`, yields the complete artifact:

```console
$ git clone <this repo> fresh && cd fresh && zed pack
… 9 files
pkg/vendor/schema/schema.json      # present

$ tar xzf … && cd pkg && node -e 'console.log(require("./src/index.js").TITLE)'
GreetingEvent
```

No `vendor/schema/.git` pointer ships either, because there is no nested git
repository to leave one behind.

## The trade

| | submodule | subtree |
| --- | --- | --- |
| Publishes correctly from a fresh clone | ✗ needs `--init` | ✓ |
| Repo size | pointer only | full copy of upstream |
| Upstream history | preserved, separate | squashed in (with `--squash`) |
| Pulling updates | `git submodule update --remote` | `git subtree pull --prefix …` |
| Contributing upstream | ordinary commits in the submodule | `git subtree push` |

For a package that is *published*, subtree removes a whole class of release
failure: correctness no longer depends on the release job remembering a flag.
The cost is repo size and a clumsier upstream-contribution path.

A third option — vendoring by plain copy, with no git linkage — packs
identically to a subtree and loses the update path entirely; it is only worth it
when upstream is effectively frozen.

## License

MIT
