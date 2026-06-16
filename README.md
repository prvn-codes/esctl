# esctl

**Elasticsearch Cluster Manager** — manage multiple local Elasticsearch clusters from the CLI.

Run isolated ES clusters per project, switch between them instantly, and never touch config files manually.

```
esctl start myapp
esctl list
esctl stop myapp
```

---

## Install

### curl (recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/prvn-codes/esctl/main/bin/esctl \
  -o /usr/local/bin/esctl && chmod +x /usr/local/bin/esctl
```

### Homebrew

```bash
brew tap prvn-codes/tap
brew trust prvn-codes/tap   # required for third-party taps
brew install esctl
```

> Homebrew requires trusting third-party taps before installing. This is a one-time step.

---

## Requirements

| Tool | Required | Notes |
|------|----------|-------|
| `jq` | Yes | Auto-installed via Homebrew if missing |
| `curl` | No | Used for HTTP readiness checks |
| Elasticsearch | Yes | Any version — see [Auto-detect](#auto-detect) |

---

## Auto-detect

esctl finds your Elasticsearch binary automatically in this order:

1. `$ESCTL_ES_BIN` env var
2. evm installations in `~/.evm` (latest version picked automatically)
3. `elasticsearch` on your `$PATH`
4. Common paths: `/usr/local/bin`, `/opt/homebrew/bin`, `/usr/share/elasticsearch/bin`

Works with [evm](https://github.com/duydo/evm), Homebrew, manual installs — anything.

---

## Usage

```
esctl <command> [cluster]

COMMANDS
  start   [cluster]    Start a cluster (or all if omitted)
  stop    [cluster]    Stop a cluster (or all if omitted)
  restart [cluster]    Restart a cluster (or all if omitted)
  status  [cluster]    Show cluster status
  list                 List all clusters with live status
  add                  Add a new cluster (interactive)
  edit    <cluster>    Edit a cluster's configuration
  remove  <cluster>    Remove a cluster
  doctor               Check dependencies and configuration
  config               Show config file path and contents
  version              Show esctl version
```

### Examples

```bash
# First time setup — check your environment
esctl doctor

# Add a cluster for your project
esctl add
# → prompts for name, port, directories, ES binary

# Start it
esctl start myapp

# See all clusters and their live status
esctl list

# Edit a cluster's config
esctl edit myapp

# Stop everything
esctl stop
```

### Live status table

```
CLUSTER                NODE                   PORT     STATUS     UPTIME       MEMORY       HTTP     ACCESS URL
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
myapp                  node-myapp             9200     RUNNING    12:04        1GB          OK       http://localhost:9200
staging                node-staging           9201     STOPPED    N/A          N/A          N/A      —
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

---

## Configuration

Config is stored at `~/.esctl/clusters.json`. You can override the path:

```bash
export ESCTL_CONFIG=~/my-custom-config.json
```

Each cluster stores:

```json
{
  "name": "myapp",
  "node_name": "node-myapp",
  "port": 9200,
  "data_dir": "$HOME/.esctl/data/myapp",
  "logs_dir": "$HOME/.esctl/logs/myapp",
  "es_bin": null
}
```

`es_bin` is `null` by default (auto-detect). Set it per-cluster to pin a specific ES version:

```bash
esctl edit myapp
# → ES binary: /path/to/specific/elasticsearch
```

### Environment variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ESCTL_ES_BIN` | — | Path to Elasticsearch binary (global override) |
| `ESCTL_CONFIG` | `~/.esctl/clusters.json` | Config file location |

---

## Updating

```bash
brew upgrade esctl
```

---

## License

MIT
