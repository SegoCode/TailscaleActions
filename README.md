# {reponame}

<p align="center">
  <a href="#about">About</a> •
  <a href="#features">Features</a> •
  <a href="#quick-start--information">Quick Start & Information</a> •
  <a href="#download">Download</a>
</p>

## About
[![Top language](https://img.shields.io/github/languages/top/{username}/{reponame}?style=flat-square)](https://github.com/{username}/{reponame})
[![Repository size](https://img.shields.io/github/repo-size/{username}/{reponame}?style=flat-square&label=repo%20size)](https://github.com/{username}/{reponame})
[![Commit activity per year](https://img.shields.io/github/commit-activity/y/{username}/{reponame}?style=flat-square&label=commits)](https://github.com/{username}/{reponame}/graphs/commit-activity)
[![Commits since tagged version](https://img.shields.io/github/commits-since/{username}/{reponame}/latest?style=flat-square&label=commits%20since%20tag)](https://github.com/{username}/{reponame}/releases)
[![GitHub downloads](https://img.shields.io/github/downloads/{username}/{reponame}/total?style=flat-square&label=downloads)](https://github.com/{username}/{reponame}/releases)
[![Source Available Noncommercial License](https://img.shields.io/badge/license-Source%20Available%20Noncommercial-blue?style=flat-square)](https://github.com/{username}/{reponame}/blob/main/LICENSE)
[![Bitcoin BTC](https://img.shields.io/badge/buy_me_a_coffee-BTC-F7931A?style=flat-square&logo=bitcoin&logoColor=white)](https://github.com/SegoCode/SegoCode/discussions/2)

This project configures a GitHub Actions runner as an exit node for [Tailscale](https://tailscale.com/). The workflow creates a temporary device in your tailnet, advertises it as an exit node and keeps it running for the duration of the job.

## Features

- Enables the IPv4 and IPv6 forwarding required by a Tailscale exit node.
- Optimizes UDP forwarding with UDP GRO.
- Advertises the runner as an exit node using an auth key stored in GitHub Secrets.
- Keeps the runner active for approximately six hours.
- Starts only through a manually triggered workflow.

## Quick Start & Information

> [!WARNING]
> An exit node routes traffic from any device that selects it. Review your Tailscale configuration, protect the auth key and use this project only in a tailnet you control.

> [!CAUTION]
> Running this action on a GitHub-hosted runner may violate GitHub's Acceptable Use Policies or other service terms. The workflow uses GitHub infrastructure as a VPN or proxy, forwards traffic for other devices, and keeps the hosted runner active for several hours. GitHub may stop the job, restrict the repository or suspend the account. Review the current [GitHub Actions Terms](https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-services#github-actions) and [Acceptable Use Policies](https://docs.github.com/en/site-policy/acceptable-use-policies/github-acceptable-use-policies) before using it. You are responsible for obtaining approval and accepting any associated costs or consequences.

### 1. Create an Auth Key with `tag:exitnode` permissions

1. Open the [Tailscale admin console](https://login.tailscale.com/admin/settings/keys).
2. Click **Generate auth key**.
3. Configure these options:
   - Enable **Reusable** so the key can be used on every run.
   - Enable **Ephemeral** so the device disappears after use.
   - Add `tag:exitnode` under **Tags**.
4. Copy the generated Auth Key and save it as a GitHub secret:
   - Open the repository on GitHub.
   - Go to **Settings** → **Secrets and variables** → **Actions**.
   - Click **New repository secret**.
   - Use `TAILSCALE_AUTHKEY` as the name.
   - Paste the Auth Key as the value.

### 2. Configure Tailscale ACLs to auto-approve the exit node

1. Open the [Tailscale admin console](https://login.tailscale.com/admin/acls).
2. Click **Edit policy**.
3. Add these entries to the ACL policy. Keep the rest of your tailnet rules:

```jsonc
{
  "tagOwners": {
    "tag:exitnode": ["autogroup:admin"]
  },

  "autoApprovers": {
    "exitNode": ["tag:exitnode"]
  }
}
```

The `tagOwners` entry lets administrators assign `tag:exitnode`. The `autoApprovers` entry automatically approves tagged devices as exit nodes.

### 3. Start the exit node

1. Open the repository's [**Actions**](https://github.com/{username}/{reponame}/actions) tab.
2. Select **Tailscale exit node**.
3. Click **Run workflow**.
4. Wait for the workflow to finish configuring the runner.
5. Select the exit node from the Tailscale device that should route traffic through it.

The workflow keeps the runner active for approximately six hours. The ephemeral runner becomes unavailable when the job ends.

### Available Parameters

The workflow does not require input parameters. It uses the `TAILSCALE_AUTHKEY` secret and sets the hostname to `us-azure-node` through the Tailscale arguments.

```yaml
args: "--advertise-exit-node --hostname us-azure-node"
```

## Download

View the [`tailscale-exit-node.yml`](https://github.com/{username}/{reponame}/blob/main/.github/workflows/tailscale-exit-node.yml) workflow or download a version from [Releases](https://github.com/{username}/{reponame}/releases).

---
<p align="center"><a href="https://github.com/{username}/{reponame}/graphs/contributors">
  <img src="https://contrib.rocks/image?repo={username}/{reponame}" />
</a></p>
