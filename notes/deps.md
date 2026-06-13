# Homelab dependencies

Im folgenden die Abhängigkeiten der Services untereinander und die Reihenfolge in der sie installiert werden müssen.

### DNS

#### Grund

# Wird in terragrunt / tofu als Backend für die DNS-Zonen verwendet.

#### Ausführung

- Terragrunt
- Ansible

### Vault

#### Grund

- Wird in späteren Repositories in denen Ansible verwendet wird, als Secret Backend genutzt.
- Muss Ansible Inventory statisch bereitstellen weil Netbox noch nicht erstellt wurde.
- Netbox Anbindung noch nicht möglich wenn terragrunt läuft.

#### Ausführung

- Terragrunt
- Ansible

#### Vorbedingungen

- Terragrunt mise / fnox Variable 'TSIG_KEY_SECRET' muss händisch gesetzt werden.

#### Post Installation

- vault_role_id und vault_secret_id in 'group_vars/all.sops.yml' müssen nach der installation aktualisiert werden .

### Netbox

#### Grund

- Wird in allen anderen Repositories in denen Ansible verwendet wird, als Inventarquelle genutzt.
- Muss Ansible Inventory statisch bereitstellen weil Netbox hier erst erstellt wird.
- Ansible mit site-netbox.yml ausführen damit die Vault VM in Netbox eingetragen wird.
- Ansible mit site-netbox-init.yml ausführen damit initiale Daten in Netbox angelegt werden.

#### Ausführung

- Terragrunt
- Ansible

### DNS Second Run

#### Grund
- Benutzt Netbox dynamisches Inventory.
- Ansible zweites mal ausführen damit Secrets in Vault angelegt werden damit mise / fnox das secret TSIG_KEY_SECRET aus Vault beziehen kann.

#### Ausführung

- Ansible

### Authentik

#### Grund

- Benötigt Vault für die Bereitstellung von Secrets.
- Benötigt Netbox als Inventory Quelle.
- Benötigt DNS damit die Domain für Authentik angelegt ist.

#### Ausführung

- Terragrunt
- Ansible
