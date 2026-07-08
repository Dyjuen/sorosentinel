# SoroSentinel

A Rust-based chaos engineering proxy for Soroban DApps. Intercepts traffic between your DApp and a Stellar RPC node to inject real mainnet failure modes — network congestion, dropped connections, sequence collisions, fee drifts, and more — so you can test resilience before audit or production.

## Architecture

SoroSentinel runs as a transparent HTTP proxy using hyper/axum. It sits between your Soroban DApp and the RPC endpoint, forwarding JSON-RPC requests and injecting faults according to YAML scenario profiles.

```
DApp → SoroSentinel Proxy → Stellar RPC Node
         ↕
    YAML Fault Profile
```

## Installation

```bash
cargo install sorosentinel
```

Or use the GitHub Action directly in your workflow:

```yaml
- name: Run SoroSentinel
  uses: sorosentinel/action@v1
  with:
    profile: network-stress/latency-burst.yaml
    rpc-endpoint: https://soroban-testnet.stellar.org
    api-token: ${{ secrets.SOROSENTINEL_TOKEN }}
```

## Quick Start

```bash
# Start the proxy with a fault profile
sorosentinel proxy --config scenarios/tx-bad-seq.yaml

# Run one-shot fault injection
sorosentinel run --profile scenarios/rpc-timeout.yaml --rpc https://rpc.example.com
```

## Fault Profiles

12+ pre-built YAML profiles organized into four domains:

| Domain | Profiles |
|--------|----------|
| Network Stress | Latency bursts, cascade failures |
| Transaction Errors | Sequence collisions, fee drift |
| RPC Failures | Timeout chains, connection dropouts |
| Fee Estimation Drift | Mispriced operations |

Custom profiles can be added — see [`docs/PROFILES.md`](docs/PROFILES.md) for the schema.

## Output

Every run produces a JUnit/XML report compatible with GitHub Actions, GitLab CI, Jenkins, and CircleCI. An optional HTML report viewer is also available.

```
sorosentinel run --profile scenarios/latency-burst.yaml --report junit
```

## Requirements

- Rust 1.80+
- Access to a Stellar RPC endpoint (testnet or mainnet)

## Development

```bash
git clone https://github.com/sorosentinel/sorosentinel
cd sorosentinel
cargo build
cargo test
```

## License

ISC
