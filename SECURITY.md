# Security Policy

## Supported Versions

| Version | Supported |
|---|---|
| Latest release | Yes |
| Older releases | No |

## Reporting a Vulnerability

If you discover a security vulnerability, please report it responsibly:

1. **Do not** open a public GitHub issue
2. Use [GitHub Security Advisories](https://github.com/ramin0/youtube-music-mcp/security/advisories/new) to report privately
3. Or email the maintainer directly

You should receive a response within 48 hours. We will work with you to understand the issue and coordinate a fix before any public disclosure.

## Security Considerations

- The bridge app listens on `127.0.0.1:8765` (localhost only). It does not accept remote connections.
- The optional `YTM_BRIDGE_TOKEN` provides bearer token authentication for the bridge HTTP endpoints.
- The Safari extension only runs on `https://music.youtube.com/*` pages.
- No user credentials or cookies are accessed or transmitted by this tool.
