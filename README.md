# pve-tailscale-integration

`pve-tailscale-integration` is a local Debian package that adds a Tailscale management panel to Proxmox VE node pages.

It packages a custom Proxmox API module, injects the required backend and UI hooks into `pve-manager`, and turns a host-local customization into a reusable `.deb` artifact.

## Features

- Adds a **Tailscale** tab to Proxmox VE node pages
- Shows local Tailscale state, hostname, IP, and peer list
- Allows changing common Tailscale settings from the Proxmox UI
- Installs a custom `PVE::API2::Nodes::Tailscale` backend module
- Applies required `pve-manager` patches automatically on install
- Restarts `pvedaemon` and `pveproxy` after installation
- Includes the fix for invalid empty `--exit-node=` handling

## Managed Settings

The UI currently exposes:

- enable or disable Tailscale
- accept routes
- accept DNS
- advertised routes
- advertised tags
- advertise exit node
- exit node
- exit-node LAN access
- SNAT subnet routes
- Tailscale SSH
- Shields Up
- hostname override
- operator user
- auth key

## Package Contents

The package installs:

- `usr/share/perl5/PVE/API2/Nodes/Tailscale.pm`
- `usr/lib/pve-tailscale-integration/apply-patches.sh`
- `usr/share/pve-tailscale-integration/nodes-import-snippet.txt`
- `usr/share/pve-tailscale-integration/nodes-register-snippet.txt`
- `usr/share/pve-tailscale-integration/js-menu-snippet.txt`
- `usr/share/pve-tailscale-integration/js-panel-snippet.js`

The package does not take ownership of core `pve-manager` files. Instead, it patches these files during installation:

- `/usr/share/perl5/PVE/API2/Nodes.pm`
- `/usr/share/pve-manager/js/pvemanagerlib.js`

This avoids direct package ownership conflicts with Proxmox packages while keeping the integration easy to reinstall after updates.

## Requirements

- Proxmox VE with `pve-manager >= 9.1.6`
- `tailscale`
- `python3`

## Build

```bash
dpkg-deb --build /root/pve-tailscale-integration /root/pve-tailscale-integration_0.1.0-1_all.deb
```

## Install

```bash
dpkg -i /root/pve-tailscale-integration_0.1.0-1_all.deb
```

After installation, refresh the Proxmox web UI.

## Update Behavior

Because the integration patches files owned by `pve-manager`, a future Proxmox update may overwrite those changes.

To reapply the integration after an update, either reinstall the package:

```bash
dpkg -i /root/pve-tailscale-integration_0.1.0-1_all.deb
```

or run the helper directly:

```bash
/usr/lib/pve-tailscale-integration/apply-patches.sh
systemctl restart pvedaemon pveproxy
```

## Validation

The current package has been verified with:

- `bash -n` on package shell scripts
- `perl -c` on `Tailscale.pm`
- `dpkg-deb --build`
- `dpkg-deb -c`

## Notes

- This is a local integration package, not an upstream Proxmox extension
- Patch application depends on expected text anchors in Proxmox source files
- If `pve-manager` changes those anchors in a future release, the patch helper may need adjustment

## License

No license has been defined yet.
