# zshrs-git-fuzzy

[git-fuzzy](https://github.com/bigH/git-fuzzy) — the full-screen `fzf`-driven
`git` interface — ported to a **native
[zshrs](https://github.com/MenkeTechnologies/zshrs) plugin**. This port covers
the `status` command end-to-end.

git-fuzzy is a *self-reentrant* fzf UI: every preview and keybinding calls
back into the tool. In bash that re-execs the script and re-sources its
library **per keystroke** (git-fuzzy even ships "dispatch-aware sourcing" to
make that cheap). As a compiled plugin, the helpers are builtins — no
per-keystroke library sourcing.

## Commands

| command     | what it does                              |
| ----------- | ----------------------------------------- |
| `gf status` | interactive `git status` UI               |

Inside the `status` view: live diff preview, full-screen inspect, and
key-bindings to stage / unstage / discard / amend / patch / commit / edit,
plus a `--listen`-driven watcher that live-reloads on repo changes.

Requires `git` and `fzf` (>= 0.71) on `PATH`. `delta` / `diff-so-fancy` are
used for diff rendering when present.

## Install

With **zpm** (zshrs's package manager):

```sh
zpm add MenkeTechnologies/zshrs-git-fuzzy
```

`zpm` clones the repo, runs `cargo build --release`, and `zmodload -R`s the
resulting `libgit_fuzzy` — then `gf` is a live command. To load it at startup,
add `zpm load git-fuzzy` to your `.zshrc`.

## Build manually

```sh
cargo build --release
zmodload -R ./target/release/libgit_fuzzy.dylib   # .so on Linux
gf status
```

## How it was ported

The self-reentrant pattern is documented in the zshrs plugin porting guide,
"self-reentrant fzf tools":
[docs/PORTING_ZSH_PLUGIN.md](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/PORTING_ZSH_PLUGIN.md).
fzf runs bind/preview commands via `sh`, which can't call a plugin builtin, so
a generated shim runs `zshrs -fc 'zmodload -R <self>; gf --helper …'` — one
`dlopen` of an mmap'd dylib per action.

## License

MIT. Ported from [bigH/git-fuzzy](https://github.com/bigH/git-fuzzy) (MIT). See
[LICENSE](LICENSE).
