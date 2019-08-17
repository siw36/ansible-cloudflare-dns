# Ansible role: Cloudflare DNS  


# This repo is archived. Please use the ansible module for cloudflare DNS records: https://docs.ansible.com/ansible/latest/modules/cloudflare_dns_module.html

![Ansible Role](https://img.shields.io/ansible/role/40608.svg?color=blue) ![Ansible Quality Score](https://img.shields.io/ansible/quality/40608.svg?color=blue) ![Ansible Role](https://img.shields.io/ansible/role/d/40608.svg?color=blue)

<img src="https://www.cloudflare.com/img/logo-cloudflare-dark.svg" alt="cloudflare_logo" height="50"/>

This role manages Cloudlare DNS records.  

With this role you can [create|update|delete] Cloudflare DNS records ans zones.  
All DNS zones and records can be managed by a variable file which allows you to picture your project's DNS setup as "infrastructure as code".  

This role works for paid AND free Cloudflare plans.  

__Configuration of variables is mandatory for this role to work!__ (See below to find out what vars needs to be set in what format)  

Example use case:  
You can easily include this role in any server provisioning routine (cloud, etc.) to automatically set up DNS records for new machines.  

__Official Ansible Galaxy entry:__ https://galaxy.ansible.com/siw36/ansible_cloudflare_dns

## Requirements
- Ansible
- Cloudflare account and access token

## Vars
### Required to be customized  
#### File: __vars/main.yml__  

Zone variables:  
`zones`: list of DNS zones that should be created, deleted or updated  
`name`: name of the DNS zone that should be created, deleted or updated  
`state`: state of the zone. Valid values: [`present`|`absent` ]  
`ssl`: type of SSL encryption between client <-> cloudflare <-> the target server. Valid values: [`off`|`flexible`|`full`|`strict`]  
`records`: list of DNS records in this zone that should be created, deleted or updated  

Record variables:  
`name`: name of the DNS record without the zone name (host0.example.com -> host0)  
`value`: the value that record should be resolving to  
`type`: type of the DNS record. Valid values: [`A`|`AAAA`|`CNAME`|`TXT`|`SRV`|`LOC`|`MX`|`NS`|`SPF`|`CERT`|`DNSKEY`|`DS`|`NAPTR`|`SMIMEA`|`SSHFP`|`TLSA`|`URI`]  
`proxied`: set if the records should be proxied through Cloudflare's servers or should resolve directly to your server. Valid values: [`true`|`false`]  
`ttl`: time to live of the DNS record in seconds. Must be between 120 and 2,147,483,647 seconds, or 1 for automatic. Default is `120`  
`priority`: the priority of the DNS record. Default is `10`  
`state`: the state of the DNS record. Valid values: [`present`|`absent`]  

Vault variables:  
These variables get set from the vault file (see below the example).  
`cfAPIKey`  
`cfEmail`  
`cfAccountID`  
`cfAccountName`  

Example:  
```yaml
zones:
  - name: example.com
    state: present
    records:

      - name: "*"
        value: 1.1.1.1
        type: A
        proxied: false
        ttl: 120
        priority: 10
        state: present

      - name: server0
        value: 2.2.2.2
        type: A
        proxied: true
        ttl: 3600
        priority: 10
        state: present

      - name: server0
        value: Hello Galaxy
        type: TXT
        proxied: true
        ttl: 3600
        priority: 10
        state: present
```

#### File: __vars/vault.yml__  

Cloudflare account credentials:  
`vault_cfAPIKey`: can be obtained in your Cloudflare account "My Profile" -> "API Keys"  
`vault_cfEmail`: can be obtained in your Cloudflare account "My Profile" -> "API Keys"  
`vault_cfAccountID`: Can be obtained after login to `https://dash.cloudflare.com` from the zone overview "Account ID"  
`vault_cfAccountName`: the zone name

The vault vars file gets automatically included at the start of the play.  

I strongly recommend to encrypt this vault file before pushing it to your git repository:  
```shell
ansible-vault encrypt vars/vault.yml
```

Example:  

```yaml
vault_cfAPIKey: xuhas87tx8gax7wtag7x3w8zqh3d08392g93
vault_cfEmail: root@example.com
vault_cfAccountID: jbd913272gd9b397pd273ghb2u9pdgb
vault_cfAccountName: "example.com"
```

## Example Playbook  
```yaml
---
- name: Configure Cloudflare DNS
  hosts: localhost
  gather_facts: false
  become: false
  roles:
    - siw36.ansible_cloudflare_dns
```

## License
GNU General Public License v3.0  

## Author information
Created by Robin Klussmann (05/2019)
