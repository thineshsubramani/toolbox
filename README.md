# thinesh-toolbox

My Personal utility toolbox to streamline SRE / sysadmin tasks across Linux servers.
Wraps my custom bash scripts, golang/python/rust CLI tools, offline docs, and more into a single engine.
Everything controlled by label and INI files in my GitHub repo (`Thinesh-Subramani`) so I only pull what I need, when I need.

---
## Comments
I've just started and I'm still thinking of better way to design this as this will help reduce 90% of time performing cliche manual debugging lol

---

## Why I built this

* Tired of manually copying scripts to every new box
* multple tools scatered in repo so this helps to manage all the repos
* Need ephemeral debug containers *and* persistent offline images
* Add new tools by add label: 'toolbox' to repo and INI files 
* Work even if network is down (tarball / docker image / USB install)

---

## Features

* Pulls only from my repos (keeps it clean and secure)
* label: 'toolbox' and Tag‑based release fetch (v0.1-netdebug, v0.2-sheetcli, etc.)
* Lifecycle: install, update, remove via single binary
* Containerized option (Docker/Podman) or direct binary install
* Supports env/proxy injection for first‑time server setups
* Polyglot playground: go/python/rust interpreters for quick logic testing
* Offline docs and CLI helpers (ansible playbooks, sheetcli)

---

## How it works (Architecture) - Just my rough idea

```
                +--------------------------------------+
                |          github.com/Thinesh-Subramani|
                |                                      |
                |  [ repo ]                            |
                |   |                                   |
                |   +-- releases (tag: v0.1-netdebug)   |
                |   +-- releases (tag: v0.2-sheetcli)   |
                |   +-- ...                             |
                |                                      |
                |  [ ini/metadata ]                     |
                |   +-- netdebug.ini                    |
                |   +-- sheetcli.ini                    |
                +------------------|-------------------+
                                   |
                                   | fetch by tag & ini
                                   v
+------------------+    parses   +-----------------------+
| central engine   | ----------> | toolbox.ini / per‑tool|
| (golang binary)  |             | ini configs           |
| install, update, |             +-----------------------+
| remove, manage   |
+--------|---------+
         |
         | installs as binary, pulls docker image,
         | or extracts tarball (offline)
         v
+------------------------------------+
| target linux server                |
| - ephemeral container (docker run) |
| - persistent container (docker run -v) |
| - direct install /usr/local/bin    |
+------------------------------------+
```

### Flow

* Add new script/tool → tag release in repo → add matching `ini` file
* Engine scans repo tags, matches them to ini configs, and pulls only the tagged release
* Installs locally or in docker image, without cloning whole repo
* If network is down: use tarball, docker save/load, or USB stick

---

## Installation

```bash
# install via single binary
curl -sSL https://github.com/Thinesh-Subramani/toolbox/releases/latest/download/toolbox-installer.sh | bash

# or pull docker image
docker pull ghcr.io/thinesh-subramani/toolbox:latest

# or untar offline
tar -xvf toolbox.tar.gz
```

---

## use case

* New server behind corporate proxy: run installer, auto-injects proxy settings
* Need quick network debug: `toolbox netdebug`
* Need offline ansible docs: `toolbox docs ansible`

---

## Why I didn't go for other toolbox that already exist

* Doesn't download all tools; only what’s tagged and described in ini files
* Can run ephemeral or persistent
* Works fully offline
* Fully controlled from my own repos

---

## Roadmap

* Add signature verification
* Plugin system (drop new tool scripts, auto-registered by ini using CI/CD)
