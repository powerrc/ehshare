<div align="center">

<img src="https://ehshare.powerrc.net/assets/logo.png" alt="EHShare logo" width="96" height="96">

# EHShare

**Drag a file in. Get a link. Open it on your phone.**

*EHShare — Easy HTTP Sharing*

Turns the PC on your desk into an **HTTP and HTTP/3 file server** for the rest of your
network, so a two-gigabyte video comes off your phone in seconds — without an upload to
somebody else's computer first. The management screen stays inside the app window, and
**nothing is sent anywhere**.

[![Platform](https://img.shields.io/badge/platform-Windows-0078D4?logo=windows&logoColor=white)](https://ehshare.powerrc.net/)
[![License](https://img.shields.io/badge/license-freeware%20%28proprietary%29-8A8A8A)](https://ehshare.powerrc.net/)
[![Website](https://img.shields.io/badge/site-ehshare.powerrc.net-0d1821)](https://ehshare.powerrc.net/)
[![Languages](https://img.shields.io/badge/i18n-EN%20%7C%20简体中文%20%7C%20日本語%20%7C%20ES%20%7C%20한국어-brightgreen)](https://ehshare.powerrc.net/)

[**Website**](https://ehshare.powerrc.net/) · [**Releases**](https://github.com/powerrc/ehshare/releases) · [**Issues**](https://github.com/powerrc/ehshare/issues) · by [PowerRC](https://www.powerrc.net)

</div>

---

![The EHShare Files page](https://ehshare.powerrc.net/assets/screens/app-files.jpg)

> Nothing is uploaded anywhere · No account, no driver, no AirDrop · Free to use, on your own machine

## Contents

- [Why it exists](#why-it-exists)
- [How it works](#how-it-works)
- [Features](#features)
- [The parts that are easy to get wrong](#the-parts-that-are-easy-to-get-wrong)
- [Screens](#screens)
- [Install](#install)
- [Languages](#languages)
- [Configuration](#configuration)
- [FAQ](#faq)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Why it exists

> As a fan of [HFSv2](https://rejetto.com/hfs/) I have been using it for almost two
> decades. I am not a fan of v3, so I decided to build my own app. My primary use case
> is simply transferring large files — like videos — from my phone to my computer. I do
> not use an iPhone or a Mac, so AirDrop is not an option. FTP software works, it is
> just not as convenient as HFS.
>
> — PowerRC

That gap is narrow but real. Bluetooth is slow. A cloud drive wants the upload first,
which is the exact opposite of what you want with a four-gigabyte file. And the cable
tools are fine right up until you need them on somebody else's machine.

HFS solved it for years. EHShare keeps that idea intact — a folder you share, an address
you paste, a browser that opens it — and rebuilds the parts worth rebuilding.

## How it works

**Three steps, then it gets out of the way.**

### 1. Start it

EHShare listens on **`:880`** for HTTP and **`:881`** for HTTP/3, and binds to this
machine's own network address rather than to every interface. A first-run page asks
seven questions — language, whether uploads need a password, local-network speed, tray
behaviour, what to remember, where the share root is, and the two ports. All seven are
changeable later.

### 2. Add a file

Drag a file or a folder onto the window, or use the built-in browser, which handles
files and folders in one pass. It is shared straight away under your current defaults
and gets its own address. **Nothing is moved, copied or renamed on disk** — the list is a
view over the files you already have.

### 3. Open the link

Type the address into any browser on the same network. You get a sortable listing,
inline previews for images, video, audio, text, JSON and PDF, and — if you allow it —
upload and new-folder buttons on the page itself.

> Closing the window does not stop sharing. With **minimize to tray** on, the X button
> leaves EHShare serving from the notification area and only **Shut down** — from the
> sidebar or the tray menu — ends it. Both can also pause sharing, and a pause drops the
> transfers that are running rather than leaving them half-finished.

## Features

Every control below lives inside the desktop window. There is **no administration port**
and **no web UI to log into**, so the network only ever sees the shares you made.

| Area | What you get |
| --- | --- |
| **List & tree** | List and tree views over one flat store, clickable path breadcrumbs, back/forward, search, and columns for type, size, access, downloads, speed, expiry and live status. A share turns *expiring* five minutes before its deadline and *expired* after it. |
| **Per-share rules** | For any entry — file or folder — set who may reach it, how many times it can be downloaded, how fast in each direction, and when it disappears. Each dimension chooses separately between the global default, your own number, or unlimited. |
| **Bulk edit** | Select several entries and the same editor applies to all of them, recursively, leaving anything you did not touch alone. |
| **Remove vs Purge** | **Remove** takes an entry out of the share and keeps the file on disk. **Purge** deletes the bytes, in red, with a confirmation that says so. Expiring a share does neither. |
| **Accounts** | A username and password over HTTP Basic, with download and/or upload permission per account. Passwords are hashed with **scrypt**, and an unknown username costs the same time as a known one so the sign-in form cannot be used to enumerate accounts. |
| **Access tokens** | A token unlocks the shares you pick — one folder, or a list of files — and nothing else. Its own expiry, download count and speed ceiling; download-only, upload-only or both. The raw value is shown **once**; what is kept is a SHA-256 digest. |
| **IP blacklist** | Block an address or a whole CIDR block by hand, or let EHShare block for you: after five failed sign-ins inside a minute, that address stops being answered for an hour. Connections are dropped rather than refused, the list survives restarts, and every entry says whether it was manual or automatic and why. |
| **Uploads** | Anonymous upload is **off by default**. When on it applies to the share root too — the root being the one place with no share of its own to attach a limit or an expiry to — and it only opens the door to your own subnet (a /24, or a /48 on IPv6). Bodies stream straight to disk; each account has a quota in GiB. |
| **Statistics** | Live transfers and connections twice a second, traffic and transfer-count charts over today / 7 days / 30 days / a window you pick, and ranked lists of most downloaded, most uploaded, top users and top tokens. Every connection is tagged HTTP or HTTP/3 and clients are grouped by subnet. |
| **Settings** | **Easy** is the seven first-run questions and nothing else. **Expert** is eleven sections — general, network, security, blacklist, authentication, sharing, limits, storage, appearance, language, about. |

Statistics are all in memory: restarting EHShare resets it, and the page says so.

## The parts that are easy to get wrong

Four decisions that separate a share server *you run* from a share server that *owns
you*.

### 1. There is no administration port

Nothing on the network reconfigures the server. Settings exist only inside the desktop
window, so there is no second thing to lock down, no admin password to leak, and no way
to reach the controls from a phone that happens to be on your Wi-Fi.

### 2. Virtual paths never touch the filesystem

`/Documents/report.pdf` is resolved by walking the catalogue, not by joining strings.
Traversal attempts, NUL bytes and control characters are refused with a `400` rather
than quietly normalised away, and share clients never see a physical path at all.

### 3. One pipeline, two transports

Routing, authorisation, limits and counters live in one place, and HTTP/1.1 and HTTP/3
are adapters onto it. A share closed over TCP cannot be reached over QUIC by going round
the check. HTTP/3 needs TLS, so port 881 issues a self-signed certificate (or one of your
own PEM pair) and advertises itself with `Alt-Svc`; port 880 stays plain HTTP so you can
paste an `http://` address into a phone with no certificate warning.

### 4. The page you are handed is finished

Every served page is self-contained HTML with its CSS inline: no framework, no bundle,
no font from someone else's server. Sorting is a query string, filtering a form, layout a
link. **It works with JavaScript switched off.** Range requests are answered properly so
a paused download resumes and a video player can seek, a bare link to a file serves the
file rather than a page with a button, and every response carries `nosniff`,
`no-referrer` and `DENY`.

## Screens

Every image is the running app or a page it served.

| | |
| --- | --- |
| ![Tree view](https://ehshare.powerrc.net/assets/screens/app-tree.jpg) | ![One share](https://ehshare.powerrc.net/assets/screens/app-sharing.jpg) |
| **Tree view** — the same list, as a tree. | **One share** — who, how often, how fast, until when. |
| ![Seven at once](https://ehshare.powerrc.net/assets/screens/app-bulk-edit.jpg) | ![Scoped tokens](https://ehshare.powerrc.net/assets/screens/app-access-tokens.jpg) |
| **Seven at once** — nothing you did not touch is altered. | **Scoped tokens** — one door, not the whole server. |
| ![Shown once](https://ehshare.powerrc.net/assets/screens/app-token-created.jpg) | ![Live traffic](https://ehshare.powerrc.net/assets/screens/app-statistics.jpg) |
| **Shown once** — only the digest is kept. | **Live traffic** — twice a second, and forgotten on exit. |
| ![Who, and from where](https://ehshare.powerrc.net/assets/screens/app-statistics-live.jpg) | ![Blacklist](https://ehshare.powerrc.net/assets/screens/app-settings-blacklist.jpg) |
| **Who, and from where** — grouped by subnet. | **Blacklist** — your entries and the automatic ones. |
| ![What the phone sees](https://ehshare.powerrc.net/assets/screens/web-listing.jpg) | |
| **What the phone sees** — generated by the app itself. | |

## Install

**Requirements:** Windows. That is the whole list — no driver, no runtime, no account.

1. Go to [**Releases**](https://github.com/powerrc/ehshare/releases).
2. Download the per-user installer.
3. Run it, answer the seven first-run questions, and start sharing.

The backend is plain **Rust on Tokio**; only the Windows build is currently packaged and
tested.

### Ports

| Port | Protocol | Purpose |
| --- | --- | --- |
| `880` | HTTP/1.1 (plaintext) | The address you paste into a phone. No certificate warning. |
| `881` | HTTP/3 over TLS, plus HTTPS/1.1 with `Alt-Svc` | QUIC, and the self-upgrade path from `https://your-pc:881/`. |

Both are configurable. EHShare binds to its own network address rather than every
interface, so the shares are not exposed to interfaces you did not intend.

## Languages

The installer ships a language picker for:

| | | | | |
| --- | --- | --- | --- | --- |
| English | 简体中文 (Simplified Chinese) | 日本語 (Japanese) | Español (Spanish) | 한국어 (Korean) |

"Easy HTTP Sharing" is left in English in every language — it is the product's subtitle,
not a phrase to be localised.

## Configuration

The **Storage** page in Expert settings shows the path of a `config.yaml` you can open in
any editor while EHShare is closed.

**Partial files are fine.** It is an overlay applied *underneath* the in-app settings, so:

- a value you set in the UI is never undone by a stale file;
- a value you set in the file is picked up at the next start.

## FAQ

### Does anything leave my computer?

No. EHShare is a server, not a client: no sync, no backup, no telemetry, no cloud
account. The traffic counters live in memory and are gone when the app restarts. What is
written to disk is your settings, and — only if you turn on **Remember my files** — your
file list, accounts and tokens.

### Which platforms does it run on?

EHShare currently runs on **Windows only**. The published build is a per-user installer
with a language picker for English, Simplified Chinese, Japanese, Spanish and Korean. The
backend is plain Rust on Tokio, but only the Windows build is currently packaged and
tested.

### Will a browser on my phone actually use HTTP/3?

If it speaks QUIC, yes. Port 881 speaks HTTP/3 over TLS and, on the same port over TCP,
HTTPS/1.1 that answers with an `Alt-Svc` header naming the QUIC endpoint. So you visit
`https://your-pc:881/` once, accept the self-signed certificate, and the browser upgrades
itself from then on. Clients that cannot do QUIC keep working over plain HTTP on port
880, and the Statistics page tells you which of the two each connection used.

### Why is HTTP/3 on TLS but HTTP/1.1 not?

QUIC has no plaintext mode, so the HTTP/3 listener always needs a certificate. EHShare
generates a self-signed one for you — cached and reused across restarts — or you point
it at your own certificate and key, and it shows you the subject, the expiry date and the
SHA-256 fingerprint so you can import it into a trust store. Port 880 stays plain HTTP on
purpose: a phone should not have to click through a certificate warning before it will
show you a holiday video.

### What happens to my files when a share expires?

Nothing. Expiry closes the link; it does not touch the file. The entry stays in the list
marked *expired* until you give it a new deadline or turn it off, and **Refresh expiry**
restarts the countdown without disturbing any of its other rules. Deleting the bytes is a
separate action, called **Purge**, and it is the only one that cannot be undone.

### Is it really free?

Yes, free to use for as long as you like, on any machine you own. It is freeware under a
proprietary licence; the source code is not published. Made by PowerRC — write to
`admin#powerrc.net` if anything is wrong with it.

## Troubleshooting

**My browser does not upgrade to HTTP/3.**
Visit `https://your-pc:881/` once in that browser and accept the certificate. Without that
trust decision the browser stays on HTTP/1.1 over TCP. Some clients cannot do QUIC at
all — use port 880 for those.

**A certificate warning appears on port 881.**
Expected on first visit: the certificate is self-signed by default. Import the SHA-256
fingerprint shown in Expert → Security → TLS into your device's trust store, or supply
your own PEM certificate and key.

**An address I blocked is not being blocked.**
Automatic blocking needs five failed sign-ins inside a minute, then blocks for an hour.
Entries on the Blacklist page state whether they are manual or automatic, and how many
connections each has refused. Blacklisted connections are *dropped*, not refused — which
is why the client appears to hang rather than receive an error.

**Anonymous upload will not turn on.**
It only opens to your own subnet — a /24, or a /48 on IPv6 — and it covers the share root
as well as folders. Check the client is on the expected subnet.

**I stopped the app from serving.**
Closing the window is not the same as shutting down. With *minimize to tray* enabled, use
**Shut down** from the sidebar or the tray menu.

## License

EHShare is **freeware under a proprietary licence**. It is free to use, on any machine you
own, for as long as you like. The source code is not published; this repository tracks
releases and issues.

Made by **[PowerRC](https://www.powerrc.net)** — <admin@powerrc.net>

---

<div align="center">

**Get a two-gigabyte video off your phone in seconds.**
Free to use. Nothing leaves your machine.

[![Download](https://img.shields.io/badge/Download-EHShare-2E6F4E?style=for-the-badge)](https://github.com/powerrc/ehshare/releases)

</div>
