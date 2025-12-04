# Repository Guidelines

## Project Structure & Module Organization
- `build.sh` orchestrates live-build, writes `build.log`, and drops images into `images/`.
- `config/` holds live-build inputs: `package-lists/`, `includes.*` overlays, `hooks/`, `preseed/`, and `rootfs/` assets.
- `kali-config/` contains per-variant profiles (`variant-gnome`, `variant-xfce`, etc.) plus shared bits in `common/`.
- `auto/` has helper scripts invoked by live-build; `cache/`, `chroot/`, and `binary/` are throwaway build outputs.
- Use `local/` for small site-specific tweaks; keep large assets out of the repo.

## Build, Test, and Development Commands
```bash
./build.sh --help                    # list supported flags
./build.sh                           # build default variant for host arch
./build.sh --variant gnome --verbose # build specific desktop, log to console
./build.sh --clean                   # run lb clean --purge and remove build artefacts
./build.sh --get-image-path          # print expected artifact name without building
```
- Set a mirror with `echo "http://kali.download/kali" > .mirror` before running to avoid prompts.
- Keep `--no-clean` while debugging so `chroot/` and `binary/` stay available for inspection.

## Coding Style & Naming Conventions
- Shell scripts are Bash; follow `set -e`/`pipefail` patterns already in `build.sh`.
- Indent with two spaces, prefer `printf` over `echo -e`, and quote variables unless intentional globbing is needed.
- Keep filenames descriptive (`config/hooks/normal/99-foo.sh`) and align with live-build phase directories.

## Testing Guidelines
- There is no unit-test suite; validation = building an image. Run the variant you touched and capture `build.log`.
- Prefer `--verbose` or `--debug` when changing build steps; attach the resulting log snippet to reviews.
- For hook changes, rerun only the impacted variant to save time; confirm the artifact appears in `images/` as expected.

## Commit & Pull Request Guidelines
- Follow the existing history: short, imperative subjects (“Drop X”, “Add Y”), no trailing period.
- One logical change per commit; mention the variant or subsystem (`kali-config/variant-xfce`) when relevant.
- PRs should describe the motivation, commands run, and whether the image built successfully (include the `build.sh` invocation and a link to `build.log` or its tail).
- Add screenshots only when modifying boot menus or visible assets under `config/includes.*`.

## Security & Configuration Tips
- Run on a Debian-based host with `live-build`, `debootstrap`, and `squashfs-tools` installed; avoid building as root—`build.sh` calls `sudo` where needed.
- Keep credentials out of the tree; use `.mirror` and `config/archives/local.list` for custom mirrors or pins, but do not commit secrets.
