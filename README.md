nextcloud-service
=========

Ansible role for deploying NextCloud to SysMango Dot Net Grid Docker Swarm.

Requirements
------------

The GRID reverse proxy needs to route inbound connections on port 80 to nextcloud.sysmango.net to port 80 on the service nextcloud:80.

Role Variables
--------------

No variables

Dependencies
------------

Local Dependancies:

> postgres
> redis
> reverseproxy (for access)

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
