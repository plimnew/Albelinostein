# Using Adelino

Adelino is an AI agent that works on your files and asks before it changes
anything. What it did, and what you decided, is kept in a signed record you
can check at any time.

This guide is organised by what you want to do. A reference of every
command is at the end. Portuguese: [guia.pt.md](guia.pt.md).

> Adelino 0.1.0 was published on 26 November 2026, on the downloads page of
> the site (<https://albelino.plimswitzzer.com/en/downloads/>). This guide
> describes the program as it will behave; installation instructions will be
> added here with the first release. See the [state of each version](../README.en.md#state-of-the-versions).

- [Start](#start)
- [How much may it do on its own?](#how-much-may-it-do-on-its-own)
- [Answering when it asks](#answering-when-it-asks)
- [The record: verify, replay, undo, export](#the-record-verify-replay-undo-export)
- [Memory and the project map](#memory-and-the-project-map)
- [Skills: notes from a book](#skills-notes-from-a-book)
- [More tools: servers and packs](#more-tools-servers-and-packs)
- [Scheduled requests](#scheduled-requests)
- [Phones and the signing key](#phones-and-the-signing-key)
- [When something is wrong](#when-something-is-wrong)
- [Command reference](#command-reference)

## Start

```bash
adelino init
```

Connects a model. If an Anthropic key is in your environment, or a model
server is running on this computer, it asks nothing. Otherwise it asks one
thing: which model to use.

```bash
adelino demo
```

A two-minute tour with a scripted assistant. No model needed. You allow one
action and refuse another, then see the verified record.

```bash
adelino chat
```

Starts a conversation in the current folder. That folder is the
*workspace*: Adelino may read inside it without asking. Add `--workspace`
to choose another, `--mode` to choose how careful it is.

```bash
adelino run "summarise the README"
```

One request, then exit. `adelino resume <session>` continues a session
that was waiting or was interrupted; `adelino sessions` lists them with
what each has used (model calls, tokens, cost where known, time).

## How much may it do on its own?

Three modes, per session:

| Mode | Reads in the workspace | Everything else |
|---|---|---|
| `ask` (default) | runs | asks you |
| `read_only` | runs | refused, without asking |
| `autonomous` | runs | runs **only** if a grant covers it; otherwise asks |

```bash
adelino mode <session> autonomous
```

A *grant* says what may run without asking in `autonomous` mode. Grants
are narrow, can expire, can have a use limit, and can be revoked.

```bash
adelino grant path ./src --write --minutes 60
```

```bash
adelino grant command make --uses 20
```

```bash
adelino grant command git --args status
```

The last one allows `git status …` but not `git push`. The match is on
whole arguments from the start.

```bash
adelino grant domain api.example.com
```

```bash
adelino grant list
```

```bash
adelino grant revoke <id>
```

Three things ask in every mode, whatever you granted:

- anything that cannot be undone;
- network requests and commands, once the session has read content from
  outside (a web page, a file outside the workspace, a tool server);
- any command, when this computer cannot confine it (see `adelino doctor`).

## Answering when it asks

In the terminal, Adelino shows what will run, the risk, why it asks and how
the command is confined, then waits for `y` or `n`. In the application the
same appears as a sheet with **Allow** and **Refuse**.

If nobody is there to answer, the action stays waiting:

```bash
adelino approvals
```

```bash
adelino approve <request>
```

```bash
adelino deny <request>
```

```bash
adelino resume <session>
```

`<request>` is the first characters of the request shown in the list.
A waiting action survives a restart, and a crash.

Your answer is signed. A refusal is recorded as firmly as an approval.

## The record: verify, replay, undo, export

```bash
adelino verify
```

Checks every event: that it has not been altered, that it follows the one
before, that it was signed by this installation, and that each decision
was signed by a device allowed to decide. If anything was changed it stops
at the first bad event and names it.

```bash
adelino replay <session>
```

Prints the session exactly as it happened, rebuilt from the record alone.

```bash
adelino undo <call>
```

Puts back a file Adelino wrote. The call id is shown next to each action.
It refuses if you changed the file afterwards, unless you add `--force`.
Only file writes can be undone; commands and network requests cannot,
which is why they are treated as riskier.

```bash
adelino export <session> -o session.json
```

```bash
adelino verify --file session.json
```

One file anyone can check on any machine with Adelino installed, with no
access to your data. It holds the whole chain of events (hashes and
metadata) and the content of that one session.

```bash
adelino redact <hash>
```

Deletes one stored piece of content (a message, a file's old version). The
record still verifies; the content is gone for good.

```bash
adelino log -n 50
```

The raw events.

## Memory and the project map

Adelino can remember facts between sessions. Each fact points at the event
that created it.

```bash
adelino memory list
```

```bash
adelino memory add "Invoices are filed by month under /finance"
```

```bash
adelino memory search invoices
```

The *project map* lets Adelino ask how the files of a project connect
instead of reading them one by one. It is built on this computer by
[Graphify](https://github.com/Graphify-Labs/graphify), offline, and stored
outside your project.

```bash
adelino graph install
```

```bash
adelino graph index
```

```bash
adelino graph query "what calls the payment module?"
```

## Skills: notes from a book

A *skill* is a set of notes Adelino writes from a document you own, such as
a book, an article or a manual, so it can look things up in it later. It
reads PDF, EPUB, Word (docx), HTML, Markdown and text files itself; there is
nothing to install. Adelino never searches for or downloads books.

```bash
adelino skill add ddia.pdf --name ddia --mode study --focus "frameworks, trade-offs, architecture patterns and anti-patterns"
```

Before anything is sent it shows the title, the number of chapters, any
warning from the reader, how many model calls and tokens it will take and
the estimated cost (or that no price is on record). It says that the
document goes to the model chapter by chapter, then asks `start? [y/N]`;
`--yes` skips the question. `--mode study` explains each idea and ends each
chapter with questions; `--mode reference` (the default) is terse, for
looking things up. `--kind prose` suits text without tables or code;
`--kind technical` (the default) keeps tables, formulas and code as written.

```bash
adelino skill list
```

```bash
adelino skill show ddia 3
```

Without a chapter number, `show` prints the index. `adelino skill off ddia`
keeps a skill but stops Adelino consulting it, `on` turns it back on,
`remove` deletes it, and `export ddia -o ddia.zip` writes it as one file.

What to know:

- A skill is reference material. It grants no permission and cannot skip an
  approval.
- Its text comes from a document, so it is untrusted. Adelino reads it only
  through a tool that marks it as data, and after it does, the session is
  untrusted: from then on every command and every network request is
  asked about, whatever grant covers it (the same rule as web pages and
  files outside the workspace).
- Only what you typed reaches Adelino's standing instructions: the skill's
  name, the file name and the focus. The notes themselves never do.
- Each model call is recorded in a session of its own with its cost, and
  stops at a spending limit like any other call. Notes already written are
  kept, so trying again after a stop or a failure does not pay twice.
- Adding, removing and switching a skill are in the signed record; adding
  also keeps the SHA-256 of the source document.
- Skills are stored in the Adelino data folder, in `skills/<name>/`, as
  `SKILL.md`, `chapters/` and `skill.json` (the open Agent Skills layout),
  so an exported skill works with other agents.
- PDF text is read without layout, so tables, formulas and code may lose
  their shape; the reader says so each time. A PDF is split into chapters
  only where a line such as "Chapter 3" starts one; otherwise it is cut
  into parts by size. Scanned PDFs with no text are refused. The notes are written by the model and can be wrong: they say
  what it understood each chapter to say.
- The application has a Skills page. Choose "Add document", pick the file
  with the system's file chooser, and give the skill a name. Adelino then
  shows what it expects to cost before it sends anything; confirm to start,
  and stop it at any time. A spending limit stops it like any other work.
  The page lists your skills and lets you read their notes, switch them on
  and off, remove them, and export one as a `.zip` file. It is not
  available on a paired phone.

## More tools: servers and packs

Adelino can use tools offered by other programs through the Model Context
Protocol (MCP). A server is either a program started on this computer or
an address reached over HTTP.

```bash
adelino mcp add notes -- npx -y some-mcp-server ~/notes
```

```bash
adelino mcp add search --url https://mcp.example.com/mcp
```

```bash
adelino mcp list
```

Every tool from a server asks **every time** until you give it a class:

```bash
adelino mcp trust notes read_file read
```

A tool classed `read` can then be covered by a grant
(`adelino grant tool mcp__notes__read_file`); without a grant it still
asks. If a server later changes a tool's description or inputs, the tool
goes back to asking every time and the next question tells you it changed.
What a tool returns is treated as outside content.

```bash
adelino mcp remove notes
```

A *pack* is one file listing servers and the classes its author proposes.

```bash
adelino pack install notes.json
```

It shows every program it would run and every class it proposes, and asks
first. Installing grants nothing.

## Scheduled requests

```bash
adelino schedule add "Summarise what changed in this folder" --every 6h
```

```bash
adelino schedule list
```

```bash
adelino schedule remove <id>
```

A scheduled request runs while `adelino serve` or the application is open.
It always runs in `ask` mode: it stops at the first thing that needs your
approval and waits. It does not run again while one is waiting for you.

## Requests, plans and steps

Each thing you ask is recorded as one request, from your message to the
end of the answer. For work with several steps Adelino lays out a plan
first and marks each step as it goes.

```bash
adelino plan
```

Shows every request of the latest session: whether it is running, waiting
for you, done, stopped at a spending limit or failed, its plan with the
state of each step, and how many actions it took. A plan authorises
nothing: every action in it is still checked and may still ask you.

## Verticals: working inside one field

A vertical is a file that fits Adelino to a field: what the model is told,
the shape of its answers, which tools it is offered, and calls that must
always be approved.

```bash
adelino vertical install robotics.json
```

Shows what the vertical does and asks before installing. A vertical grants
nothing and cannot skip an approval; it can only add reasons to ask.

```bash
adelino chat --vertical robotics
```

```bash
adelino vertical list
```

```bash
adelino vertical use robotics --session <id>
```

The record says which vertical and version each answer was given under. If
the installed file changes, a session using it stops before the model is
asked again, until you run `adelino vertical use` to accept the new
version. `--none` takes a session out of its vertical.

### The engineering vertical

Adelino has one vertical of its own, for robotics, drones, machine learning,
mathematics, physics and quantum computing. Once it is installed (see
`adelino vertical install`), use it by name:

```bash
adelino chat --vertical engineering
```

Inside it:

- Nothing is changed before the work is laid out as a plan.
- There are three levels of activity: **simulation**, **bench** (hardware
  powered, unable to move) and **field** (the vehicle moves or flies). A
  session starts in simulation.
- To go up, Adelino asks you each item of that level's checklist and then
  shows you all your answers at once. You approve or refuse exactly that.
  Bench lasts 8 hours, field 2 hours; then the session is back in
  simulation.
- Writing firmware to a controller is refused in simulation. Programs that
  can command hardware always ask.
- Python code runs in the project's own environment, in `.venv-adelino`
  inside the folder. Installing packages always asks, and shows which.

What it cannot do: read an answer and judge it. Units, sources, what it
declines to help with, and giving nothing meant for a higher level are
instructions to the model. A checklist records what you stated; it does
not make a flight safe or lawful. That stays with you.

## Checking a model: evaluations

Before trusting a model with real work, run it through a set of cases.

```bash
adelino eval <file>
```

Each case runs in an empty folder of its own, inside the vertical the file
names; approvals are answered automatically as each case says. The result
is a report, `eval-report.md`, and the same in `eval-report.json`.

A case ends as passed, failed, error, or **needs review**. Automatic checks
look at what was called, refused and written; they cannot tell whether an
answer is correct or safe. Most cases therefore end as "needs review": the
report gives the question, the full record and a place for a specialist to
mark the verdict. The command fails if any case failed, so it can stand in
front of a release.

Running against a paid model costs money. Set a limit first:
`adelino usage limit set --day --usd 2`.

## What it costs, and a limit on spending

Every call to a model is recorded with the tokens it used and what it cost.

```bash
adelino usage
```

Shows the total, then by session, by model and by day. A model that is not
in the price list (one running on your computer, for example) is shown as
having no price; its cost is not counted and nothing is guessed. Prices are
in `prices.json` in the data folder if you put one there, otherwise the
list that came with Adelino; the report says which version was used.

A limit stops Adelino before it asks the model again:

```bash
adelino usage limit set --day --usd 5
```

```bash
adelino usage limit set --session <id> --calls 40 --action warn
```

A limit is for one session, for one day across everything (days are counted
in UTC), or for each run of a scheduled request (`--schedule <id>`). It is
in dollars, tokens or model calls. `stop` is the default; `warn` says so
once and carries on. A stopped session continues with `adelino resume`
after you raise or remove the limit.

## Phones and the signing key

A paired phone can chat with Adelino on your computer and approve actions.
The phone creates its own signing key, so an approval it gives cannot be
forged by the computer.

```bash
adelino serve
```

(or keep the application open)

```bash
adelino pair --host 192.168.1.20
```

Shows a link, and a QR code in a terminal. It works once, for five minutes.
In the phone application, paste the link on the first screen. The
application does not scan the code with the camera yet; another QR reader
can read it and give you the link to paste.

Revoking a device (`adelino devices --revoke <key>`) also closes the
connection it has open.

```bash
adelino devices
```

```bash
adelino devices --revoke <key>
```

`adelino remote …` plays the part of a phone from another terminal.

Your installation has its own signing key. To replace it:

```bash
adelino key rotate
```

The change is recorded, signed by the old key. Earlier events stay
verifiable; the old key can no longer write or approve. Restart
`adelino serve` and the application afterwards.

```bash
adelino account status
```

There is no account today, and nothing is sent anywhere.

## When something is wrong

```bash
adelino doctor
```

Shows where Adelino keeps its files, its key, the size of the record,
whether commands can be confined on this computer, and whether the
project map is installed.

| You see | It means |
|---|---|
| `no Anthropic API key` | Run `adelino init`, or set `ANTHROPIC_API_KEY`. |
| `this session has actions waiting` | Answer with `adelino approvals`, then `adelino resume`. |
| `FAIL at seq N: …` | The record was altered at event N. Nothing after it can be trusted. |
| `written by another device key` | The key file does not match this record. Restore the right `device.key`. |
| `the signing key was rotated; restart Adelino` | A running `serve` or application still holds the old key. |
| `none` on the sandbox line of `adelino doctor` | Commands cannot be confined here. Adelino asks before each one. |
| `marked interrupted, not re-run` | Adelino stopped while a command ran. It will not run it again by itself. |

## Command reference

Every command accepts `--help`.

| Command | What it does |
|---|---|
| `adelino init` | First-time setup: connect a model |
| `adelino demo` | Guided tour, no model needed |
| `adelino chat` | Talk to the agent |
| `adelino run "<request>"` | One request, then exit |
| `adelino resume <session>` | Continue a waiting or interrupted session |
| `adelino sessions` | List sessions and what each has used |
| `adelino mode <session> <mode>` | Change a session's mode |
| `adelino approvals` | Actions waiting for a decision |
| `adelino approve <request>` | Allow a waiting action |
| `adelino deny <request>` | Refuse a waiting action |
| `adelino grant …` | What may run without asking (`path`, `command`, `domain`, `tool`, `list`, `revoke`) |
| `adelino verify` | Check the record; `--file` checks an exported file; `--witness` checks a head another device remembers |
| `adelino replay <session>` | The session as it happened |
| `adelino log` | Raw events |
| `adelino undo <call>` | Put back a file Adelino wrote |
| `adelino export <session> -o <file>` | A session as one verifiable file |
| `adelino redact <hash>` | Delete one stored piece of content |
| `adelino memory …` | Remembered facts (`list`, `add`, `search`) |
| `adelino eval <file>` | Run a set of evaluation cases against the model and write a report; `-o` for where |
| `adelino plan` | The requests of a session, each with its plan and how it ended; `--session <id>` |
| `adelino vertical …` | Fields packaged as data (`install`, `list`, `use`); start a session in one with `--vertical <name>` |
| `adelino usage` | What model calls used and cost, by session, model and day; `--session <id>` for one |
| `adelino usage limit …` | Spending limits (`set`, `list`, `remove`) for a session, a day or a scheduled request |
| `adelino graph …` | The project map (`install`, `index`, `query`, `explain`, `path`) |
| `adelino skill …` | Notes distilled from a book or document you own (`add`, `list`, `show`, `on`, `off`, `remove`, `export`) |
| `adelino mcp …` | Tool servers (`add`, `list`, `trust`, `remove`) |
| `adelino pack install <file>` | Install a pack of tool servers |
| `adelino schedule …` | Scheduled requests (`add`, `list`, `remove`) |
| `adelino serve` | Let paired phones connect |
| `adelino pair --host <address>` | Create a pairing link |
| `adelino devices` | Paired devices; `--revoke` removes one |
| `adelino remote …` | Act as a paired device |
| `adelino key rotate` | Replace the signing key |
| `adelino account status` | Whether an account is in use |
| `adelino doctor` | What is active on this computer |
