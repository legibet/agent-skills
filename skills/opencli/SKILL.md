---
name: opencli
description: Read or act on websites through the user's real, logged-in Chrome via the `opencli` CLI. Use when a task touches a specific site's content or the user's account there (知乎, Reddit, 小红书, X, B站…), when a page needs login or blocks plain fetching, or when a page must be clicked, filled, or extracted interactively.
---

# opencli — websites through the user's Chrome

`opencli` runs inside the user's everyday Chrome through an extension, so every request carries their real login, cookies, and browser fingerprint. Two layers:

- **Adapters**: `opencli <site> <command>`, deterministic commands that return structured data, mostly by calling the site's own API from inside the page.
- **Browser primitives**: `opencli browser <session> <command>`, to open, read, click, and type on any page.

Public pages with no login or anti-bot wall are cheaper through plain fetching; reach for opencli when one of the description's cases holds.

## Pick one layer

List the site's adapters first. If one covers the task, use it and you are done; most tasks on a covered site end here. Drive the page with browser primitives only for what no adapter covers.

## Adapters

```bash
opencli list -f json | jq -r '.[] | select(.site=="zhihu") | "\(.name)\t\(.access)\t\(.description)"'
opencli zhihu question --help        # args, defaults, output columns
opencli zhihu question 2087643546463613137 --limit 5 -f json
```

- Site keys are slugs like `xiaohongshu`, `twitter`, `bilibili`; list them with `opencli list -f json | jq '[.[].site] | unique'`.
- Pass `-f json` on every adapter call.
- Adapters open and close their own background tab; nothing to clean up.

## Browser primitives

```bash
opencli browser t open "https://example.com/list" --window background
opencli browser t wait selector ".item" --timeout 15000
opencli browser t find --css ".item a" --limit 10        # refs, text, attrs
opencli browser t eval "(() => [...document.querySelectorAll('.item')].map(e => ({title: e.querySelector('h2')?.textContent.trim(), href: e.querySelector('a')?.href})))()"
opencli browser t click 12                               # ref from find/state, or a CSS selector
opencli browser t wait selector ".detail"
opencli browser t extract --chunk-size 8000              # page as markdown; loop on next_start_char
opencli browser t close
```

- One session name per task; reuse it across calls so the tab persists, and `close` it when done.
- `--window background` keeps the work out of the user's way; drop it when the user wants to watch.
- To work in a tab the user already has open (a page they logged into or set up), `bind` the session instead of `open`, and `unbind` at the end. opencli never closes a bound tab.
- Read with targeted queries: `find --css` for elements and refs, `eval` for structured data, `extract` for long text. Use `state` only on light pages or when you need the whole page's ref map: on heavy pages (Reddit, web-component UIs) it exceeds 60k tokens.
- After any navigation or page-changing action, `wait` before reading again; refs from the previous page are void.
- `eval` is for reading; make changes with `click`, `type`, `fill`, `select`, `keys`.
- `network` lists the JSON the page fetched (`--detail <key>` for one body). It pays off on API-driven SPAs; server-rendered pages have no data requests to capture.
- Per-command flags: `opencli browser <s> <command> --help`.

## Writes act on the user's real accounts

Adapter commands with `access: write` (post, comment, reply, like, follow, favorite, subscribe, delete…) and any browser action that submits, posts, or sends act publicly as the user. State exactly what will happen and get the user's confirmation before running one. Reads need no confirmation.

## Failures

| Signal | Meaning | Do |
|---|---|---|
| exit 69 | Browser bridge not connected | Run `opencli doctor` and relay its fix to the user. |
| exit 77 | Not logged in to the site | Ask the user to log in in Chrome, then retry. |
| exit 75, or `Navigation rejected` | Transient | Retry once. |
| exit 66 | Empty result | Recheck the args; the site may have changed. |
| Adapter returns broken or wrong data | Site changed | Rerun with `--trace retain-on-failure` and report the trace summary to the user; fixing the adapter is the user's call. |
