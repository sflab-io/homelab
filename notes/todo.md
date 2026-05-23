# TODO's

- mitte ende april grobkonzept
- dann in bu4
- technologiegespräch frühestens anfang mai
- gitlab, artifact store, software supply chain, security
- kosten noch nichts final, bit macht da mit und es wird überlegt ob 30/monat für gitlab sein muss

## CloudNativePG: Backup-Konfiguration einrichten

Der CloudNativePG Operator läuft im Cluster, aber die Backup-Konfiguration wurde noch nicht eingerichtet.

**Was zu tun ist:**
- S3-kompatiblen Object Store konfigurieren (z.B. MinIO oder AWS S3)
- `ScheduledBackup`-Ressource für automatische tägliche Backups erstellen
- `barmanObjectStore` in der `Cluster`-Spec konfigurieren (WAL-Archivierung + Basis-Backups)
- S3-Credentials als SOPS-verschlüsseltes Secret in `d1-infra-homelab/components/cloudnativepg/configs/base/` hinzufügen

**Dokumentation:**
- Blog-Post: https://oneuptime.com/blog/post/2026-03-13-deploy-cloudnativepg-operator-flux-cd/view (Step 5: Configure Scheduled Backups)
- CloudNativePG Backup-Dokumentation: https://cloudnative-pg.io/documentation/current/backup/
- Barman Object Store Konfiguration: https://cloudnative-pg.io/documentation/current/backup_barmanobjectstore/

**Kontext:**
- PostgreSQL-Cluster: `postgres-primary` im Namespace `databases`
- Konfigurationsdateien in: `d1-infra-homelab/components/cloudnativepg/configs/base/`
- SOPS AGE-Recipient: `age1595c6n7r3zu0aql8qeehlulds6g4cvavmphsyauk9uddks5hpuzqcg7c4u`

## Authentik: OIDC Provider für Grafana einrichten

Nachdem Authentik im Cluster installiert wurde, muss ein OIDC Provider für Grafana konfiguriert werden, damit sich Nutzer über Authentik bei Grafana anmelden können.

**Was zu tun ist:**
1. In der Authentik UI anmelden: `https://auth.k3s-homelab-production.home.sflab.io`
2. Unter **Anwendungen → Provider → Provider erstellen** einen neuen **OAuth2/OpenID Provider** anlegen:
   - **Name:** `grafana`
   - **Client Type:** `Confidential`
   - **Redirect URIs:** `https://grafana.k3s-homelab-production.home.sflab.io/login/generic_oauth`
   - **Scopes:** `openid`, `email`, `profile`
3. **Client ID** und **Client Secret** notieren
4. Unter **Anwendungen → Anwendungen** eine neue Anwendung erstellen und den Provider zuweisen
5. Die Grafana HelmRelease Values mit den OIDC-Daten aktualisieren:
   ```yaml
   grafana.ini:
     auth.generic_oauth:
       enabled: true
       name: Authentik
       client_id: <client-id>
       client_secret: <client-secret>
       scopes: openid email profile
       auth_url: https://auth.k3s-homelab-production.home.sflab.io/application/o/grafana/authorize/
       token_url: https://auth.k3s-homelab-production.home.sflab.io/application/o/token/
       api_url: https://auth.k3s-homelab-production.home.sflab.io/application/o/userinfo/
   ```

**Dokumentation:**
- Anleitung: https://oneuptime.com/blog/post/2026-03-13-deploy-authentik-identity-provider-with-flux-cd/view (Step 7: Create an OIDC Provider for Grafana)
- Authentik OAuth2/OIDC Provider Dokumentation: https://docs.goauthentik.io/docs/add-secure-apps/providers/oauth2/
- Authentik Grafana Integration: https://docs.goauthentik.io/integrations/services/grafana/

**Kontext:**
- Authentik läuft im Namespace `authentik`
- Authentik URL: `https://auth.k3s-homelab-production.home.sflab.io`
- Grafana ist noch nicht installiert — dieser Schritt ist erst nach Grafana-Installation relevant
