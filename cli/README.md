# Conduit CLI

Command-line interface for running a Psiphon Conduit node - a volunteer-run proxy that relays traffic for users in censored regions.

## Quick Start

Want to run a Conduit station? Get the latest CLI release: https://github.com/Psiphon-Inc/conduit/releases

Our official CLI releases include an embedded psiphon config.

Contact Psiphon (conduit-oss@psiphon.ca) to discuss custom configuration values.

Conduit deployment guide: [GUIDE.md](./GUIDE.md)

## Docker

Use the official Docker image, which includes an embedded Psiphon config. Docker Compose is a convenient way to run Conduit if you prefer a declarative setup.

```bash
docker compose up
```

The compose file includes:
- Conduit service (metrics endpoint on `conduit:9090`)
- Prometheus (`http://localhost:9091`)
- Grafana (`http://localhost:3000`, default login: `admin` / `conduit`)

Grafana ships with a pre-provisioned dashboard showing:
- Connected vs connecting clients over time
- Upload/download bandwidth rates
- Total transferred bytes
- Station live status
- Configured bandwidth limit

## Building From Source

```bash
# First time setup (clones required dependencies)
make setup

# Build
make build

# Run
./dist/conduit start --psiphon-config /path/to/psiphon_config.json
```

## Requirements

- **Go 1.24.x** (Go 1.25+ is not supported due to psiphon-tls compatibility)
- Psiphon network configuration file (JSON)

The Makefile will automatically install Go 1.24.12 if not present.

## Usage

```bash
# Start with default settings
conduit start

# Customize limits
conduit start --max-clients 20 --bandwidth 10

# Verbose output (info messages)
conduit start -v
```

### Options

| Flag                   | Default  | Description                                |
| ---------------------- | -------- | ------------------------------------------ |
| `--psiphon-config, -c` | -        | Path to Psiphon network configuration file |
| `--max-clients, -m`    | 500      | Maximum concurrent clients                 |
| `--bandwidth, -b`      | 400      | Bandwidth limit per peer in Mbps           |
| `--data-dir, -d`       | `./data` | Directory for keys and state               |
| `--metrics-addr`       | -        | Prometheus metrics listen address          |
| `-v`                   | -        | Verbose output                             |

## Data Directory

Keys and state are stored in the data directory (default: `./data`):

- `conduit_key.json` - Node identity keypair
  The Psiphon broker tracks proxy reputation by key. Always use a persistent volume to preserve your key across container restarts, otherwise you'll start with zero reputation and may not receive client connections for some time.

## Building

```bash
# Build for current platform
make build

# Build with embedded config (single-binary distribution)
make build-embedded PSIPHON_CONFIG=./psiphon_config.json

# Build for all platforms
make build-all

# Individual platform builds
make build-linux       # Linux amd64
make build-linux-arm   # Linux arm64
make build-darwin      # macOS Intel
make build-darwin-arm  # macOS Apple Silicon
make build-windows     # Windows amd64
```

Binaries are output to `dist/`.
