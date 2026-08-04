# TailscaleActions

<p align="center">
  <a href="#about">About</a> •
  <a href="#features">Features</a> •
  <a href="#quick-start--information">Quick Start & Information</a> •
  <a href="#download">Download</a>
</p>

## About
[![Top language](https://img.shields.io/github/languages/top/SegoCode/TailscaleActions?style=flat-square)](https://github.com/SegoCode/TailscaleActions)
[![Repository size](https://img.shields.io/github/repo-size/SegoCode/TailscaleActions?style=flat-square&label=repo%20size)](https://github.com/SegoCode/TailscaleActions)
[![Commit activity per year](https://img.shields.io/github/commit-activity/y/SegoCode/TailscaleActions?style=flat-square&label=commits)](https://github.com/SegoCode/TailscaleActions/graphs/commit-activity)
[![Commits since tagged version](https://img.shields.io/github/commits-since/SegoCode/TailscaleActions/latest?style=flat-square&label=commits%20since%20tag)](https://github.com/SegoCode/TailscaleActions/releases)
[![GitHub downloads](https://img.shields.io/github/downloads/SegoCode/TailscaleActions/total?style=flat-square&label=downloads)](https://github.com/SegoCode/TailscaleActions/releases)
[![Source Available Noncommercial License](https://img.shields.io/badge/license-Source%20Available%20Noncommercial-blue?style=flat-square)](https://github.com/SegoCode/TailscaleActions/blob/main/LICENSE)
[![Bitcoin BTC](https://img.shields.io/badge/buy_me_a_coffee-BTC-F7931A?style=flat-square&logo=bitcoin&logoColor=white)](https://github.com/SegoCode/SegoCode/discussions/2)

Configura un runner de GitHub Actions como exit node de [Tailscale](https://tailscale.com/). La workflow crea una máquina temporal en la red tailnet, anuncia el runner como exit node y lo mantiene activo durante la ejecución.

## Features

- Configura el forwarding IPv4 e IPv6 necesario para un exit node.
- Optimiza el forwarding UDP mediante UDP GRO.
- Anuncia el runner como exit node con una auth key almacenada en GitHub Secrets.
- Mantiene el runner activo durante aproximadamente seis horas.
- Usa una workflow manual para iniciar el exit node solo cuando lo necesitas.

## Quick Start & Information

> [!WARNING]
> Un exit node enruta el tráfico de los dispositivos que lo seleccionen. Revisa la configuración de Tailscale, protege la auth key y utiliza este proyecto solo en una tailnet que administres.

### 1. Crear un Auth Key con permisos para `tag:exitnode`

1. Accede a la [consola de administración de Tailscale](https://login.tailscale.com/admin/settings/keys).
2. Haz clic en **Generate auth key**.
3. Configura estas opciones:
   - Activa **Reusable** para reutilizar la key en cada ejecución.
   - Activa **Ephemeral** para que el dispositivo desaparezca después de su uso.
   - En **Tags**, agrega `tag:exitnode`.
4. Copia el Auth Key y guárdalo como secreto en GitHub:
   - Abre el repositorio en GitHub.
   - Ve a **Settings** → **Secrets and variables** → **Actions**.
   - Haz clic en **New repository secret**.
   - Usa `TAILSCALE_AUTHKEY` como nombre.
   - Pega el Auth Key como valor.

### 2. Configurar ACLs en Tailscale para autoaprobar el exit node

1. Accede a la [consola de administración de Tailscale](https://login.tailscale.com/admin/acls).
2. Haz clic en **Edit policy**.
3. Añade estas entradas a la política ACL. Conserva las demás reglas de tu tailnet:

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

La entrada `tagOwners` permite que los administradores asignen `tag:exitnode`. La entrada `autoApprovers` aprueba automáticamente como exit node los dispositivos que lleven ese tag.

### 3. Iniciar el exit node

1. Abre la pestaña [**Actions**](https://github.com/SegoCode/TailscaleActions/actions) del repositorio.
2. Selecciona **Tailscale exit node**.
3. Haz clic en **Run workflow**.
4. Espera a que la ejecución complete la configuración del runner.
5. En Tailscale, selecciona el exit node desde el dispositivo que quieras enrutar.

La workflow mantiene el runner activo durante aproximadamente seis horas. Al terminar la ejecución, el runner efímero deja de estar disponible.

### Available Parameters

La workflow no necesita parámetros. Utiliza el secreto `TAILSCALE_AUTHKEY` y configura el hostname `us-azure-node` mediante los argumentos de Tailscale.

```yaml
args: "--advertise-exit-node --hostname us-azure-node"
```

## Download

Consulta la workflow [`tailscale-exit-node.yml`](https://github.com/SegoCode/TailscaleActions/blob/main/.github/workflows/tailscale-exit-node.yml) o descarga una versión desde [Releases](https://github.com/SegoCode/TailscaleActions/releases).

---
<p align="center"><a href="https://github.com/SegoCode/TailscaleActions/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=SegoCode/TailscaleActions" />
</a></p>
