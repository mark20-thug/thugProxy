# thugproxy

Open-source OpenVPN launcher that routes through a chain of **fast, verified proxies**.

```
==================================================
  THUGPROXY
==================================================
```

## How it works

1. **You add proxy servers** to your pool: `./thugproxy add socks5://1.2.3.4:1080`
2. **It tests** every proxy in the pool and ranks them by reachability + latency: `./thugproxy test`
3. **It starts** the VPN through a working first proxy from the pool, then **enriches** the chain in the background with more reachable proxies (max 2 hops, respecting country diversity)
4. **It connects** OpenVPN through the chain: `sudo ./thugproxy run`

If the chain dies, it automatically searches for a replacement from the pool.

## Features

- Auto-builds a multi-hop proxy chain from a pool of free proxies
- Starts with a working first proxy from the pool, then enriches the chain
  in the background by testing further reachable proxies until the chain
  reaches the max hop count
- Per-proxy health checks with measured latency (parallel, 20 workers)
- Fast-first chain selection, with country diversity
- End-to-end chain validation via ProxyChains (`strict_chain`)
- Auto-failover when the active chain goes down
- Red ASCII-art banner, because naming things is hard

## Installation

```bash
git clone <repository-url>
cd thugproxy
chmod +x thugproxy
```

Copy one or more `.ovpn` files into the script directory (or point `THUGPROXY_OVPN_DIR` at them).

## Requirements

- Linux (Debian/Ubuntu-ish)
- `python3` (>= 3.8)
- `openvpn`
- `proxychains4` / `proxychains` (`proxychains` package)
- `curl`
- `sudo` for running

Install deps:

```bash
sudo apt install python3 openvpn proxychains4 curl
```

## Usage

```bash
# Add proxies to your pool
./thugproxy add socks5://1.2.3.4:1080
./thugproxy add 5.6.7.8:1080 --type socks4

# Check which proxies are up and how fast
./thugproxy test

# Inspect the pool / saved chain
./thugproxy list

# Connect through the fastest chain
sudo ./thugproxy run
```

## Proxy list formats

One proxy per line in `proxy-pool.txt` (or via `thugproxy add`):

```
socks5://1.2.3.4:1080  # US
http://45.194.3.204:8080  # IN
socks4 111.125.154.22 1080
```

Optional `# CC` country annotation is used to pick geographically diverse chains.

## Configuration

| Variable | Default | Meaning |
|---|---|---|
| `THUGPROXY_HOME` | directory of the script | Proxy lists, auth file |
| `THUGPROXY_OVPN_DIR` | `THUGPROXY_HOME` | Directory with `.ovpn` files |
| `PROXYCHAINS_CONF` | `/etc/proxychains.conf` | ProxyChains config path |

## License

MIT - see [LICENSE](LICENSE).
