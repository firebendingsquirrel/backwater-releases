# Backwater releases

This repository holds signed builds of Backwater, an unofficial Slack client for macOS and Windows, and the `latest.json` feed that its in-app updater reads. Source code is not published here.

Each release attaches the macOS update package (`Backwater.app.tar.gz`), the Windows installer when available, their updater signatures and `latest.json`. The app verifies every package against the public key built into it before installing.
