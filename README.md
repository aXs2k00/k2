# K2

K2 is a super-lightweight Kali live-build profile aimed at quickly spinning up command-and-control instances. It builds on the upstream Kali live-build configuration with a trimmed image footprint so you can launch C2 infrastructure fast. Supported variants are kept lean: Enlightenment (e17) and Openbox.

These are the same [build-scripts](https://gitlab.com/kalilinux/build-scripts) that the [Kali team](https://www.kali.org/) uses to generate the official Kali Linux base images, found here: [kali.org/get-kali/](https://www.kali.org/get-kali/).

## Quickstart builds

```console
$ echo "http://kali.download/kali" > .mirror
# Enlightenment (e17)
$ ./build.sh --variant e17 --verbose
# Openbox (lean, C2-focused)
$ ./build.sh --variant openbox --verbose
# Check expected ISO name without building
$ ./build.sh --variant openbox --get-image-path
```

Images land in `images/` after a successful build. Openbox lives in `kali-config/variant-openbox/`; build it with `--variant openbox`. Openbox uses `emptty` (CLI display manager) with autologin to the `kali` user; tweak `kali-config/variant-openbox/includes.chroot/etc/emptty/conf` if you want different behavior.

## C2 package hints

- Openbox variant bakes in C2 essentials in `kali-config/variant-openbox/package-lists/kali.list.chroot` (SSH, WireGuard, Python 3/pip, Git, tmux, socat, nmap, gcc/make, golang, curl). Trim or extend that list as needed.
- To mirror the same toolset in e17, append the same block to `kali-config/variant-e17/package-lists/kali.list.chroot`.
- Drop your WireGuard config at `kali-config/variant-<name>/includes.chroot/etc/wireguard/wg0.conf`; the hook enables `wg-quick@wg0` automatically when present.
- Emptty config for Openbox lives at `kali-config/variant-openbox/includes.chroot/etc/emptty/conf`; adjust `AUTOLOGIN`, `DEFAULT_USER`, or `AUTOLOGIN_SESSION` to change login flow or session.
- First-boot framework menu: a systemd unit (`k2-framework-setup.service`) prompts on TTY1 to optionally pull Sliver, Havoc, Merlin, Villain, or PoshC2. Selections are cloned/built into `/opt/<name>` and logged to `/var/log/k2-framework-setup.log`; rerun manually via `/usr/local/sbin/k2-framework-setup` after removing `/var/lib/k2-framework-setup-done`.

- - -

These images can be used to live boot into Kali, from such a USB/CD/DVD/sdCard, as well offers a basic installation. For more customization during setup, see [kali-installer](https://gitlab.com/kalilinux/build-scripts/kali-installer).

- [kali-installer](https://gitlab.com/kalilinux/build-scripts/kali-installer) uses [Simple-CDD](https://wiki.debian.org/Simple-CDD) _(which is a wrapper for [debian-cd](https://wiki.debian.org/debian-cd))_
- [kali-live](https://gitlab.com/kalilinux/build-scripts/kali-live) uses [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html)

- - -

Have a look at [Live Build a Custom Kali ISO](https://www.kali.org/docs/development/live-build-a-custom-kali-iso/) for explanations on how to use this repository.

There are also other [code examples of live-build](https://gitlab.com/kalilinux/recipes/live-build-config-examples), as well as [code examples for pre-seed to automate/unattended installation](https://gitlab.com/kalilinux/recipes/kali-preseed-examples).

- - -

## Help

```console
$ ./build.sh --help
Usage: ./build.sh [<option>...]

  --distribution <arg>
  --proposed-updates
  --arch <arg>
  --verbose
  --debug
  --variant <arg>
  --version <arg>
  --subdir <arg>
  --get-image-path
  --no-clean
  --clean
  --help

More information: https://www.kali.org/docs/development/live-build-a-custom-kali-iso/
$
```

## Usage Examples

Both images types, using the latest packages:

```console
$ ./build.sh
[...]
```

- - -

Manually define which Kali mirror to pull from, as well as be more detailed in output:

```console
$ echo "http://kali.download/kali" > .mirror
$
$ ./build.sh --verbose
[...]
```

- - -

Build a different Live image version (GNOME and KDE Plasma):

```console
$ ./build.sh \
  --debug \
  --variant e17
[...]
$ ./build.sh \
  --debug \
  --variant openbox
[...]
$
```
