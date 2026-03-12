# Contributing

Thanks for your interest in contributing to YouTube Music MCP!

## Getting Started

1. Fork the repo and clone it locally
2. Follow the [README](README.md) setup instructions
3. Make your changes on a new branch
4. Test your changes (see below)
5. Open a pull request

## Development Setup

```bash
# Prerequisites: macOS, Go 1.21+, Node.js 18+, Xcode

# Build everything
make build                    # Go MCP server
make bridge-install           # Safari extension + bridge app
make bridge-restart           # Restart bridge app
```

## Testing

```bash
make test                     # MCP handshake test
make bridge-status            # Verify bridge is running
cd safari-extension && npm run typecheck  # TypeScript types
```

For end-to-end testing, open YouTube Music in Safari after building and use an MCP client.

## Commit Messages

We use [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation
- `chore:` maintenance
- `refactor:` code change that neither fixes a bug nor adds a feature
- `ci:` CI/CD changes

## Pull Requests

- Keep PRs focused on a single change
- Include a clear description of what and why
- Make sure CI passes (Go build + TypeScript typecheck)
- Update documentation if your change affects user-facing behavior

## Reporting Issues

Use [GitHub Issues](https://github.com/ramin0/youtube-music-mcp/issues). Include:

- What you expected vs. what happened
- Steps to reproduce
- macOS version, Safari version
- Any relevant logs (`make bridge-status`, DevTools console)
