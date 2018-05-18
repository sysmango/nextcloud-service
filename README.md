nextcloud-service
=========

Ansible role for deploying NextCloud to SysMango Dot Net Docker Swarm.

Requirements
------------

The GRID reverse proxy needs to route inbound connections on port 80 to nextcloud.sysmango.net to port 80 on the service nextcloud:80.  [Traefik](http://traefik.io) is currently being utilized as a reverse proxy and ingres controller.

Secrets are maaged via [HashiCrop Vault](http://vaultproject.io).

Role Variables
--------------

nextcloud_db_user
nextcloud_db_passwd
nextcould_db_connection
nextcloud_database
nextcloud_admin_user
nextcloud_admin_passwd
nextcloud_docker_image
nextcloud_docker_image_tag

Dependencies
------------

Local Dependancies:

* [PostgreSQL](http://postgresql.org/)
* [Redis](https://redis.io/)
* [Traefik](http://traefik.io/)
* [HashiCrop Vault](http://vaultproject.io/)

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
