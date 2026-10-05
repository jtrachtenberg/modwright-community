<p align="center">
  <img src="assets/modwright.webp" width="384" alt="A wright in a woodcut-style print swings a hammer at a great wooden gear wheel on a harbour shore, above the word ModWright">
</p>

# ModWright community

This is the public home of [ModWright](https://www.npmjs.com/package/modwright),
a game-agnostic [MCP](https://modelcontextprotocol.io) server for game
modding: bug reports, game and feature requests, questions and release notes.
The package itself is on npm, and its full documentation is the README shown
there.

ModWright gives an AI assistant the tools a modder uses across Cyberpunk 2077,
Baldur's Gate 3, Skyrim Special Edition, Elden Ring, Valheim, Subnautica 2,
Stardew Valley and Nivalis Nights:
- install and mod detection, and load orders;
- log triage and compatibility checks;
- finding which mod is doing something: who owns a file, which mods write
  the same record, and what the frameworks' own logs say;
- the game's own data and a sourced knowledge base;
- scaffolding, validation, build and deploy;
- an in-game bridge where the game supports one.

## Install

Node.js 20 or newer. For Claude Code on macOS or Linux:

```bash
claude mcp add modwright -- npx -y modwright@latest
```

On Windows, MCP clients cannot start `npx` directly, so it goes through
`cmd /c`:

```bash
claude mcp add modwright -- cmd /c npx -y modwright@latest
```

Any other MCP client takes the same command in its configuration. On
Windows, use `"command": "cmd"` and put `"/c", "npx"` in front of the
arguments.

```json
{
  "mcpServers": {
    "modwright": { "command": "npx", "args": ["-y", "modwright@latest"] }
  }
}
```

- **Check it worked:** `npx -y modwright@latest --version` prints the
  version. Then ask your assistant to run `list_games`, and
  `check_toolchain` for your game.
- **Updates:** `@latest` makes npx look for a new release each time your
  client starts the server. Without it npx keeps the copy it cached the
  first time. To stay on one version, name it instead, for example
  `modwright@0.1.5`.

## Reporting a problem

[Open an issue](../../issues/new/choose). The template asks for what makes a
report actionable:
- the ModWright version: `npx modwright --version`;
- the game, and your OS;
- the tool call and what it returned;
- `check_toolchain game=<id>` output, where a tool or data folder is involved.

ModWright never needs your save files or your mod's private sources to
diagnose a problem. Paste only what you are comfortable making public.

A security problem goes to **Report a vulnerability** on this repository's
[Security tab](../../security), not to a public issue.

Questions and ideas that aren't a bug go to
[Discussions](../../discussions).

## Release notes

See [CHANGELOG.md](CHANGELOG.md).

## License

MIT; see [LICENSE](LICENSE). Files ModWright generates into your own projects
are yours, with no attribution required. Data extracted from a game remains
its publisher's. This repository holds no source code.
