# Backwater

Backwater is an unofficial desktop client for Slack, built with Rust, Tauri 2 and a TypeScript interface. It keeps one or more Slack workspaces signed in at once and runs on macOS, with a Windows build in progress. Slack Technologies neither makes nor endorses Backwater. This repository holds signed builds and the update feed. The source repository is private at present.

## Downloads

Each [release](https://github.com/firebendingsquirrel/backwater-releases/releases) attaches these files:

| File | Contents |
|---|---|
| `Backwater.app.tar.gz` | The macOS application for Apple silicon (arm64), macOS 14 or later |
| `Backwater.app.tar.gz.sig` | The updater signature for the macOS package |
| `latest.json` | The update feed that installed copies read |

Current releases contain no Windows installer. The Windows build compiles in continuous integration, has no test on Windows hardware, and joins the releases once it passes one.

## Installation on macOS

The macOS package extracts with Finder or with the command below, and the extracted `Backwater.app` belongs in `/Applications`.

```sh
tar -xzf Backwater.app.tar.gz && mv Backwater.app /Applications/
```

The build carries a self-signed certificate and no Apple notarization, so macOS blocks the first launch. The Open Anyway button in System Settings, Privacy & Security, allows it once. On first sign-in, macOS asks for Keychain access, and each later version asks once more, because the Keychain recognizes an application without an Apple team identifier only by its exact code.

Sign-in uses Slack's own sign-in page in a private window, or the default browser, where passkeys and an existing Slack session work. Slack handles the password, single sign-on and two-factor steps. No developer app or workspace approval is required.

## Updates

An installed copy checks `latest.json` 10 seconds after launch and from Settings, Advanced, Check for updates. A newer version installs only after confirmation, and the application then restarts in place, with no removal or reinstallation. A restart waits while a Huddle is active. Every package carries a minisign signature, and the application rejects any package that fails verification against the public key built into it. Updates keep the signing certificate, so macOS keeps microphone, camera and notification permissions across versions. The first in-app update, from 0.3.1 to 0.3.2, completed on 9 November 2026.

Versions before 0.3.1 have no updater and require one manual installation of a current release.

## Features

| Area | Implemented |
|---|---|
| Workspaces | Several signed-in workspaces, each with its own live connection, rail tile and unread badge |
| Conversations | Channels, direct messages and group direct messages, with starred conversations, a compact list option and Slack's read and unread styling |
| Messages | Slack markup, Block Kit sections, fields, context, images and rich text, legacy attachments, reactions, edits, deletion, and unsend with Command+Z |
| Composer | Drafts per conversation, mentions, emoji, a slash-command menu with the workspace's commands, file attachments by drop, picker or paste |
| Threads and Activity | Thread panel and replies, a Threads list, and an Activity list of mentions, replies and reactions |
| Notifications | Native macOS notifications for direct messages, mentions, keywords or all messages, with previews and sounds optional; a clicked notification opens its conversation |
| Huddles | Joining by button or `/huddle`, microphone and camera, screen sharing where the webview supports it, and a separate Huddle window |
| Settings | 16 categories; Slack account preferences synchronize with read-back confirmation, and device preferences stay local |
| Appearance | System, Light, Dark and OLED themes |

Opening a conversation from a notification and pasting files have offline test coverage only. Application buttons and menus inside messages render disabled, because Slack app interactions are not implemented. User profiles, Drafts and sent, Saved and Directories remain unimplemented.

## Performance

All figures below are idle measurements on one Mac (macOS 15.2, Mac16,13, 10 logical processors, 25,769,803,776 bytes of memory), with official Slack 4.52.155 and Backwater sampled at the same time every 2 seconds and the same busy public channel open in both. Physical footprint is the value Activity Monitor shows as Memory. Each total includes the WebKit or Electron helper processes that macOS attributes to the application.

Matched-uptime trial, 6 November 2026, with official Slack restarted 3 minutes 40 seconds before a 120-second sample:

| Measure | Slack 4.52.155 (Electron) | Backwater 0.2.0 (Tauri) | Reduction |
|---|---:|---:|---:|
| Physical footprint, median | 771,056,256 bytes | 121,916,800 bytes | 84.2% |
| Summed resident memory, median | 1,106,411,520 bytes | 187,809,792 bytes | 83.0% |
| Processor, mean of one logical processor | 0.17% | 0.02% | 90.5% |
| Processor, peak of one logical processor | 1.50% | 0.50% | 66.7% |
| Attributed processes | 7 | 4 | |

Uncontrolled paired observation, 9 November 2026, 118 seconds:

| Measure | Slack 4.52.155 (Electron) | Backwater 0.3.0 (Tauri) | Reduction |
|---|---:|---:|---:|
| Physical footprint, median | 894,855,416 bytes | 183,815,552 bytes | 79.5% |
| Summed resident memory, median | 508,747,776 bytes | 180,912,128 bytes | 64.4% |
| Processor, mean of one logical processor | 0.27% | 0.05% | 81.2% |
| Processor, peak of one logical processor | 3.48% | 0.50% | 85.6% |
| Attributed processes | 9 | 4 | |

Application size on disk, 9 November 2026:

| Measure | Slack 4.52.155 | Backwater 0.3.2 | Reduction |
|---|---:|---:|---:|
| Installed application | 578,252,800 bytes | 11,608,064 bytes | 98.0% |
| Download | Not measured | 6,027,133 bytes | |

These measurements cover idle use only. The windows differ in size (1,668 by 1,066 points for Slack against 1,240 by 820), and official Slack carries features and caches that Backwater lacks. The 9 November observation leaves open conversations, uptime and workspace count uncontrolled, and a debug copy of Backwater ran at the same time outside the attribution. The 0.3.0 median footprint is 50.8% above the 0.2.0 figure, and the cause remains unverified. Startup time, scrolling, Huddles, screen sharing, energy use and Windows remain unmeasured.

## Privacy and security

Slack credentials stay in Rust and in the operating-system credential store (the Keychain on macOS), never in browser storage or files. Backwater contacts Slack, Amazon Chime for Huddle media, and GitHub for this update feed, and contains no telemetry or analytics. Backwater depends on private Slack web-client endpoints for browser sign-in, events and several views, so a change at Slack can break those features without notice. Security reports belong in the private vulnerability reporting of the source repository once it is public. Until then, an issue in this repository without exploit details is the contact route.

## Known limitations

- The macOS build has no Apple notarization, and Windows has no published build.
- Huddles are a feasibility implementation: Slack room-membership behavior, media continuity across windows and screen-share audio remain unverified or unimplemented.
- Activity and thread read state do not synchronize back to Slack.
- Live Slack account preference writes have no verification yet.
