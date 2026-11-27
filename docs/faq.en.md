# Frequently asked questions

Version 0.1.0 was published on 26 November 2026. The answers describe the
program as it was designed and tested; see the
[state of each version](../README.en.md).

## Does my data leave my computer?

Only if you choose so. Adelino runs on your computer and works with a local
model and without a network. If you connect a remote model, whatever you
send to the model goes to that model's provider. Network requests made by
the agent ask first, and always ask after the session has read content from
outside. There is no account today, and nothing is sent to pLim.

## Which models can I use?

Claude, through the Anthropic API, or any server compatible with the OpenAI
API running on your computer (for example Ollama, LM Studio, llama.cpp or
vLLM), so that nothing leaves the machine. One honest caveat: calls to a
real model have not yet been verified; they were tested against a stand-in
server, at the protocol level.

## How much does it cost?

No price is published in this repository and no version is for sale. What
can be said: each call to a remote model costs whatever the provider
charges, and Adelino records the tokens and the estimated cost of each call.
A local model has no counted cost. You can set a spending limit per session,
per day or per scheduled request, which stops Adelino before it asks the
model again.

## What is recorded?

Every message, every proposed action, the policy decision, every approval or
refusal (signed), and the result and cost of each call. The record is signed
and chained with checksums, so a later change is detected. The content
(messages, arguments, results) is stored separately and can be erased with
`adelino redact` without invalidating the record. Keys and secrets are not
written to the record.

## How do I know the record has not been altered?

With `adelino verify`: it checks each event, its link to the previous one,
the signature of whoever wrote it and that of whoever decided. If anything
was altered, it stops at the first wrong event and points to it. You can
also export a session to a file that anyone can verify on another machine,
without access to your data.

## Can I undo what Adelino did?

File writes, yes: `adelino undo` restores the previous version or deletes
the file it created. It refuses if the file was changed afterwards, unless
you force it. Commands and network requests cannot be undone, and so they
ask more often.

## Can Adelino delete or damage my files without my knowing?

By default it does not act on its own: in the default mode it only reads
inside the workspace and asks about everything else. Nothing irreversible
runs without a signed human decision. An approved command runs confined (on
Linux) to the workspace and without a network. But an approval is your
signature on what you were shown: reading what is about to run is still up
to you.

## What are skills?

Notes that Adelino writes, with a model, from a book, article or manual of
yours (PDF, EPUB, Word, HTML, Markdown or text), chapter by chapter, so that
it can consult them later. It shows the estimated cost before sending
anything and only goes ahead with your agreement. A skill grants no
permission, its text is treated as untrusted and the notes may be wrong.
Adelino never searches for or downloads books.

## Which systems are supported?

Linux (x86_64: `.deb`, `.rpm`, AppImage and command-line program), macOS for
Apple Silicon (`.dmg`, version 0.1.1, signed with Developer ID and notarised
by Apple; it does not run on Macs with an Intel processor) and Android (APK,
arm64) are published. Linux is validated by the acceptance suite. macOS,
Windows, Android and iOS are not yet validated. Windows (still without a
code-signing certificate) and iOS (still without a signed build recipe) are
not published. On Windows and
macOS there is no command sandbox yet: every command asks.

## Can I approve actions from my phone?

The protocol is built and tested: a paired phone has its own key and its
approval is a signature that the computer cannot forge. But the phone app
has not yet been run on a real device, and reading the QR code with the
camera is not yet built.

## Is the code open source?

The licence is Apache-2.0: Albelinostein can be used free of charge for any
purpose, including commercial use. The source code is not published yet.
This repository exists to publish the state of each version, the
guide, the technical document and the keys for verifying downloads, which
are on the downloads page of the site
(<https://albelino.plimswitzzer.com/en/downloads/>). The binaries are
distributed by pLim.

## How do I verify a download?

Each version has a checksum list (`SHA256SUMS`) signed with a GPG key, whose
public part is published in `docs/keys/albelino-releases.asc` and at
<https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
Step by step in [verify.en.md](verify.en.md). The Releases of this
repository contain no version: downloads are only on the downloads page of
the site.

## Where do I get support?

In the Discord community, <https://discord.gg/AyZHZPtkHk>, where the team
answers: questions in #help and bugs in #bug-reports. Security
vulnerabilities never on Discord: report them privately on GitHub, under
"Report a vulnerability"; see [SECURITY.md](../SECURITY.md).
