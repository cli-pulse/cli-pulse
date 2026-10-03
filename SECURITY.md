# Security Policy

CLI Pulse is a developer tool that touches local credentials and AI provider
APIs, so security and privacy are explicit product goals. This document
explains how to report vulnerabilities and summarizes how user data is
handled.

The full privacy policy is at <https://cli-pulse.github.io/cli-pulse/privacy.html>.
It is the authority on what CLI Pulse reads, keeps and sends; this file
focuses on the security and disclosure aspects, and where the two disagree,
the policy is right.

---

## Reporting vulnerabilities

If you believe you've found a security or privacy issue in CLI Pulse,
please report it privately first.

- **Email:** **yyyyy.yeyuhe@gmail.com**
- **Subject prefix:** `[CLI Pulse Security]`
- **Preferred details:**
  - the affected platform (macOS / iOS / watchOS / Android / backend / web)
  - the affected app version (visible in Settings → About)
  - reproduction steps and impact
  - any logs, stack traces, or screenshots that help

Please do **not** open a public GitHub issue for unfixed security
vulnerabilities. Public issues are appropriate for general bug reports and
feature requests.

We aim to:

- acknowledge new reports within 3 business days
- provide an initial assessment within 7 business days
- ship a fix or a documented mitigation within 30 days for high-severity
  issues, with timeline updates if more time is needed

This is a small, single-developer project, so response times depend on the
report's severity and complexity. Reports that include a clear repro and
impact assessment get triaged fastest.

---

## Responsible disclosure

We support coordinated disclosure:

- please give us a reasonable window (typically 30 days, longer for
  complex issues) to ship a fix before publishing
- if the issue is being actively exploited, tell us in the first email so
  we can prioritize
- we will credit reporters in the release notes when a fix ships, unless
  the reporter prefers anonymity

---

## Data-handling summary

The policy's
[data-by-data breakdown](https://cli-pulse.github.io/cli-pulse/privacy.html#data)
lists every item. What matters most for security, as of CLI Pulse 1.55:

- **Provider API keys and pasted session cookies are not uploaded to CLI
  Pulse servers.** They are kept in the macOS Keychain on the Mac where you
  enter them (EncryptedSharedPreferences in the Android beta) and sent only
  to the provider they belong to, over HTTPS.
- **Tokens AI CLIs keep on the Mac** (`~/.codex/auth.json`,
  `~/.claude/.credentials.json`, `~/.gemini/oauth_creds.json`,
  `~/.local/share/kilo/auth.json` and Claude Code's Keychain item) are read
  to ask each provider for your quota and are **not** uploaded to CLI Pulse
  servers. The app copies the file-based ones into a Keychain item that only
  CLI Pulse's own programs can read. Renewing an expired token rewrites that
  CLI's own credential file.
- **Browser cookies** are sent only to the provider they belong to, never to
  CLI Pulse servers. The app reads them only for a provider whose cookie
  source is "Automatic" (Cursor's is by default), and not in Strict privacy
  mode. The Companion CLI has a separate fallback: when Claude's token does
  not work, it reads your claude.ai cookie from the Claude desktop app or a
  Chromium-based browser and keeps it in a plain-text file only your user
  account can read; from 1.31.0 it does not do this in Strict privacy
  mode, and 1.30.0 and earlier ignore that switch.
- **Session-log contents** (`~/.codex/sessions/`, `~/.claude/projects/` and
  the other paths the policy names) are parsed on the Mac and never
  uploaded. Of what they give, only daily token counts and cost estimates
  sync, and only while you are signed in.
- **What does sync while you are signed in is more than metrics:** quota
  state, the AI CLI sessions running on the Mac (the program's name and its
  project folder's name, never the full path, with a keyed hash of the
  path), alerts, an alert webhook address if you add one, the Mac's name,
  load and versions, and some diagnostics.
- **The optional Companion CLI**, installed separately, uploads more than the
  app, including up to 48 characters of each AI CLI's command line (which can
  include folder paths) and what sessions started through it print (with
  secrets redacted). See
  [its section](https://cli-pulse.github.io/cli-pulse/privacy.html#companion-cli).
- **Yield Score** is opt-in and collected only by the Companion CLI: the
  commit hash, a keyed hash of the project path, the commit timestamp and a
  merge flag. Commit messages, diffs, file paths and author identity are
  never uploaded.
- **Consent comes before the scan.** Without an account, CLI Pulse reads no
  session logs or credential files and contacts no AI provider until you
  answer "Start local scan" or "Last 30 days only", and not after "Not now",
  apart from the exceptions the policy lists. Signing in counts as a yes to
  the 30-day scan, but not over an earlier "Not now". Versions 1.50 to 1.54
  did not honour "Not now" on a Mac synced to an account; 1.55 does, in the
  app and its background helper, and so does Companion CLI 1.31.0. See
  [When the scanning starts](https://cli-pulse.github.io/cli-pulse/privacy.html#consent).

---

## Credential handling

- **Storage:** the macOS Keychain holds every secret the Mac app stores:
  provider API keys, pasted cookies, the CLI Pulse session token, the Mac's
  pairing secret, and the key behind the project-path hashes. On iPhone and
  Apple Watch the CLI Pulse session token is kept in the Keychain; on
  Android, AndroidX EncryptedSharedPreferences is used. The exception is the
  Companion CLI, which keeps its pairing secret, its hash key and the
  claude.ai cookie it reads from a browser or the Claude desktop app in files
  only your user account can read.
- **Transport to providers:** TLS 1.2+ direct from the user's device to
  each provider's official API endpoint. Provider credentials do not
  transit CLI Pulse infrastructure.
- **Transport to CLI Pulse Sync:** TLS 1.2+ to Supabase. The app authorizes
  with your CLI Pulse session token; the background helper and the
  Companion CLI each upload with their own pairing of the Mac to your
  account.
- **App Sandbox** (`com.apple.security.app-sandbox`) is enabled in the Mac
  App Store build, for the app and its background helper. There, file access
  outside the app container requires security-scoped bookmarks you grant in
  Settings → Advanced → CLI Tool Access. The direct-download build (GitHub,
  Homebrew), its background helper and built-in agent, and the Companion CLI
  are **not** sandboxed and read the files the policy names directly.

---

## Local components on the Mac

Besides the app itself, CLI Pulse for Mac can run up to three local
components. None of them uploads raw credentials, raw cookies or
session-log contents, and none of them sends crash reports.

- **The background helper** runs the app's collectors on its own schedule
  (every 2 minutes by default) and uploads what they find for your iPhone
  and Apple Watch. It is switched on when you pair the Mac with your account
  and off with Settings → Advanced → "Enable background sync". Since 1.55 it
  follows your answer to the scan question and uploads only while the app is
  signed in to the account the Mac was paired with. After an update from an
  earlier version this holds once the 1.55 app has restarted the helper and
  recorded whether you are signed in; until then it behaves as before. An
  upload already under way when you sign out is allowed to finish.
- **The built-in agent** (direct-download build only) runs the sessions you
  start from CLI Pulse and answers the app's questions about the Mac. It
  uploads nothing.
- **The Companion CLI** is optional and installed separately from Settings
  → Companion CLI. It is not sandboxed, has its own pairing and schedule, and
  uploads more than the app (see above). Versions 1.30.0 and earlier ignore
  the app's consent answer, sign-in and Privacy switches; 1.31.0 follows
  them. Remove it with Settings → Companion CLI → Uninstall….

---

## Remote sync

CLI Pulse Sync is account-based (Supabase Auth). Sync exists so that
iPhone, Apple Watch, and Android clients can show the same numbers as the
Mac without re-running the local scanner themselves.

- Server-side encryption at rest (AES-256) is applied by Supabase to the
  database and storage backing each account. There is no end-to-end
  encryption.
- TLS 1.2+ in transit.
- No third-party product-analytics SDK ships with CLI Pulse. **Sentry**
  receives crash reports from the Mac, iPhone and Apple Watch apps (and the
  Android app), whether or not you are signed in, and the same SDK reports
  whether each app session ended in a crash. A local `beforeSend` scrubber
  removes JWTs, strings shaped like `sk-…` API keys, Bearer headers,
  `/Users/<name>` paths, fields whose names contain common sensitive
  fragments, and the IP-address field before an event leaves the device.
  Since 1.55 web-request breadcrumbs carry no query strings, and parts of
  their addresses that look like identifiers, or follow words such as
  `workspace`, `organizations` or `users`, are replaced; this works by shape
  and place, so a part that looks like an ordinary word and follows no such
  word is kept.
  Performance tracing is disabled
  (`tracesSampleRate = 0`). There is no switch to turn crash reporting off.
- **Anonymous install statistics** (a random install id, the install
  channel, app and macOS versions, display language and a few yes/no
  milestones) go to our own database, linked to no account. They can be
  turned off, and Strict privacy mode turns them off too. See
  [Anonymous install statistics](https://cli-pulse.github.io/cli-pulse/privacy.html#install-statistics).
- **Remote control** between an iPhone and a Mac is built into the
  direct-download build but is not switched on in 1.55. When it is, the two
  devices connect directly over your local or private network, encrypted
  with TLS 1.2 using a key they agree on when you pair them, and what they
  exchange does not pass through CLI Pulse servers.

---

## User controls

The full list is under
[Your controls](https://cli-pulse.github.io/cli-pulse/privacy.html#controls).
The ones that matter most for security:

- **Scan consent:** Settings → Privacy: the scan switch when you use CLI
  Pulse without an account, or "Choose again…" while signed in; "Include
  older usage history" for logs older than 30 days.
- **Stop background uploads from a Mac:** sign out, or turn off Settings →
  Advanced → "Enable background sync". The Companion CLI is separate:
  Settings → Companion CLI → Uninstall….
- **Strict privacy mode:** Settings → Privacy. CLI Pulse then reads no
  secret that another app keeps in your keychain or browsers on its own
  (such as Claude Code's Keychain item or browser cookies). The sign-in files
  AI CLIs keep in your home folder are still read, for quota.
- **Folder access (Mac App Store build):** granted in Settings → Advanced →
  CLI Tool Access. There is no button to take back a single grant; turning
  the local scan off, or signing out, stops every read.
- **Delete API keys:** Settings → Providers → remove a provider; the
  Keychain entry is deleted.
- **Delete account:** "Delete Account", at the bottom of Settings on the Mac
  and in Settings on iPhone. Cascading deletes remove all associated rows
  within 30 days.

---

## Out of scope

The following are intentionally out of scope for this security policy:

- third-party AI providers' own APIs and their data handling — please
  refer to the provider's own policies
- third-party platform stores (Apple App Store, Google Play, Supabase)
  for their own infrastructure security

---

## Contact

- **Security disclosures:** **yyyyy.yeyuhe@gmail.com** with subject
  `[CLI Pulse Security]`
- **General support:** [clipulse.support@gmail.com](mailto:clipulse.support@gmail.com)
