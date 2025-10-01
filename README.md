Configurar un **GitHub Actions runner como un exit node de Tailscale**

---

## 1️⃣ Crear un Auth Key con permisos para `tag:exitnode`

1. Accede a la [consola de administración de Tailscale](https://login.tailscale.com/admin/settings/keys).
2. Haz clic en **"Generate auth key"**.
3. Configura las siguientes opciones:
   - **Reusable**  (La key puede reutilizarse en cada run)
   - **Ephemeral** (Para que desaparezca después del uso)
   - **Tags:** Agrega `tag:exitnode`
4. Copia el Auth Key generado y **guárdalo como un secreto en GitHub**:
   - Ve a tu repositorio en GitHub → **Settings** → **Secrets and variables** → **Actions**.
   - Haz clic en **New repository secret**.
   - Nombre: `TAILSCALE_AUTHKEY`
   - Valor: Pega tu Auth Key

---

## 2️⃣ Configurar ACLs en Tailscale para Auto-Aprobar el Exit Node

1. Accede a la [consola de administración de Tailscale](https://login.tailscale.com/admin/acls).
2. Haz clic en **Edit policy**.
3. Modifica la configuración de ACL para incluir lo siguiente:

```jsonc
{
  "tagOwners": {
    "tag:exitnode": ["autogroup:admin"]
  },

  "AutoApprovers": {
    "exitNode": [ "tag:exitnode" ]
  },
  ...
}
