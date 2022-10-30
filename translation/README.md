# Translation App

This playbook deploys [ioi/translation](https://github.com/ioi/translation).

## Deployment

1. Provision a VM.
1. Add the (floating) IP to the `[translation]` group in `env/hosts.ini`.
1. Open `env/vars.yml`. Fill out the following config:
   - `translation_db`
   - `translation_db_user`
   - `translation_db_pass`
   - `translation_secret_key`

1. Set up A record domain pointing to the VM IP. Put the domain name in `translation_fqdn`.
1. If you want to set up Nginx basic auth, fill out the following config:
   - `translation_auth_basic_user`
   - `translation_auth_basic_pass`

1. Deploy:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml translation/deploy.yml

## Configuring default users

Open the app, log in as `admin` (default password: `admin`), and then change the passwords for the following auto-generated users:
  - `admin`
  - `staff`
  - `ISC`
