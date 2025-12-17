# Design Tokens Migration Commands

Claude Code slash commands for migrating codebases to semantic color tokens.

## Commands

| Command | Description |
|---------|-------------|
| `/design-tokens-migration` | Migrate any CSS codebase to semantic tokens |
| `/tailwind-design-token-migration` | Migrate Tailwind v4 projects to semantic tokens |

## Installation

Copy the `commands/` folder to your project's `.claude/` directory:

```bash
cp -r commands/ your-project/.claude/commands/
```

## Usage

1. Open Claude Code in your project
2. Run the desired slash command
3. Follow the phased migration process with approval checkpoints

## What It Does

- Scans for all color usage (CSS variables, hex, rgb, named colors)
- Maps colors to semantic tokens (`--color-{section}-{token}`)
- Replaces color usage throughout the codebase
- Validates no hardcoded colors remain

## Semantic Token Categories

- `background` - Page layers
- `brand` - Interactive element backgrounds
- `status` - State indicators (error, success, info)
- `container` - Component backgrounds
- `text` - Typography colors
- `borders` - Component outlines
- `shadows` - Elevation effects

## License

MIT
