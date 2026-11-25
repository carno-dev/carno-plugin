# Carno for Claude Code

**Share the apps you make with Claude Code.** You made something and want your friends to be
able to open it. Type `/carno:share` (or just ask Claude to "share this with my friends") and
you get a link that anyone can open on a phone or computer.

- No account, no API key, no setup
- Works for a web page, a game, a form, a small tool, or a full app with a server and a database
- Keys and `.env` files never leave your computer

## Install

In your terminal:

```bash
claude plugin install carno --marketplace carno-dev/carno-plugin
```

Or, inside a Claude Code session:

```
/plugin install carno --marketplace carno-dev/carno-plugin
```

## Use

In any project:

```
/carno:share
```

Claude puts the app online with [Carno](https://www.carno.ai) and replies with:

- **the link** to send your friends
- **an access code** they type to open it
- **a claim link**: apps without an account last 7 days. Open the claim link and sign in with
  your email to keep the app, update it at the same link, and choose exactly who can open it.

You don't have to type the command. "Share this with my friends", "give me a link to this" and
"put this online" work too.

## What's inside

| Part | What it does |
|---|---|
| `skills/share` | The `/carno:share` skill: picks the folder, runs [`npx carno-ai`](https://www.npmjs.com/package/carno-ai), and explains the result |
| `.mcp.json` | Connects Carno's MCP server (`https://mcp.carno.ai/mcp`). Once you sign in through `/mcp`, Claude can list your apps, show logs, roll back and share with specific people |

## What it runs and sends

Everything the plugin does, in full:

- **Runs** the Carno command-line tool from npm, pinned to an exact version:
  `npx -y carno-ai@0.1.0` (source: [carno-ai on npm](https://www.npmjs.com/package/carno-ai), MIT).
  It runs only when you ask to share an app, and Claude Code asks your permission first.
- **Sends** the project folder you chose to Carno (`https://www.carno.ai`), which builds it and
  puts it online. The upload leaves out `.env` files, `node_modules`, `.git` and build output, and
  honours `.gitignore` and `.carnoignore`. Carno scans the upload for passwords and keys and
  stops if it finds one.
- **Stores** a random device token in `~/.carno/config.json` so your no-account apps stay tied
  to your computer. If you sign in, a sign-in token is stored in `~/.config/carno/credentials.json`.
  Both files are readable only by you.
- **Connects** to Carno's MCP server at `https://mcp.carno.ai/mcp`. It does nothing until you
  sign in through `/mcp`.

It reads no other files, uses no credentials from your environment, and sends nothing anywhere
else. Carno's [privacy policy](https://www.carno.ai/privacy) covers what happens to uploads.

## Links

- Website: https://www.carno.ai
- Guides: https://www.carno.ai/guides
- Privacy: https://www.carno.ai/privacy · Terms: https://www.carno.ai/terms
- Support: support@carno.ai

MIT licensed.
