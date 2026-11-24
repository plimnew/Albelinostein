[Português](README.md) · [English](README.en.md)

# Albelinostein

**The AI agent that can prove what it did.**

**Albelinostein** is the desktop and mobile app, and the name of its mascot.
Underneath runs **Adelino**, the engine and command-line program (`adelino`).

Adelino is a local AI agent: it runs on your computer, reads and writes
files, runs commands and calls services, like other agents. The difference
is what surrounds every action, so that afterwards it is possible to prove
what happened and who allowed it.

<p>
  <img src="docs/img/albelinostein-3d-bicicleta.webp" alt="Albelinostein, the mascot, on a bicycle" width="300">
</p>

<p>
  <img src="docs/img/approval.png" alt="Albelinostein asking before it runs a command: what will run, the risk, why it asks, and how the command is confined" width="560">
</p>

This repository is public so that people can find the product: what it is,
the state of each compiled version, how to verify a download, the user guide
and the whitepaper. The source code is not public.

Site: <https://albelino.plimswitzzer.com>

## The five guarantees

1. **A deterministic policy decides** whether an action may run. Never a
   second model.
2. **A human signature approves it** when it matters, from the terminal or
   from a paired phone that holds its own key.
3. **A signed, hash-chained ledger records everything.** Alter one byte
   later and `adelino verify` names the exact event.
4. **An OS sandbox confines commands.** An approved command still cannot
   write outside your project or reach the network.
5. **Undo** restores any file Adelino wrote.

It also has memory between sessions, a map of the working folder, and
*skills* made from books and documents the person provides. See the
[whitepaper](docs/whitepaper.en.md).

## State of the versions

Version 0.1.0 is in preparation. **No version has been published.**

| System | State |
|---|---|
| Linux: `.deb`, `.rpm`, AppImage and command-line program | Validated by the acceptance suite; not yet published |
| macOS | Not yet validated |
| Windows | Not yet validated |
| Android | Not yet validated |
| iOS | Not yet validated |

No file is signed yet and the signing keys have not been generated.
Nothing can be downloaded from this repository today.

## Where downloads will appear

- In the **Releases** of this repository (GitHub), once a version is published.
- On the downloads page of the site: <https://albelino.plimswitzzer.com>

This table and the Releases list are the source of truth: if there is no
version listed here, it does not exist.

## How a download is verified

1. Each version ships a `SHA256SUMS` file with the SHA-256 sum of every file.
2. `SHA256SUMS` is signed with the GPG key "Albelino Releases"; its public
   half will be published in `docs/keys/` with the first release.
3. You check the signature of the list (`gpg --verify`), then the sum of
   the file (`sha256sum --check`).

Step by step: [docs/verificar.pt.md](docs/verificar.pt.md) (in Portuguese).

## Documentation

- [User guide](docs/guide.en.md) ([Português](docs/guia.pt.md))
- [Whitepaper](docs/whitepaper.en.md) ([Português](docs/whitepaper.pt.md))
- [FAQ](docs/faq.pt.md) (in Portuguese)
- [How to verify a download](docs/verificar.pt.md) (in Portuguese)
- [Security](SECURITY.md)
- [Changelog](CHANGELOG.md)

## Support

Only through the form: <https://albelino.plimswitzzer.com/suporte/>

## Licence

Binaries distributed by pLim. All rights reserved.
