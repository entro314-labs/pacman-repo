# entro314 pacman repository

Prebuilt Arch Linux packages for entro314 labs applications, served as a real
pacman repository. Install and upgrade with plain `pacman`: no AUR helper, no
Flatpak, no AppImage.

Served from **`https://repo.entro314.com`**, a domain we own, so the URL in
your `/etc/pacman.conf` survives a GitHub organisation rename.

Nothing here is edited by hand. Each application's release pipeline
(tauri-release-kit's `arch-repo.yml`) builds its package from the released
`.deb`, signs it, and pushes it here; the git history is the audit log.

## Use it

**1. Trust the signing key.** `[entro314]` is `SigLevel = Required`, so pacman
refuses everything here until the key is known:

```sh
curl -sLo /tmp/entro314.asc https://repo.entro314.com/entro314.asc
sudo pacman-key --add /tmp/entro314.asc
sudo pacman-key --lsign-key 1E86EEC08C6DBFFA8E543CE4571E7DA620B865A0
```

Check the fingerprint before trusting it:

```
1E86 EEC0 8C6D BFFA 8E54  3CE4 571E 7DA6 20B8 65A0
entro314 labs (package signing) <packages@entro314.com>
rsa4096, created 2026-11-05, expires 2031-11-04
```

**2. Add the repository to `/etc/pacman.conf`**, below the official and
distribution repositories (nothing here replaces a package from them):

```ini
[entro314]
SigLevel = Required DatabaseOptional
Server = https://repo.entro314.com/$arch
```

**3. Install.**

```sh
sudo pacman -Sy meltemi anasa
```

Updates arrive with `sudo pacman -Syu`. The applications detect that pacman
installed them and leave updating to it.

Do not substitute `SigLevel = Optional TrustAll` to get past a signature
error: that lets unsigned packages from this host run install scripts as root.

## What is in here

| Package | Application | Architectures |
|---|---|---|
| `meltemi` | [Meltemi](https://meltemi.email), email client | x86_64, aarch64 |
| `anasa` | [Anasa](https://anasa.md), Markdown thinking and writing app | x86_64, aarch64 |

Stable releases only; prereleases are not published here.

## Layout

```
x86_64/        packages, detached .sig files, entro314.db + .files
aarch64/       same, for arm64
entro314.asc   the package signing public key
CNAME          repo.entro314.com
```

The newest version of each package and the one before it are kept, for
rollback (`sudo pacman -U https://repo.entro314.com/x86_64/<older-file>.pkg.tar.zst`).
Older ones are pruned when a new version is published, because GitHub Pages
refuses to deploy a site over 1 GB.
