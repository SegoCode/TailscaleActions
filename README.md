# TailscaleActions

[![Top language](https://img.shields.io/github/languages/top/SegoCode/TailscaleActions?style=flat-square)](https://github.com/SegoCode/TailscaleActions)
[![Repository size](https://img.shields.io/github/repo-size/SegoCode/TailscaleActions?style=flat-square&label=repo%20size)](https://github.com/SegoCode/TailscaleActions)
[![Commit activity per year](https://img.shields.io/github/commit-activity/y/SegoCode/TailscaleActions?style=flat-square&label=commits)](https://github.com/SegoCode/TailscaleActions/graphs/commit-activity)
[![License: MIT License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](https://github.com/SegoCode/TailscaleActions/blob/main/LICENSE)
[![Bitcoin BTC](https://img.shields.io/badge/buy_me_a_coffee-BTC-F7931A?style=flat-square&logo=bitcoin&logoColor=white)](https://github.com/SegoCode/SegoCode/discussions/2)

Configure a GitHub Actions runner as a [Tailscale](https://tailscale.com/) exit node. The workflow creates a temporary machine in your tailnet, advertises the runner as an exit node and keeps it active during the job.

## Quick Start & Information

> [!CAUTION]
> Running this action on a GitHub-hosted runner may violate GitHub's Acceptable Use Policies or other service terms. The workflow uses GitHub infrastructure as a VPN or proxy, forwards traffic for other devices and keeps the hosted runner active for several hours. GitHub may stop the job, restrict the repository or suspend the account. Review the current [GitHub Actions Terms](https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-services#github-actions) and [Acceptable Use Policies](https://docs.github.com/en/site-policy/acceptable-use-policies/github-acceptable-use-policies) before using it.

> [!NOTE]
> `tailscale/github-action@v4` still accepts `authkey`, but Tailscale recommends OAuth clients (`oauth-client-id`, `oauth-secret` and `tags`) for new setups.

### 1. Create an Auth Key with permissions for `tag:exitnode`

1. Open the [Tailscale admin console](https://login.tailscale.com/admin/settings/keys).
2. Click **Generate auth key**.
3. Configure these options:
   - Enable **Reusable** so the key can be reused on every run.
   - Enable **Ephemeral** so the device disappears after use.
   - Add `tag:exitnode` under **Tags**.
4. Copy the Auth Key and save it as a GitHub secret:
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

1. Open the repository's [**Actions**](https://github.com/SegoCode/TailscaleActions/actions) tab.
2. Select **Tailscale exit node** and **run the workflow**.
3. Select the exit node in Tailscale from the device that should route traffic through it.

The workflow keeps the runner active for approximately six hours. The ephemeral runner becomes unavailable when the job ends.

---
<p align="center"><a href="https://github.com/SegoCode/TailscaleActions/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=SegoCode/TailscaleActions" />
</a></p>
