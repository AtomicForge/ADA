# ADA

A secure, native chat client for [Ollama](https://ollama.com). GTK4 and libadwaita, Wayland only, built as a Flatpak.

ADA keeps your chats and a persistent cross-chat memory in an **encrypted database** (SQLCipher). The passphrase is typed at every start and never stored. If the database file ever leaves your machine (backup, file sync, copy), it stays unreadable.

> **Status: experimental, not audited.** Please read [SECURITY.md](SECURITY.md) before you trust it with anything sensitive.

> **AI disclosure.** ADA was written with AI assistance (Anthropic's Claude) and tested by the maintainer, who is not a professional developer. The app icon was created with an AI image tool. Independent review is very welcome.

![ADA](data/icons/io.github.AtomicForge.Ada-256.png)

## Features

- Chat with your local Ollama models, streamed live, with a stop button
- Your messages in speech bubbles, a copy button under every answer, `Ctrl+S` shows or hides the sidebar
- **Answer length:** Compact, Medium or Detailed
- **Incognito chats:** not saved, no memory used, nothing added to the memory
- Chat list: new, rename, delete
- **Encrypted storage** for chats, titles, memory and settings
- **Memory** across chats: one text you can read and edit, used in every normal chat. ADA adds facts about you automatically from your own messages (can be switched off in Settings)
- Optional **web search** through your own SearXNG container
- "Find automatically" for Ollama and SearXNG on localhost
- Answers are shown with a small, safe Markdown subset (headings, bold, italic, code, lists); links, images and HTML are never interpreted
- Small codebase, one third-party dependency (`sqlcipher3`)
- English interface only

## Install

Download the `.flatpak` file from the [latest release](../../releases/latest), check the SHA-256 sum shown in the release notes, then:

```
sha256sum io.github.AtomicForge.Ada-*.flatpak
flatpak install --user io.github.AtomicForge.Ada-*.flatpak
flatpak run io.github.AtomicForge.Ada
```

You need a running Ollama server (default `http://localhost:11434`), for example in Podman. For web search you also need SearXNG with JSON output enabled: in SearXNG's `settings.yml`, add `json` under `search:` → `formats:`.

The Flatpak has only three permissions: Wayland, network (to reach Ollama and SearXNG) and GPU (`dri`). Your data lives in `~/.var/app/io.github.AtomicForge.Ada/data/ada/`.

## Build from source (Flatpak)

You need Flatpak with the GNOME 51 runtime and SDK, and `org.flatpak.Builder`:

```
flatpak install --user flathub org.gnome.Sdk//51 org.flatpak.Builder
flatpak run org.flatpak.Builder --user --install --force-clean build-dir io.github.AtomicForge.Ada.yaml
flatpak run io.github.AtomicForge.Ada
```

## Run from source and test

You need GTK 4, libadwaita 1 and PyGObject, plus the pinned database binding in a virtual environment:

```
python3 -m venv --system-site-packages .venv
.venv/bin/pip install --require-hashes -r requirements.txt
.venv/bin/python -m unittest discover -s tests
.venv/bin/python -m ada
```

The tests for networking, storage and rendering run without a display.

## Known limitations

- A forgotten passphrase cannot be recovered. This is by design.
- No passphrase change, no export, no automatic lock after inactivity.
- No file or image upload yet (planned).
- With web search on, your message is sent to your SearXNG, which forwards it to its search engines. ADA does not show the source URLs under the answer yet.
- Models can cut an answer when their context window is small. There is no context-size setting yet.
- Python cannot reliably wipe the passphrase or chat text from memory, so incognito chats are not protected against memory forensics.

## License

GPL-3.0-or-later, see [LICENSE](LICENSE).

ADA uses [sqlcipher3](https://github.com/coleifer/sqlcipher3) (zlib license) and [SQLCipher](https://www.zetetic.net/sqlcipher/) by Zetetic (BSD-style license). The Flatpak builds SQLCipher 4.20.0 from the official source and checks the generated file against a pinned checksum.
