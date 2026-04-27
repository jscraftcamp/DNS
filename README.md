# DNS

DNS-as-code for **jscraftcamp.org** using [OctoDNS](https://github.com/octodns/octodns) with the [Hetzner provider](https://github.com/octodns/octodns-hetzner).

## Prerequisites

- Python 3.12+
- A Hetzner DNS Console API token ([dns.hetzner.com](https://dns.hetzner.com))

## Local Setup

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Copy the environment template and fill in your token:

```bash
cp .env.example .env
# edit .env with your actual token
```

## Usage

**Dry-run** (preview changes without applying):

```bash
source .env
octodns-sync --config-file=octodns.yaml
```

**Apply** (push changes to Hetzner DNS):

```bash
source .env
octodns-sync --config-file=octodns.yaml --doit
```

## Importing Existing Records

If the zone already has records in Hetzner, dump them first to avoid deleting anything on the initial sync:

```bash
octodns-dump --config-file=octodns.yaml --output-dir=zones/ jscraftcamp.org. hetzner
```

Review `zones/jscraftcamp.org.yaml`, then commit the result.

## CI/CD

GitHub Actions runs automatically:

| Trigger | Behaviour |
|---|---|
| Pull request | `octodns-sync` dry-run (plan only) |
| Push to `main` | `octodns-sync --doit` (apply) |

The workflow requires a repository secret named `HETZNER_TOKEN`.

## Structure

```
.
├── octodns.yaml                  # OctoDNS configuration
├── zones/
│   └── jscraftcamp.org.yaml      # DNS records for jscraftcamp.org
├── requirements.txt              # Python dependencies
└── .github/workflows/
    └── octodns.yaml              # CI/CD workflow
```
