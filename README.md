```
 ██████╗ ██╗████████╗   ███████╗██╗   ██╗███████╗███████╗██╗   ██╗
██╔════╝ ██║╚══██╔══╝   ██╔════╝██║   ██║╚══███╔╝╚══███╔╝╚██╗ ██╔╝
██║  ███╗██║   ██║█████╗█████╗  ██║   ██║  ███╔╝   ███╔╝  ╚████╔╝ 
██║   ██║██║   ██║╚════╝██╔══╝  ██║   ██║ ███╔╝   ███╔╝    ╚██╔╝  
╚██████╔╝██║   ██║      ██║     ╚██████╔╝███████╗███████╗   ██║   
 ╚═════╝ ╚═╝   ╚═╝      ╚═╝      ╚═════╝ ╚══════╝╚══════╝   ╚═╝   
                                                                  
```

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![zshrs plugin](https://img.shields.io/badge/zshrs-native%20plugin-blue.svg)](https://github.com/MenkeTechnologies/zshrs)

### `[FULL-SCREEN FZF GIT UI — COMPILED]`

> *"a self-reentrant fzf UI with no per-keystroke re-sourcing."*

## `[NATIVE ZSHRS PLUGIN]`

[git-fuzzy](https://github.com/bigH/git-fuzzy) — the full-screen `fzf`-driven `git` interface — ported to a **native [zshrs](https://github.com/MenkeTechnologies/zshrs) plugin**. This port covers the `status` command end-to-end.

### [`zshrs`](https://github.com/MenkeTechnologies/zshrs) &middot; [`znative`](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZNATIVE.md) &middot; [`upstream`](https://github.com/bigH/git-fuzzy)

---

## Table of Contents

- [\[0x00\] Overview](#0x00-overview)
- [\[0x01\] Install](#0x01-install)
- [\[0x02\] Commands](#0x02-commands)
- [\[0x03\] How it was ported](#0x03-how-it-was-ported)
- [\[0xFF\] License](#0xff-license)

---

## [0x00] OVERVIEW

git-fuzzy is a *self-reentrant* fzf UI: every preview and keybinding calls back into the tool. In bash that re-execs the script and re-sources its library **per keystroke** (git-fuzzy even ships "dispatch-aware sourcing" to make that cheap). As a compiled plugin, the helpers are builtins — no per-keystroke library sourcing.

Requires `git` and `fzf` (>= 0.71) on `PATH`. `delta` / `diff-so-fancy` are used for diff rendering when present.

---

## [0x01] INSTALL

```sh
znative load MenkeTechnologies/zshrs-git-fuzzy
```

Put that one line in your `.zshrc`. [znative](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZNATIVE.md), zshrs's package manager, installs the plugin on the first shell start — clones it, runs `cargo build --release`, and `zmodload -R`s the resulting `libgit_fuzzy` — then loads it from the store, zero-network, on every start after. No separate install step.

### Manual build

```sh
cargo build --release
zmodload -R ./target/release/libgit_fuzzy.dylib   # .so on Linux
gf status
```

---

## [0x02] COMMANDS

| command     | what it does                              |
| ----------- | ----------------------------------------- |
| `gf status` | interactive `git status` UI               |

Inside the `status` view: live diff preview, full-screen inspect, and key-bindings to stage / unstage / discard / amend / patch / commit / edit, plus a `--listen`-driven watcher that live-reloads on repo changes.

---

## [0x03] HOW IT WAS PORTED

The self-reentrant pattern is documented in the zshrs plugin porting guide, "self-reentrant fzf tools": [docs/PORTING_ZSH_PLUGIN.md](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/PORTING_ZSH_PLUGIN.md). fzf runs bind/preview commands via `sh`, which can't call a plugin builtin, so a generated shim runs `zshrs -fc 'zmodload -R <self>; gf --helper …'` — one `dlopen` of an mmap'd dylib per action.

---

## [0xFF] LICENSE

MIT. Ported from [bigH/git-fuzzy](https://github.com/bigH/git-fuzzy) (MIT). See [LICENSE](LICENSE).
