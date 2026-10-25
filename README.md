# Zephyr™ Mechanical Keyboard (ZMK) Firmware

[![Discord](https://img.shields.io/discord/719497620560543766)](https://zmk.dev/community/discord/invite)
[![Build](https://github.com/zmkfirmware/zmk/workflows/Build/badge.svg)](https://github.com/zmkfirmware/zmk/actions)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-v2.0%20adopted-ff69b4.svg)](CODE_OF_CONDUCT.md)

[ZMK Firmware](https://zmk.dev/) is an open source ([MIT](LICENSE)) keyboard firmware built on the [Zephyr™ Project](https://www.zephyrproject.org/) Real Time Operating System (RTOS). ZMK's goal is to provide a modern, wireless, and powerful firmware free of licensing issues.

Check out the website to learn more: https://zmk.dev/.

You can also come join our [ZMK Discord Server](https://zmk.dev/community/discord/invite).

To review features, check out the [feature overview](https://zmk.dev/docs/). ZMK is under active development, and new features are listed with the [enhancement label](https://github.com/zmkfirmware/zmk/issues?q=is%3Aissue+is%3Aopen+label%3Aenhancement) in GitHub. Please feel free to add 👍 to the issue description of any requests to upvote the feature.

---

# Build ZMK for Polarity Works BT60 (WSL2 + devcontainer)

Board IDs: BT60 V2 = `bt60//zmk`, BT60 V1 hotswap = `bt60_hs//zmk`.

## Prerequisites (Windows)

- WSL2 + Ubuntu, VS Code + Dev Containers extension, Docker Desktop running
- Docker Desktop → Settings → Resources → WSL integration → enable Ubuntu → Apply & restart
- Check in Ubuntu: `docker version` shows a `Server:` section

## Setup

Ubuntu (WSL) terminal:

```bash
cd ~
git clone git@github.com:vuckale/zmk.git
cd zmk
git remote add upstream https://github.com/zmkfirmware/zmk.git
code .
```

VS Code: **Reopen in Container**. Then in the container terminal:

```bash
west init -l app/
west update
```

Then: Dev Containers: **Rebuild Container**.

## Build

```bash
cd /workspaces/zmk/app
west build -p -d build/bt60 -b bt60//zmk
```

Output: `app/build/bt60/zephyr/zmk.uf2`

Custom config: add `-- -DZMK_CONFIG=/workspaces/zmk-config/config` (needs a `zmk-config` Docker volume).

## Flash

1. Open `\\wsl.localhost\Ubuntu\home\<you>\zmk\app\build\bt60\zephyr\` in Explorer
2. Enter bootloader (double-tap reset on the underside)
3. Copy `zmk.uf2` to the USB drive that appears

## Sync with upstream ZMK

Ubuntu (WSL) terminal, in `~/zmk`:

```bash
git.exe fetch upstream
git.exe checkout main
git.exe rebase upstream/main
git.exe push --force-with-lease origin main
```

Then in the container terminal:

```bash
west update
```

If the rebase stops on a conflict in `README.md`, keep yours:

```bash
git.exe checkout --theirs README.md
git.exe add README.md
git.exe rebase --continue
```

## Troubleshooting

- `dockerDesktopLinuxEngine` error: Docker Desktop not running yet
- `/var/run/docker.sock` missing in Ubuntu, or `distro-services/ubuntu.sock` error: enable WSL integration, run `wsl --shutdown`, reopen Ubuntu
- Slow builds: clone in WSL, not under `/mnt/c`
- `Permission denied` on `.git` files: `sudo chown -R $USER:$USER ~/zmk`
