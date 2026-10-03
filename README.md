# CLI Pulse

CLI Pulse is a developer app for monitoring usage, quotas, resets, sessions,
and alerts across 50+ AI coding providers — and for remotely watching and
steering your Mac's AI-CLI agents from your phone. Available on macOS, iOS,
watchOS, and Android (beta).

This repository is the **public distribution and trust-documentation** repo
for CLI Pulse. It exists so users can:

- download official builds
- read the privacy, security, and terms documents
- find support contacts

It is **not** the product source code. CLI Pulse is closed-source commercial
software. See [License / All Rights Reserved](#license--all-rights-reserved)
below.

---

## What is CLI Pulse

CLI Pulse helps developers see — at a glance — how much of their AI provider
quotas they've used, what each provider is costing, when limits reset, and
which sessions are currently active. It supports 50+ providers, including
Claude, Codex, Gemini, OpenRouter, Cursor, Copilot, JetBrains AI, Ollama,
Warp, and Augment.

It also turns your phone into a remote control for your Mac's coding agents:
pair the macOS app's local helper, then from iPhone or iPad watch your
managed AI-CLI sessions live, send prompts, approve or deny permission
requests, and keep an eye on a whole swarm of agents — without touching your
Mac. Remote control is built into the direct-download Mac build but is not
switched on in CLI Pulse 1.55. When it is, the phone and the Mac connect to
each other directly over your local or private network, not through CLI
Pulse servers.

The product spans:

- a macOS menu-bar app with a local helper
- an iOS / iPadOS / watchOS app
- an Android app (beta)

---

## Download

| Platform | Where |
|----------|-------|
| iOS / iPadOS / watchOS | [App Store](https://apps.apple.com/app/cli-pulse/id6761163709) |
| macOS | [Signed & notarized DMG on GitHub Releases](https://github.com/JasonYeYuhe/cli-pulse/releases/latest) |
| Android (beta) | [Google Play internal testing](https://play.google.com/apps/internaltest/4699926885235347963) or [APK on GitHub Releases](https://github.com/JasonYeYuhe/cli-pulse/releases/latest) |

Binaries obtained from any source not listed above are **not** authentic and
may have been modified. Please report unofficial redistributions to
**yyyyy.yeyuhe@gmail.com**.

The published landing page lives at
<https://jasonyeyuhe.github.io/cli-pulse/>.

---

## Privacy summary

The privacy policy is at <https://cli-pulse.github.io/cli-pulse/privacy.html>
(its source is [`docs/privacy.html`](docs/privacy.html)). It lists everything
CLI Pulse reads, keeps and sends, and where this summary and the policy
disagree, the policy is right. The short version, for CLI Pulse 1.55:

- **Provider API keys and pasted session cookies never reach CLI Pulse
  servers.** They are kept in the macOS Keychain on the Mac where you enter
  them (EncryptedSharedPreferences in the Android beta) and sent only to the
  provider they belong to. Browser cookies, read only for a provider set to
  read them automatically (Cursor is, by default), are likewise sent only to
  that provider.
- **Tokens your AI CLIs keep on your Mac** (such as `~/.codex/auth.json`,
  `~/.claude/.credentials.json`, `~/.gemini/oauth_creds.json` and Claude
  Code's Keychain item) are read to ask each provider for your quota and are
  sent only to that provider. Renewing an expired one rewrites that CLI's own
  credential file.
- **Session-log contents stay on your Mac.** Logs such as those under
  `~/.codex/` and `~/.claude/` are parsed on the Mac; of what they give, only
  daily token counts and cost estimates sync, and only while you are signed
  in.
- **You are asked before the scan starts.** Using CLI Pulse without an account
  asks first: "Start local scan", "Last 30 days only" or "Not now". Until you
  answer, and after "Not now", the app and its background helper read none of
  your session logs or credential files and contact no AI provider
  ([exceptions](https://cli-pulse.github.io/cli-pulse/privacy.html#consent)).
  Signing in counts as a yes to the 30-day scan, but not over an earlier
  "Not now", and never as a yes to the one-time read of up to a year of older
  logs. Versions 1.50 to 1.54 did not honour "Not now" on a Mac synced to an
  account; 1.55 does.
- **What syncs while you are signed in is more than numbers.** Besides daily
  token counts, cost estimates and quota state, it includes the AI CLI
  sessions running on your Mac (the program's name and its project folder's
  name, never the full path), alerts, your Mac's name (which often contains
  your own name), its load and app versions, and a few diagnostics. The
  [data-by-data breakdown](https://cli-pulse.github.io/cli-pulse/privacy.html#data)
  lists every item.
- **The optional Companion CLI**, installed separately, is not sandboxed,
  keeps its own pairing with your account, and uploads more than the app,
  including up to 48 characters of each AI CLI's command line (which can
  include folder paths), machine readings such as battery and fan sensors,
  what sessions you start through it print (with secrets redacted), and, if
  Yield Score is on, commit metadata (commit hash, a keyed hash of the project
  path, the commit timestamp and a merge flag; never messages, diffs, file
  paths or author identity). Companion CLI 1.30.0 and earlier ignore the
  app's consent answer, sign-in and Privacy switches. See
  [its section](https://cli-pulse.github.io/cli-pulse/privacy.html#companion-cli).
- **On the Mac, only the App Store build runs in App Sandbox.** It reads
  files outside its container only through the folder access you grant. The
  direct-download build (GitHub, Homebrew), its background helper and
  built-in agent, and the Companion CLI read the files the policy names
  directly.
- **Crash reports** go to Sentry from the Mac, iPhone and Apple Watch apps
  (and the Android app), whether or not you are signed in, after an on-device
  scrubber removes strings shaped like keys and tokens, `/Users/<name>` paths
  and identifiers in web addresses. The same SDK reports whether each app
  session ended in a crash. There is no switch to turn crash reporting off.
- **Anonymous install statistics** (a random install id, the install channel,
  app and macOS versions, display language and a few yes/no milestones,
  linked to no account) go to our own database; turn them off in Settings →
  Privacy. No third-party analytics SDK ships with CLI Pulse.
- **Strict privacy mode** (Settings → Privacy) stops CLI Pulse reading, on
  its own, secrets other apps keep in your keychain or browsers, such as
  Claude Code's Keychain item and browser cookies, and turns off the anonymous
  install statistics. The sign-in files AI CLIs keep in your home folder are
  still read for quota, and crash reports are still sent.

Your controls (the scan question, background sync, Strict privacy mode, the
Companion CLI, deleting your account) are listed under
[Your controls](https://cli-pulse.github.io/cli-pulse/privacy.html#controls).

---

## Repository scope

This public repository is intentionally limited to:

- this `README.md`
- [LICENSE.md](LICENSE.md) — All Rights Reserved license
- [PRIVACY.md](PRIVACY.md) — points to the privacy policy, which is
  `docs/privacy.html`
- [TERMS.md](TERMS.md) — terms of use
- [SECURITY.md](SECURITY.md) — security policy and disclosure contact
- `docs/` — the static GitHub Pages site (landing, privacy, terms, security,
  data-handling, support, release notes)
- GitHub Releases — signed/notarized DMGs and APKs and their release notes

### What this repository is *not*

- It is **not** the product source code. The CLI Pulse macOS / iOS / watchOS
  / Android apps, their helper component, the Supabase backend, provider
  integrations, scanners, fixtures, internal planning documents, RPC
  schemas, and migrations are **not** published here.
- It is **not** a reproducible-build repository. You cannot rebuild a
  shipping CLI Pulse binary from anything in this repo.
- It is **not** a provider-integration reference. Nothing here documents how
  CLI Pulse talks to provider APIs, parses session logs, manages quotas,
  reads cookies, or accesses the Keychain.
- It is **not** permission to copy or reuse CLI Pulse code, assets, product
  designs, or documentation. See [LICENSE.md](LICENSE.md).

If you are looking for the product itself, install it from one of the
[official distribution channels](#download).

---

## License / All Rights Reserved

Copyright © Jason Ye. All rights reserved.

The contents of this repository are provided for product distribution, legal
notices, release notes, support, and transparency documentation only. No
license is granted to copy, modify, redistribute, reverse engineer, or
create derivative works from CLI Pulse application code, assets, product
designs, helper behavior, provider integration logic, or documentation
except where explicitly permitted in writing.

Full terms: [LICENSE.md](LICENSE.md).

For licensing inquiries, partnership requests, or takedown notices, email
**yyyyy.yeyuhe@gmail.com**.

---

## Support

- **Email:** [clipulse.support@gmail.com](mailto:clipulse.support@gmail.com)
- **Issues:** [github.com/JasonYeYuhe/cli-pulse/issues](https://github.com/JasonYeYuhe/cli-pulse/issues)
- **Security disclosures:** see [SECURITY.md](SECURITY.md)
- **Response time:** within 48 hours on business days
