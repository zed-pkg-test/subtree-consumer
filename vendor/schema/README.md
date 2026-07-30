# shared-schema

One JSON Schema shared by several repositories. It exists to be **vendored**,
not installed: it is the payload for the three strategies compared in
[`submodule-consumer`](https://github.com/zed-pkg-test/submodule-consumer) and
[`subtree-consumer`](https://github.com/zed-pkg-test/subtree-consumer).

Deliberately not a zed package. The question those fixtures ask is what
`zed publish` does with content that arrived in the working tree by some
*git* mechanism rather than through the registry — so this side has to stay a
plain git repo.

## License

MIT
