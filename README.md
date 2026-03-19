
    `pve-tailscale-integration` is a local Debian package that adds a custom Tailscale integration to Proxmox VE node
    pages.

    The package was created to make the integration reproducible and portable across hosts instead of relying on ad hoc
    manual edits in `/usr/share`.

    ## What This Package Does

    The package provides three pieces:

    1. A custom Proxmox API module:
       - `PVE::API2::Nodes::Tailscale`
       - installed as `/usr/share/perl5/PVE/API2/Nodes/Tailscale.pm`

    2. An idempotent patch helper:
       - `/usr/lib/pve-tailscale-integration/apply-patches.sh`

    3. Patch snippets used by the helper:
       - `/usr/share/pve-tailscale-integration/*`

    During installation, the package:

    … +117 lines
    - If a future Proxmox release changes those anchors significantly, the helper script may need adjustment.
    - The package is architecture-independent because it ships scripts and text assets only.

    ## Validation Performed

    The current package staging was validated with:

    - `bash -n` on shell scripts
    - `perl -c` on `Tailscale.pm`
    - `dpkg-deb --build`
    - `dpkg-deb -c` to verify package contents

    ## Suggested Future Improvements

    - Add a `postrm` or documented rollback flow
    - Add a version compatibility check against the installed `pve-manager`
    - Split the UI panel source into a dedicated managed JS file if Proxmox ever supports a cleaner extension mechanism
    - Turn the helper into a more explicit patch engine with better diff/error reporting
    - Add automated smoke tests for anchor matching in patched Proxmox files

    ## Summary

    This package turns a host-local Tailscale customization for Proxmox VE into a reusable deployment artifact. It is
    intentionally simple, transparent, and easy to rebuild, while avoiding direct package ownership conflicts with
    `pve-manager`.
