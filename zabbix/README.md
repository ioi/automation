# Zabbix Setup

## 1. Deploy server and frontend

Run the command below to deploy the server and front-end
```
ansible-playbook -i env/hosts.ini -e @env/vars.yml zabbix/deploy_zabbix_server.yml
```

In the Zabbix frontend, go to Configuration → Actions, select Autoregistration as the event source and click on Create action:

- In the Action tab, give your action a name
- In the Operation tab, add 2 operations:
-- `Add Host`
-- `Link To Template` → `Template OS Linux by Zabbix agent active`

Next, go to Administration → General → Autoregistration, change the setting to PSK and create a identity (any text), and generate a PSK using this command:

```
openssl rand -hex 32
```

Before deploying the agent, copy the PSK and identity generate before and paste it on zabbix_psk and zabbix_psk_identity respectively. Then, deploy the agent using this command:
```
ansible-playbook -i env/hosts.ini -e @env/vars.yml contestant-vm/deploy_zabbix_agent.yml
```
