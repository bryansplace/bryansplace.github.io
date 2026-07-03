---
title: Fix french filenames
date: 2026-07-03

layout: post


---

# Fixing Mangled French Filenames on a Freebox Share Across a Multi-Kernel Home Network

## The problem

You mount a Freebox Delta SMB/CIFS share (`Disque_1`) on multiple Linux boxes. On one machine, accented filenames like `Téléchargements` and `Vidéos` show up fine. On another, they render as `T?l?chargements` or with escaped octal bytes (`T`$`\351`l`\351`chargements).

**Root cause:** CIFS relies on the kernel's NLS (National Language Support) layer to translate SMB's on-the-wire UTF-16 filenames into a local charset. If `iocharset=utf8` isn't set in the mount options — or worse, if the running kernel doesn't have the `nls_utf8` codec available at all (built-in or as a loadable module) — filenames fall back to a default charset (often `cp437`) and non-ASCII characters get mangled.

Some kernels, especially minimal/mobile builds (e.g. postmarketOS on phone SoCs), are compiled *without* `CONFIG_NLS_UTF8` at all. In that case, no fstab option can fix it — there's no codec to select.

---

## Step 1 — Diagnose

On the machine with mangled filenames:

```bash
uname -a
cat /etc/fstab | grep -i disque   # or whatever your CIFS mount line is
```

Check the mount options actually in effect:

```bash
mount | grep -i cifs
```

Look specifically for `iocharset=`. If it's missing, add it:

```
//192.168.1.254/Disque_1 /mnt/freebox cifs vers=3.0,guest,uid=1000,gid=1000,noserverino,_netdev,nofail,rw,cache=none,iocharset=utf8 0 0
```

```bash
sudo umount /mnt/freebox
sudo mount -a
ls /mnt/freebox/Téléchargements
```

If that fixes it — done. If the mount now fails outright, keep going.

---

## Step 2 — Check whether the kernel actually has UTF-8 NLS support

```bash
lsmod | grep nls
zcat /proc/config.gz 2>/dev/null | grep CONFIG_NLS_UTF8
find /lib/modules/$(uname -r) -name "nls_utf8*"
```

Three possible outcomes:

- `CONFIG_NLS_UTF8=y` → built in, should always work, investigate elsewhere.
- `CONFIG_NLS_UTF8=m` and the `.ko` file exists → just needs loading: `sudo modprobe nls_utf8`.
- `# CONFIG_NLS_UTF8 is not set` and no `.ko` file anywhere → **this kernel cannot do UTF-8 CIFS translation, full stop.** This is common on trimmed-down mobile/embedded kernels (e.g. postmarketOS on Snapdragon SoCs). Rebuilding the kernel config is the "correct" fix but often impractical. Skip to Step 4 (proxy approach).

### Gotcha: wrong kernel is running

If `modprobe nls_utf8` fails with "module not found," first double check `uname -r` actually matches the kernel you think you're running. On Debian/Ubuntu systems, an interrupted `apt` upgrade can leave you booted into a **newer kernel whose module directory was never fully populated**, while an older, fully-installed kernel sits unused. Symptoms:

```
modprobe: FATAL: Module nls_utf8 not found in directory /lib/modules/6.8.0-134-generic
```

Check for multiple kernels and pick the good one:

```bash
ls /lib/modules/
dpkg -l | grep linux-image | grep ^ii
```

Boot into the known-good kernel:

```bash
awk -F"'" '/menuentry / {print $2}' /boot/grub/grub.cfg | grep <good-kernel-version>
sudo grub-reboot "Advanced options for Ubuntu>Ubuntu, with Linux <good-kernel-version>"
sudo reboot
```

Once confirmed working, pin it permanently so future upgrades don't silently switch you back:

```bash
sudo sed -i "s/^GRUB_DEFAULT=.*/GRUB_DEFAULT=\"Advanced options for Ubuntu>Ubuntu, with Linux <good-kernel-version>\"/" /etc/default/grub
sudo update-grub
```

Then either fix or purge the broken kernel:

```bash
sudo apt remove --purge linux-image-<bad-version> linux-modules-<bad-version> linux-headers-<bad-version>*
sudo update-grub
```

**Also fix any dpkg interruption first**, if you hit it — it's the usual root cause of a half-installed kernel:

```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
sudo apt autoremove
sudo apt full-upgrade -y
```

If autoremove/purge chokes on a package "in a very bad inconsistent state," force it:

```bash
sudo dpkg --remove --force-remove-reinstreq <package-name>
# or, if that's not enough:
sudo dpkg --purge --force-all <package-name>
```

---

## Step 3 — Confirm the fix, make it permanent

Once `nls_utf8` loads and the mount works manually:

```bash
sudo mount -t cifs //<nas-ip>/<share> /mnt/freebox -o vers=3.0,guest,uid=1000,gid=1000,noserverino,rw,cache=none,iocharset=utf8
ls /mnt/freebox/Téléchargements
```

Put the same options in `/etc/fstab`, then test a clean remount:

```bash
sudo umount /mnt/freebox
sudo mount -a
ls /mnt/freebox/Téléchargements
```

If this machine works, you're done for it. If this is the kernel that genuinely lacks `CONFIG_NLS_UTF8` (Step 2's third case), continue to Step 4.

---

## Step 4 — When the kernel can't do UTF-8 CIFS at all: proxy through another machine

Some kernels (minimal mobile builds) simply don't ship `nls_utf8`, built-in or as a module, and rebuilding the kernel isn't practical. The fix is to **not mount CIFS directly on that machine** — instead, mount the CIFS share correctly on a different machine that *does* have full UTF-8 support, and re-share it.

### What doesn't work: re-exporting CIFS over kernel NFS

It's tempting to re-export the CIFS mount via `nfs-kernel-server`. **This does not work.** The Linux kernel NFS server requires the underlying filesystem to implement `export_operations` for stable file handles — CIFS doesn't support this:

```
exportfs: /mnt/Disque_1 does not support NFS export
```

Don't waste time here — skip straight to sshfs.

### What works: sshfs

1. **On the proxy machine** (the one with full UTF-8 CIFS support), mount the share correctly:

```bash
sudo mkdir -p /mnt/freebox
```

`/etc/fstab`:
```
//<nas-ip>/<share> /mnt/freebox cifs vers=3.0,guest,uid=1000,gid=1000,noserverino,_netdev,nofail,rw,cache=none,iocharset=utf8 0 0
```

```bash
sudo mount -a
ls /mnt/freebox/Téléchargements   # confirm correct accents here first
```

2. **Set up SSH key auth** from the client machine to the proxy (so the mount can be unattended/automatic):

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ""
ssh-copy-id user@proxy-host
# or manually if ssh-copy-id isn't available:
cat ~/.ssh/id_ed25519.pub | ssh user@proxy-host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
```

3. **On the client machine**, install sshfs and the fuse kernel module:

```bash
sudo apk add sshfs        # Alpine/postmarktOS
# or: sudo apt install sshfs   # Debian/Ubuntu

sudo modprobe fuse
```

Make fuse load automatically at boot (needed on Alpine/OpenRC systems — not persistent otherwise):
```bash
echo "fuse" | sudo tee -a /etc/modules
```

4. **Create the mountpoint and fix ownership** — FUSE requires the mounting user to own the mountpoint:

```bash
sudo mkdir -p /mnt/freebox
sudo chown $(whoami):$(whoami) /mnt/freebox
```

5. **Test manually first**, in the foreground so you see errors immediately:

```bash
sshfs user@proxy-host:/mnt/freebox /mnt/freebox -o ro,IdentityFile=/home/$(whoami)/.ssh/id_ed25519 -f
```

In a second terminal:
```bash
ls /mnt/freebox/Téléchargements
```

Ctrl+C the first terminal once confirmed working.

6. **Make it permanent via fstab:**

```
user@proxy-host:/mnt/freebox /mnt/freebox fuse.sshfs ro,_netdev,nofail,reconnect,ServerAliveInterval=15,IdentityFile=/home/user/.ssh/id_ed25519,allow_other,uid=1000,gid=1000 0 0
```

Enable `allow_other` support (required for the fstab-driven mount option above):
```bash
grep user_allow_other /etc/fuse.conf || echo "user_allow_other" | sudo tee -a /etc/fuse.conf
```

7. **Test with a clean remount, then reboot to confirm it survives a real boot cycle:**

```bash
sudo umount /mnt/freebox 2>/dev/null
sudo mount -a
ls /mnt/freebox/Téléchargements

sudo reboot
# after reboot:
mount | grep freebox
ls /mnt/freebox/Téléchargements
```

### If it doesn't come up automatically at boot

OpenRC's `_netdev` handling (and sometimes Tailscale's own startup timing — see below) can race against network readiness. Fall back to a ping-retry script rather than relying on fstab's `_netdev` alone, e.g. in `/etc/local.d/network-mounts.start`:

```bash
#!/bin/sh
# wait for proxy host to be reachable, then mount
for i in $(seq 1 30); do
    ping -c1 -W1 <proxy-host-ip-or-tailscale-name> >/dev/null 2>&1 && break
    sleep 2
done
mount /mnt/freebox
```

```bash
chmod +x /etc/local.d/network-mounts.start
sudo rc-update add local default
```

---

## Variant: client connects to the proxy over Tailscale instead of LAN

If the client machine isn't on the same local subnet as the proxy and instead reaches it over Tailscale, the steps are identical — just address the proxy by its **Tailscale hostname or 100.x.x.x IP** instead of the LAN IP, everywhere:

- SSH key copy: `ssh-copy-id user@<tailscale-hostname>`
- Manual sshfs test: `sshfs user@<tailscale-hostname>:/mnt/freebox /mnt/freebox ...`
- fstab line: `user@<tailscale-hostname>:/mnt/freebox /mnt/freebox fuse.sshfs ...`

Two Tailscale-specific things to watch for:

1. **Boot-time race condition is worse.** Tailscale itself needs to come up and authenticate before the hostname resolves or the tunnel is reachable — this is on top of normal network readiness, so `_netdev` alone is even less reliable. Strongly consider the ping-retry `local.d` script above, but make it wait for the Tailscale IP specifically:

```bash
#!/bin/sh
for i in $(seq 1 60); do
    tailscale status >/dev/null 2>&1 && ping -c1 -W1 <tailscale-ip> >/dev/null 2>&1 && break
    sleep 2
done
mount /mnt/freebox
```

You may also want the mount script to depend on the `tailscaled` service explicitly if your init system supports declaring service ordering (OpenRC: add `need tailscale` inside a proper init script rather than a raw `local.d` script, if you want it more robust).

2. **Use Tailscale MagicDNS names over raw 100.x IPs where possible** — more readable in fstab and survives Tailscale IP reassignment (unlikely but possible) without editing config.

Everything else — key auth, fuse module, `allow_other`, ownership of the mountpoint — is unchanged from the LAN case.

---

## Quick reference — decision tree

1. Mangled filenames on CIFS mount → check `iocharset=utf8` is in the mount options.
2. Still mangled or mount fails with "iocharset utf8 not found" → check kernel has `nls_utf8` (built-in, module, or missing entirely via `zcat /proc/config.gz | grep NLS_UTF8`).
3. Module missing but `find /lib/modules/$(uname -r) -name nls_utf8*` shows it should exist → check you're actually booted into the kernel you think you are; fix any interrupted `dpkg`/`apt` state; boot into a known-good kernel; pin it via `GRUB_DEFAULT`.
4. Kernel genuinely has no UTF-8 NLS support (common on minimal/mobile builds) → don't fight the kernel. Mount CIFS correctly on a proxy machine with full UTF-8 support, then re-share via **sshfs** (not kernel NFS — CIFS can't be re-exported that way).
5. Client and proxy on different networks → same sshfs approach, just address the proxy via Tailscale hostname/IP instead of LAN IP, and harden the boot-time mount against Tailscale's own startup delay.