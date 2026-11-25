---
name: share
description: Put the app in this project online with Carno and give the person one link their friends can open. Use when someone asks to share an app, put it online, publish it, deploy it, host it, or "send a link" to it so other people can try it.
when_to_use: 'Trigger phrases: "share this with my friends", "how do I share this app", "give me a link", "put this online", "let my team try it", "host this", "deploy this so others can use it".'
argument-hint: "[folder]"
allowed-tools: Bash(npx -y carno-ai@latest *)
---

# Share this app with Carno

Carno puts the app online and returns a link anyone can open on a phone or computer. It
works without an account, without an API key, and without any setup.

## 1. Pick the folder

- If the person gave a folder (`$ARGUMENTS`), use it.
- Otherwise use the project you're working in (the repository root, or the current
  directory).
- Don't upload a home directory, `/`, or a folder with several unrelated projects. Ask which
  app they mean instead.

Tell the person in one line what you're about to do, for example "Putting `my-app` online
with Carno…". The upload leaves out `.env` files, `node_modules`, `.git` and build output,
and it honours `.gitignore` and `.carnoignore`. Don't paste or print secrets.

## 2. Check whether they're signed in to Carno

```bash
npx -y carno-ai@latest --json whoami
```

- `"signed_in": true`: go to step 3b. The app becomes theirs and keeps one link across updates.
- Anything else: go to step 3a. This is the usual case.

## 3a. No account (default)

Run this from the project, passing the folder:

```bash
npx -y carno-ai@latest --json deploy "<folder>"
```

It prints one JSON document. Act on `status`:

| `status` | What to tell the person |
|---|---|
| `live` | Give them the `url` (the link to share), the `accessCode` (friends type it to open the app) and the `claimUrl`. Say plainly: **the app lasts 7 days unless they open the claim link** and sign in with their email. Claimed apps don't expire, and they can choose exactly who can open them. |
| `needs_keys` | The app needs an API key or password to run. Keys never go in the upload. They should open the `claimUrl`, sign in, and add the key on the app's Keys page. Then the app starts. |
| `couldnt_start` | Show the `message`. If the cause is clear in the code (a missing start script, a wrong port, a build error), offer to fix it, then run the deploy again. |
| `rejected` | Show the `message`. Usually the scan found what looks like a password or key in the code. Offer to move it into an environment variable (read from `process.env`, kept in `.env`, which is never uploaded), then deploy again. They add the real value after claiming the app. |
| `needs_review` | The upload is being checked. Tell them it will be live shortly and to try the link again in a minute. |

The exit code means the same thing: `0` live, `2` couldn't start or needs a key, `3` blocked,
`1` anything else. If the command fails before printing JSON (no network, npm unavailable),
say what failed in one sentence.

Each no-account deploy makes a **new** link. To update the same link after changes, they claim
the app once and sign in (`npx -y carno-ai@latest login`). From then on, step 3b updates it in place.

## 3b. Signed in

```bash
npx -y carno-ai@latest --json push "<folder>" --note "<one plain sentence about what changed>"
```

- `live_url`: the app is live at that link. Give it to them.
- `preview_url` only: the new version is ready to check at that link. They make it live from
  the app's page in Carno.
- `state` `needs_choice`: the folder holds several projects. Ask which one and push that folder.
- `blocked` with `reason` `bloated_upload`: the folder is mostly data or build files. Show the
  message and ask before adding `--force`.
- `missing_keys`: list them. They add them on the app's Keys page in Carno.
- A `needs_payment` blocker: hosting this app needs a payment method. Only the owner can add one,
  on the billing page in Carno. Don't try to work around it.

## Afterwards

Keep the reply short: the link, the access code if there is one, the claim link and the 7-day
note for no-account apps, and one line on how to update it. Don't describe Carno's pricing
unless they ask. For a lasting setup inside Claude Code, the plugin also connects Carno's MCP
server, which can list their apps, show logs, roll back and share with specific people once
they sign in through `/mcp`.
