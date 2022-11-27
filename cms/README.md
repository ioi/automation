# CMS

This playbook deploys [ioi/cms](https://github.com/ioi/cms).

## Service <-> Instance Mapping

| 10.0.0.31 | 10.0.0.50 | 10.0.0.11~1x | 10.0.0.101~1xx | 10.0.0.221 |
| --- | --- | --- | --- | --- |
| `[nginx_contest]`<br>Nginx for<br>AdminWebService &<br>ContestWebService | `[cms_db]`<br>PostgreSQL | `[cms_contest_web]`<br>ContestWebService | `[cms_worker]`<br>WorkerService | `[nginx_public]`<br>Nginx for<br>RankingWebService
| `[cms_admin_web]`<br>AdminWebService |||| `[cms_ranking]`<br>RankingWebService
| `[cms_scoring]`<br>ScoringService
| `[cms_checker]`<br>CheckerService
| `[cms_evaluation]`<br>EvaluationService
| `[cms_proxy]`<br>ProxyService
| `[cms_log]`<br>LogService

## Deployment

1. Provision VMs as specified in the mapping table above, updating `env/hosts.ini` accordingly.
1. Fill the CMS configs in `env/vars.yml`. Several notes:
   - `cms_worker_host_slots`: the number of (static) IPs should be equal to the maximum number of workers you want to deploy during a contest. See also "Worker management" section below.

1. Deploy:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml cms/deploy.yml

1. Verify that the following services can be accessed:
   - Contest web service (`https://cms.[domain]`)
   - Admin web service (`https://admin.[domain]`)
   - Ranking web service (`https://ranking.[domain]`)


## Adding admins

Modify `env/data/admins.csv`, then run:

```
ansible-playbook -i env/hosts.ini -e @env/vars.yml cms/add_admins.yml
```

## Adding teams

Modify `env/data/teams.csv`, then run:

```
ansible-playbook -i env/hosts.ini -e @env/vars.yml cms/add_teams.yml
```

## Adding users

Modify `env/data/users.csv`, then run:

```
ansible-playbook -i env/hosts.ini -e @env/vars.yml cms/add_users.yml
```

## Creating contests

1. Open admin web service.
1. Create the contest.

## Activating contests

By default, contest web service will show a dropdown of all contests. Contestants need to select one of the contests to enter.

To force the contestants to enter one of the contests:

1. Obtain the contest id (integer) from contest URL in the admin web service.
1. Run:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml -e contest_id=<ID> cms/deploy.yml --tags=services 


1. Open contest web service, and verify that the contest is automatically selected.

**Notes**

- REMEMBER to always add `-e contest_id=<ID>` when running ansible playbooks from this point, otherwise the contest will be unset.
   - Alternatively, `contest_id` can also be set in `env/vars.yml`.

- To revert this behavior (show the dropdown again), set `contest_id` as `ALL` (or leave it unspecified), and run the above playbook again.

## Adding participations for a contest

Run:

```
ansible-playbook -i env/hosts.ini -e @env/vars.yml -e contest_id=<ID> cms/add_participations.yml
```

## Adding statements to tasks

1. Rsync files from Translation app's `/opt/translation/final_pdf/` to `env/data/final_pdf/`.
1. (IMPORTANT) For each task, check for possibly multiple files for a language! This could happen when there was a race condition in saving the versions. In such cases, take the latest one and remove the duplicates. It could be the case that the latest one has some hash appended to the filename -- please remove it.
1. Run:

       ansible-playbook -i env/hosts.ini -e @env/vars.yml cms/add_statements.yml 

## Configuring ranking web service assets

- Upload logo to `/var/local/lib/cms/ranking/logo.png`.
- Upload user faces to `/var/local/lib/cms/ranking/faces/`.

## Worker management

To deploy workers after the other CMS services have been deployed, run:

```
ansible-playbook -i env/hosts.ini -e @env/vars.yml -e contest_id=<ID> cms/deploy.yml --limit=cms_worker
```

## Viewing logs

On a `cms_log` instance, run:

```
sudo journalctl -u cms-log -f
```

(If somehow it does not output anything -- try restarting journald via `sudo service systemd-journald restart`.)

Or, to view latest per-service CMS logs, open `/var/local/log/cms/<service_name>-<shard>/last.log`.
