# QRSalt MCP server

Connect your AI assistant to QRSalt, and it can make QR codes and short links, change where they point, tell you how often they were scanned, and read a QR image you send it.

The server is remote. There is nothing to install or run: your assistant talks to

```
https://app.qrsalt.com/api/mcp
```

over Streamable HTTP. The first time it connects, a QRSalt page opens in your browser where you sign in, choose a workspace and tick what the assistant may do. If your client can't sign in, it can send an API key instead.

This repository holds the plugin manifests (`plugin.json`, `mcp.json`, and copies for Cursor and Claude Code) and the `server.json` that lists the server in the official MCP Registry as `io.github.QRSalt/qrsalt-mcp`. The server's code is not here.

## What you can ask it

- "Make me a QR code for tomorrow's open day and show me the picture."
- "Shorten this link and call it Spring flyer."
- "Which of my codes still point at the old domain?"
- "Point the table tent code at the new menu." The printed code stays the same; only its destination moves.
- "Which code was scanned most last month?"
- "Pause the trade-show banner code until March."
- "What's in this QR?" with the image attached.

## The 13 tools

What your assistant sees depends on what you allowed when you signed in (or what your key allows), and on your plan. A Free workspace gets the two picture tools. The other 11 come with the Pro plan.

**Pictures** (scope `render`, every plan)

| Tool | What it does |
| --- | --- |
| `render_qr_code` | Turns text or a link into a QR image. Nothing is saved. |
| `read_qr_image` | Reads the text out of a QR image you send as a file. It never fetches a URL. |

**Reading your account** (scope `read`)

| Tool | What it does |
| --- | --- |
| `list_codes` | Lists your QR codes and short links, newest first, with scan counts. |
| `get_code` | Everything recorded about one code: destination, short link, scans, last scan. |
| `list_folders` | Your folders and how many codes are in each. |
| `get_scan_analytics` | Scan totals and a daily series, for one code or the whole workspace. |
| `get_code_image` | The picture of a saved code, with its colours, shapes and logo. |

**Changing things** (scope `write`)

| Tool | What it does |
| --- | --- |
| `create_code` | Makes a new code for a link, text, email, phone number or text message. |
| `update_code` | Re-points, renames, files, tags, pauses or resumes a dynamic code. |
| `create_short_link` | Shortens a link. It counts clicks and can be re-pointed later. |
| `set_gs1_link` | Gives a dynamic code a GS1 Digital Link address for product packaging. |

**Deleting** (scope `delete`, never ticked for you)

| Tool | What it does |
| --- | --- |
| `remove_gs1_link` | Removes a code's GS1 Digital Link address. |
| `delete_code` | Deletes one code or short link, permanently. |

Both deleting tools need the code's exact name or short-link ending as a confirmation, so a vague "delete it" deletes nothing.

## Install

### Cursor

Install the plugin from [cursor.directory](https://cursor.directory/c/qrsalt), or add the server to `.cursor/mcp.json` (one project) or `~/.cursor/mcp.json` (all projects):

```json
{
  "mcpServers": {
    "qrsalt": {
      "url": "https://app.qrsalt.com/api/mcp"
    }
  }
}
```

Open Settings, then MCP, and connect. Cursor opens the QRSalt sign-in page.

### Claude Code

```
claude mcp add --transport http qrsalt https://app.qrsalt.com/api/mcp
```

Then run `/mcp` inside Claude Code, choose `qrsalt` and sign in.

Or install it as a plugin:

```
/plugin marketplace add QRSalt/qrsalt-mcp
/plugin install qrsalt@qrsalt
```

### Claude app (web and desktop)

1. Open Settings, then Connectors, and add a custom connector.
2. Paste `https://app.qrsalt.com/api/mcp` as the server URL.
3. Leave the OAuth client ID and secret empty.
4. Connect, and sign in on the QRSalt page that opens.

### VS Code

Add this to `.vscode/mcp.json`, press Start above the entry and sign in:

```json
{
  "servers": {
    "qrsalt": {
      "type": "http",
      "url": "https://app.qrsalt.com/api/mcp"
    }
  }
}
```

### Other clients

Any client that speaks MCP over Streamable HTTP can connect with the server URL. Clients that support MCP sign-in find everything else on their own. Clients built for older protocol revisions still work: the server answers `initialize` in 2025-06-18, 2025-03-26 and 2024-11-05, as well as the current 2026-07-28.

## Signing in

The first time your client connects, the server replies that it needs sign-in and says where to find out how. Your client opens a QRSalt page in your browser. There you:

1. sign in to QRSalt,
2. choose the workspace,
3. tick what the assistant may do. Delete is never ticked for you.

Only a workspace owner or admin can connect an assistant, and it acts as the person who connected it. To cut it off, revoke it under Dashboard, API, Connected apps.

For client developers: discovery starts at `https://app.qrsalt.com/.well-known/oauth-protected-resource/api/mcp`. The authorization server supports OAuth 2.1 with PKCE (S256), dynamic client registration and client ID metadata documents.

## Scopes

| Scope | Box on the sign-in page | Allows |
| --- | --- | --- |
| `render` | Render images | Drawing and reading QR images. Nothing is stored or read from your account. |
| `read` | Read | Listing codes, links and folders, and reading scans. |
| `write` | Create and change | Creating and changing codes and links. |
| `delete` | Delete | Deleting codes and removing GS1 addresses. Off unless you tick it. |

## Using an API key instead

If your client can set a header but can't sign in, make a key in the QRSalt dashboard under API and send it on every request:

```
Authorization: Bearer qr_live_YOUR_KEY
```

For Claude Code:

```
claude mcp add --transport http qrsalt https://app.qrsalt.com/api/mcp --header "Authorization: Bearer qr_live_YOUR_KEY"
```

Replace `qr_live_YOUR_KEY` with your own key. Make a key for the assistant alone and tick only what it needs, so you can revoke it without stopping anything else. Keep it out of files you commit.

## What it will not do

- No tool opens a web address you give it.
- Nothing touches design, custom domains, team members or billing.
- Every call runs against the workspace you connected. No tool can name a different one.

## Links

- Setup guide and tool reference: https://qrsalt.com/qr-code-mcp-server
- QRSalt: https://qrsalt.com
- MCP Registry entry: `io.github.QRSalt/qrsalt-mcp`

## License

MIT. See [LICENSE](LICENSE).
