keepassxc
==============
[![Ansible Lint](https://github.com/oxivanisher/role-keepassxc/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/oxivanisher/role-keepassxc/actions/workflows/ansible-lint.yml)

This role installs Keepass XC.

Installation method
-------------------

`keepassxc_install_method` (default `auto`) picks where KeePassXC comes from:

| Value     | Source                                                                 |
|-----------|------------------------------------------------------------------------|
| `auto`    | The upstream PPA when it publishes the (upstream) codename, i.e. Ubuntu and derivatives like Linux Mint. Otherwise `keepassxc_fallback_method` (default `flatpak`). |
| `ppa`     | Always the PPA.                                                        |
| `flatpak` | Flathub (`org.keepassxc.KeePassXC`, system-wide). Current upstream release on amd64 and arm64, so this covers Debian trixie and Raspberry Pi OS. |
| `distro`  | The distribution package (`keepassxc_distro_package`, default `keepassxc-full`). |

The flatpak tasks need the `community.general` collection.

### SSH agent with the flatpak

The flatpak reaches the SSH agent through `/run/flatpak/ssh-auth`, which is a
proxy to whatever `SSH_AUTH_SOCK` the host had when KeePassXC was started. If
the desktop session runs more than one agent (for example the Xsession
`ssh-agent` in `/tmp/ssh-*` and the systemd user agent in
`$XDG_RUNTIME_DIR/openssh_agent`), KeePassXC and some terminals can end up
talking to different agents. Make sure the session uses a single agent.

Notes
-----

The GPG key is fetched from `keyserver.ubuntu.com` using `get_url` and then converted to a binary keyring with `gpg --dearmor`. This two-step approach is necessary because the Hockeypuck keyserver randomizes the order of ASCII armor headers (`Comment:` / `Version:`) between requests, causing raw checksum comparisons to always differ and marking the task as changed on every run. Converting to binary format strips these headers and produces deterministic output.

Once keyserver.ubuntu.com upgrades to Hockeypuck 2.4 (which adds a stable binary export API), this workaround can be replaced with a direct `get_url` download of the binary key.

Example Playbook
----------------
```yaml
- name: Install Keepass XC
  hosts: client
  collections:
    - oxivanisher.linux_desktop
  roles:
    - role: oxivanisher.linux_desktop.keepassxc
```

License
-------

BSD

Author Information
------------------

This role is part of the [oxivanisher.linux_desktop](https://galaxy.ansible.com/ui/repo/published/oxivanisher/linux_desktop/) collection, and the source for that is located on [github](https://github.com/oxivanisher/collection-linux_desktop).
