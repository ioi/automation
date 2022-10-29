# Matrix

This playbook deploys [Matrix server](https://github.com/matrix-org/synapse).

## Deployment

1. Provision a VM.
1. Add the (floating) IP to the `[matrix]` group in `env/hosts.ini`.
1. Open `env/vars.yml`. Fill out the following config:
   - `matrix_db`
   - `matrix_db_user`
   - `matrix_db_pass`
   - `matrix_registration_shared_secret`

1. Set up A record domain pointing to the VM IP. Put the domain name in `matrix_fqdn`.
1. Deploy:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml matrix/deploy.yml

## Adding users

1. SSH to the VM.
1. Add a user by running:

       register_new_matrix_user -c /etc/matrix-synapse/homeserver.yaml -u <username> http://localhost:8008

## Connecting via client 

1. Open a Matrix client, e.g. https://app.element.io.
1. Change the homeserver to the value of `matrix_fqdn`.
1. Verify that we can log in using the user above.

## Public registration

Use this: https://github.com/zeratax/matrix-registration.

## Auto-join rooms

We can make newly-registered users (using the above public registration mechanism) to auto-join certain rooms, by setting the `auto_join_rooms` config in `/etc/matrix-synapse/homeserver.yaml`.
