# Code Server OnDemand App

A BatchConnect app that launches a VS Code environment in your browser on an HPC cluster. It runs `code-server` inside a SLURM batch job, so you get a full editor with access to cluster storage and compute without SSH or a local VS Code setup.

## How it works

OnDemand submits a batch job that starts `code-server` on an allocated port. The session is password-protected. OnDemand proxies the connection to your browser so the editor is accessible from the web portal.

## Prerequisites

- Access to an OnDemand portal on your cluster
- `code-server` installed on the compute nodes (or available via a shared path)
- Lmod for loading the `git` module

## Setup

Clone into your OnDemand apps directory:

```bash
git clone https://github.com/SammyCode002/code-server ~/ondemand/dev/code-server
```

Open `code-server/template/script.sh.erb` and update the `CODE_SERVER` path to match where `code-server` lives on your system:

```bash
CODE_SERVER="/path/to/code-server/bin/code-server"
```

The current path is set for CHPC Utah (version 3.5.0). You will need to change it for your cluster.

## How the session works

The script sets `PASSWORD` from the OnDemand-generated session password, then starts `code-server` with:

- `--auth=password` — requires the session password to open the editor
- `--bind-addr=0.0.0.0:<port>` — binds to the allocated port
- `--disable-telemetry` — no usage data sent to Coder
- A separate data directory at `~/.local/share/codeserver-ood` to keep settings isolated from other installs

The working directory defaults to `$HOME`.

## Known issues

- In-app extension installs do not work (no outbound internet from compute nodes on most clusters)
- The password auth between your browser and the OnDemand proxy is not encrypted end-to-end — do not use this over an untrusted network without cluster VPN

## License

See `code-server/LICENSE`. Code Server is developed by [Coder](https://github.com/coder/code-server).
