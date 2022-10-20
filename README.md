# Overview
This role does configures daily backups of FreeIPA data.
This role requires root permissions. It must be called as root. This needs to be managed at the ansible or playbook level.

>Backups are stored in {{ backup_dir }}/{{ ansible_fqdn }}/freeipa/date

>Use `ipa-restore` to restore those backups. Make sure to set the appropriate options.

>Webhook notifications only support rocketchat for the moment.

# Variables

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| freeipa_backup_retention | string | no | `30` | The default number of backups to keep. |
| freeipa_backup_periodicity | string | no | `OnCalendar=*-*-* 22:00:00` | The default periodicity of backups (every night at 10pm). Systemd timer format. |
| freeipa_backup_mount_type | string | no | `local` | Type of storage that will hold the backup files. Supported types: local, nfs |
| freeipa_backup_dir | string | no | `/tmp/freeipa_backup` | Path where the backups are sent. Is the mount point in case of network storage. |
| freeipa_backup_remote_mount_path | string | no | `nfsserver:/path/to/mount` | The remote path of the mount command. Depends on the protocol. |
| freeipa_backup_options | string | no | `""` | Options to pass to `ipa-backup`, ex: "--data --online" |
| freeipa_backup_webhook_notification | boolean | no | `false` | Send the result of the backup at the end of execution |
| freeipa_backup_webhook_hostname | string | no | n.a. | The hostname to send the payload to |
| freeipa_backup_webhook_uri | string | no | n.a. | The uri to send the payload to |
| freeipa_backup_webhook_script | string | no | `/usr/local/bin/webhook-notify.sh`| The path of the webhook script to call (the default value is set for infra_monkey.webhook_notify galaxy role) |
| freeipa_backup_notify_failure_only | boolean | no | `true` | Sending a notification only on failure. |


# Example of inventory variables

    freeipa_backup_mount_type: "nfs"
    freeipa_backup_dir: "/mnt/backup"
    freeipa_backup_remote_mount_path: "192.168.25.2:/exports/backups"
    freeipa_backup_periodicity: "OnCalendar=*-*-* 1:00:00"


# Reqirements

This role uses the collection based ansible modules which requires:
- ansible >= 2.10
This role depends on the ansible collections:
- ansible.builtin
- ansible.posix

Default webhook script from ansible galaxy (you can use your own script):
- infra_monkey.webhook_notify

The role configures **systemd timers**, if your host doesn't use systemd, it will fail.

# Automatique Testing

This role is tested using Molecule against:
- Python 3.7, 3.8 and 3.9
- CentOS 7/8/9
- Debian 9/10/11