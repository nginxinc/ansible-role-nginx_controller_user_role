NGINX Controller User Role
==========================

Manage custom user roles with NGINX Controller.
The user role is how permissions are assigned to users.  The user role defines the objects / endpoints within NGINX Controller that the user can interact with and what they can do with those objects.
NGINX Controller includes three built-in Roles that cannot be modified: admin, user, and guest.

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`controller.fqdn` - FQDN of the NGINX Controller instance

`controller.auth_token` - Authentication token for NGINX Controller

`nginx_controller_user_role.metadata.name` -  name of the Role

### Template Variables

This role has multiple template related variables. The descriptions and defaults for all these variables can be found in **[vars/main.yml](./vars/main.yml)**

Dependencies
------------

Example Playbook
----------------

To use this role you can create a playbook such as the following (let's name it `nginx_controller_user_role.yaml` for the purposes of this example).

```yaml
- hosts: localhost
  gather_facts: no

  vars:
    nginx_controller_user_email: "user@example.com" # Required by nginx_controller_generate_token role
    nginx_controller_user_password: "mySecurePassword" # Required by nginx_controller_generate_token role
    nginx_controller_fqdn: "controller.mydomain.com"
    nginx_controller_validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx_controller_generate_token

    - name: Configure the user role
      include_role:
        name: nginxinc.nginx_controller_user_role
      vars:
        nginx_controller_user:
          metadata:
            name:  # the name of the user role
            displayName:   # a friendly display name for the user role (spaces and special characters allowed)
            description:  # a description of the user role
            tags:   # an array of tags
            - tagOne
            - tagTwo
          desiredState:
            permissions:  # an array of endpoints, objects, and the level of access
            -
              access: "READ"
              path: "/services/environments/development"
            -
              access: "WRITE"
              path: "/services/environments/test"
```

You can then run `ansible-playbook nginx_controller_user_role.yaml` to execute the playbook.

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_user_role.yaml -e "nginx_controller_user_email=user@company.com nginx_controller_user_password=notsecure nginx_controller_fqdn=controller.example.local nginx_controller_validate_certs=false"`

You can also pass/override any variables by passing a `yaml` file containing any number of variables like so `ansible-playbook nginx_controller_user_role.yaml -e "@nginx_controller_user_role_vars.yaml"`

License
-------

[Apache License, Version 2.0](./LICENSE)

Author Information
------------------

[Brian Ehlert](https://github.com/brianehlert)

&copy; [NGINX, Inc.](https://www.nginx.com/) 2020
