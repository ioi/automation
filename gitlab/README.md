# GitLab

This playbook deploys [Omnibus GitLab](https://docs.gitlab.com/omnibus/).

## Deployment

1. Provision a VM.
1. Add the (floating) IP to the `[gitlab]` group in `env/hosts.ini`.
1. Set up A record domain pointing to the VM IP. Put the domain name in `gitlab_fqdn` in `env/vars.yml`.

1. Deploy:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml gitlab/deploy.yml

   (This could take some time.)

1. Open GitLab at https://{{gitlab_fqdn}}.

1. Get the `root` password in `/etc/gitlab/initial_root_password`, and save it somewhere. Verify that you can log in using the `root` username and the password.

1. Turn off public registration when asked in the top banner.

## Deploying runner

This assumes the runner is in the same host as GitLab.

1. Open https://{{gitlab_fqdn}}/admin/runners.
1. Click "Register an instance runner", view the registration token, put it in `gitlab_runner_registration_token` in `env/vars.yml`.

1. Deploy:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml gitlab/deploy_runner.yml

1. Verify that there is a new online runner on https://{{gitlab_fqdn}}/admin/runners.

## Setting up Nginx basic auth

See: https://stackoverflow.com/a/29414687.

// TODO: automate this step in the playbook.

## Setting up email notification

See: https://docs.gitlab.com/omnibus/settings/smtp.html.

// TODO: automate this step in the playbook.
