nextcloud-service
=========

Ansible role for deploying NextCloud to K3s with NFS-backed storage and Traefik ingress.

Requirements
------------

* K3s cluster with Traefik v3 installed
* NFS server for persistent storage
* cert-manager for TLS certificates (optional)
* HashiCorp Vault for secrets management
* PostgreSQL database (can be deployed via database-service role)

Role Variables
--------------

### K3s Configuration
* `nextcloud_namespace` - Kubernetes namespace (default: nextcloud)
* `nextcloud_replicas` - Number of replicas (default: 1)
* `env` - Environment name for PV naming (default: prod)

### NextCloud Configuration
* `nextcloud_docker_image` - NextCloud container image (default: nextcloud)
* `nextcloud_docker_image_tag` - Image tag (default: 28.0.0)
* `nextcloud_hostname` - Hostname for ingress (default: nextcloud.sysmango.net)

### Database Configuration
* `nextcloud_db_user` - Database user (default: nextcloud)
* `nextcloud_db_passwd` - Database password (default: nextadmin)
* `nextcloud_db_connection` - Database connection string
* `nextcloud_database` - Database name (default: nextcloud)
* `create_nextcloud_db` - Create database and user (default: true)

### NFS Configuration (Nextcloud-isolated)
* `nc_nfs_server_host` - NFS server hostname/IP
* `nc_nfs_html_path` - NFS path for NextCloud HTML files
* `nc_nfs_config_path` - NFS path for NextCloud config
* `nc_nfs_data_path` - NFS path for NextCloud data
* `nc_nfs_custom_apps_path` - NFS path for custom apps

Additional options:
* `nc_nfs_dir_owner` (default: `"33"`)
* `nc_nfs_dir_group` (default: `"33"`)
* `nc_nfs_dir_mode` (default: `"0755"`)
* `nc_nfs_data_dir_mode` (default: `"0775"`)

Migration note: legacy `nfs_*` variables continue to work via defaults fallback, but are deprecated for this role. Prefer `nc_nfs_*` to avoid cross-role overrides.

### Vault Configuration
* `vault_addr` - Vault server URL
* `pg_vault_secret` - Vault secret path for PostgreSQL password
* `pg_password_from_vault` - Use Vault for DB password (default: true)

Dependencies
------------

* K3s cluster with Traefik v3
* NFS server
* PostgreSQL database
* HashiCorp Vault
* cert-manager (optional, for TLS)

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: nextcloud-service }

License
-------

BSD

Author Information
------------------

Butch Lebo <butch.lebo@sysmango.com>
http://sysmango.com/
