# Changelog

All notable changes to this plugin will be documented here.

## 1.2.0 — OpenAI Codex plugin

- Added the OpenAI Codex plugin (`postzen-dev/postzen-codex-plugin`): same hosted MCP server and four skills, packaged in the `.codex-plugin/plugin.json` format. No changes for other hosts.

## 1.1.0 — generated from the unified source repository

- The plugin is now generated from `postzen-dev/postzen-plugins`, with no functional changes.
- The Claude Code plugin version jumps from 0.1.0 to align with the other hosts.

## 1.0.0 — initial release

- Added the `postzen` MCP server pointing at `https://mcp.postzen.dev/mcp` (OAuth 2.1 with dynamic client registration — no API key or client ID to configure).
- Added four skills: `postzen-post`, `postzen-queue`, `postzen-analytics`, `postzen-connect`.
- Logo: PostZen's official mark.
