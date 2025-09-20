# NextCloud Helm Deployment

This role provides a Helm-based NextCloud deployment following the Virtono tutorial approach, adapted for SysMango infrastructure.

## Overview

The Helm-based deployment offers several advantages over the traditional Ansible approach:

- **Simplified Management**: Uses the official NextCloud Helm chart
- **Better Upgrades**: Helm handles application upgrades more gracefully
- **Community Support**: Benefits from the NextCloud community's Helm chart maintenance
- **Reduced Complexity**: Less custom YAML templates to maintain

## Key Features

- **Vault Integration**: Automatically fetches secrets from Vault
- **NFS Storage**: Uses existing NFS-backed PersistentVolumes
- **CA Certificates**: Integrates SysMango CA certificates via init containers
- **Traefik Integration**: Creates IngressRoute for external access
- **PostgreSQL**: Connects to external PostgreSQL database

## Usage

### Deploy NextCloud with Helm

To use the Helm-based deployment, update your playbook to use the new task file:

```yaml
- name: Deploy NextCloud via Helm
  ansible.builtin.include_role:
    name: nextcloud-service
    tasks_from: main-helm
```

### Cleanup Helm Deployment

To remove the Helm-based deployment:

```yaml
- name: Cleanup NextCloud Helm deployment
  ansible.builtin.include_role:
    name: nextcloud-service
    tasks_from: main-helm
  vars:
    cleanup_mode: true
```

Or use tags:

```bash
ansible-playbook site.yml --tags cleanup
```

## Configuration

The Helm deployment uses the same variables as the traditional deployment:

### Required Variables

- `nextcloud_hostname`: Domain name for NextCloud
- `nc_nfs_server_host`: NFS server hostname/IP
- `vault_addr`: Vault server URL
- `nextcloud_vault_secret`: Vault secret path for NextCloud credentials
- `pg_vault_secret`: Vault secret path for PostgreSQL credentials

### Optional Variables

- `nextcloud_namespace`: Kubernetes namespace (default: `nextcloud-prod`)
- `nextcloud_replicas`: Number of replicas (default: `1`)
- `enable_ca_certificates`: Enable CA certificate integration (default: `true`)
- `create_nextcloud_db`: Create database and user (default: `true`)
- `nc_nfs_html_path`, `nc_nfs_config_path`, `nc_nfs_data_path`, `nc_nfs_custom_apps_path`
- `nc_nfs_dir_owner` (default: `"33"`), `nc_nfs_dir_group` (default: `"33"`)
- `nc_nfs_dir_mode` (default: `"0755"`), `nc_nfs_data_dir_mode` (default: `"0775"`)

Migration note: legacy `nfs_*` variables are still honored via defaults for backward compatibility, but are deprecated in favor of `nc_nfs_*` to prevent cross-role overrides.

## Deployment Process

The Helm deployment follows these steps:

1. **Preflight Checks**: Validates Helm, CRDs, and CA certificates
2. **Vault Integration**: Fetches secrets from Vault
3. **Database Setup**: Creates PostgreSQL database and user (if enabled)
4. **Storage Setup**: Creates NFS directories and PVs/PVCs
5. **Helm Repository**: Adds and updates NextCloud Helm repository
6. **Secrets Creation**: Creates Kubernetes secrets for database and admin credentials
7. **Helm Deployment**: Deploys NextCloud using official Helm chart
8. **Ingress Setup**: Creates Traefik IngressRoute
9. **Validation**: Waits for deployment to be ready

## Files Structure

```
roles/nextcloud-service/
├── tasks/
│   ├── main.yml                    # Original Ansible deployment
│   ├── main-helm.yml              # New Helm deployment entry point
│   ├── helm-deployment.yml        # Helm deployment tasks
│   ├── helm-cleanup.yml           # Helm cleanup tasks
│   └── preflight.yml              # Shared preflight checks
├── templates/
│   ├── nextcloud-helm-values.yaml.j2  # Helm values template
│   └── [existing templates]       # Original templates (still used for PVs/IngressRoute)
├── files/
│   └── nextcloud-helm-values.yaml # Static Helm values reference
└── README-helm.md                 # This documentation
```

## Comparison: Ansible vs Helm

| Aspect | Ansible Deployment | Helm Deployment |
|--------|-------------------|-----------------|
| **Complexity** | High (custom templates) | Medium (Helm values) |
| **Upgrades** | Manual template updates | `helm upgrade` |
| **Community** | Custom maintenance | Community maintained |
| **Rollbacks** | Manual | `helm rollback` |
| **Configuration** | Ansible variables | Helm values + secrets |
| **CA Certificates** | Custom init container | Integrated via extraInitContainers |

## Migration from Ansible to Helm

To migrate from the existing Ansible deployment to Helm:

1. **Backup Data**: Ensure NFS data is backed up
2. **Cleanup Old**: Run cleanup on existing deployment
3. **Deploy Helm**: Use `main-helm.yml` task file
4. **Verify**: Check that all data and configurations are preserved

## Troubleshooting

### Check Helm Release Status
```bash
helm list -n nextcloud-prod
helm status nextcloud -n nextcloud-prod
```

### View Helm Values
```bash
helm get values nextcloud -n nextcloud-prod
```

### Debug Pod Issues
```bash
kubectl logs -n nextcloud-prod deployment/nextcloud
kubectl describe pod -n nextcloud-prod -l app.kubernetes.io/name=nextcloud
```

### CA Certificate Issues
If CA certificates aren't working:
1. Check if the CA ConfigMap exists: `kubectl get configmap sysmango-ca-certificates -n nextcloud-prod`
2. Verify init container logs: `kubectl logs -n nextcloud-prod -l app.kubernetes.io/name=nextcloud -c ca-certificates-setup`

## Benefits of Helm Approach

1. **Standardization**: Uses official NextCloud Helm chart
2. **Maintainability**: Reduces custom template maintenance
3. **Upgrades**: Easier application upgrades via Helm
4. **Rollbacks**: Built-in rollback capabilities
5. **Community**: Benefits from community contributions and bug fixes
6. **Documentation**: Better documented via Helm chart documentation
