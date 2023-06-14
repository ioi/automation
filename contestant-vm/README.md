# Contestant VM

This directory contains all playbooks those will be executed on D-day


## Workflow

1. Pull vm logs

       ansible-playbook -i env/generated_data/vpn/vm_hosts.ini contestant-vm/pull_logs.yml
       ansible-playbook -i env/generated_data/vpn/vm_hosts_online.ini contestant-vm/pull_logs.yml
