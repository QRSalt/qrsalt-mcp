# Changelog

## 1.0.0 (2026-09-28)

First release.

- `plugin.json` and `mcp.json` in the Agent Plugins 1.1.0 format, pointing at the remote server `https://app.qrsalt.com/api/mcp` over Streamable HTTP.
- The same manifest for Cursor (`.cursor-plugin/plugin.json`) and Claude Code (`.claude-plugin/plugin.json`, with a one-plugin marketplace).
- `server.json` for the official MCP Registry, published as `io.github.QRSalt/qrsalt-mcp`.
- Sign-in with OAuth on first use, or an API key in an `Authorization` header.
