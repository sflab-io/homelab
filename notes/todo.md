# TODO's

---

Nachdem vault installiert wurde und die pki eingerichtet wurde zertifikate root ca und intermediate ca mit ansible als dateien in packer repository schreiben damit beim nächsten packer build die zertifikate in proxmox vms installiert werden können.
bisher händischer vorgang.

---

die vault vm wird nicht in netbox eingetragen in terragrunt weil vault gestartet wird bevor der netbox service zur verfügung steht. deswegen kann die vault vm frühesten im ansible lauf von netbox_init erfolgen.
netbox init static data anpassen und das eintragenb der vault vm dort abbilden.