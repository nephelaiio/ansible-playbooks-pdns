# nephelaiio.playbooks-pdns

[![Build Status](https://travis-ci.org/nephelaiio/ansible-playbooks-pdns.svg?branch=master)](https://travis-ci.org/nephelaiio/ansible-playbooks-pdns)

A set of ansible playbooks to install and configure [PowerDNS](https://www.powerdns.com/).

## Playbook descriptions

The following lists the group targets and descriptions for every playbook

| playbook     | description                                                                   | target         |
| ---          | ---                                                                           | ---            |
| recursor.yml | install and configure [pdns recursor](https://www.powerdns.com/recursor.html) | pdns_recursors |
| server.yml   | install and configure [pdns server](https://www.powerdns.com/auth.html)       | pdns_servers   |
| records.yml  | configure standalone dns records                                              | pdns_servers |
| register.yml | create host records                                                           | pdns_register | default('all') |

## Playbook variables

The following parameters are available/required for playbook invocation

### [recursor.yml](recursor.yml):
| required | variable                   | description                 | default                           |
| ---      | ---                        | ---                         | ---                               |
| no       | pdns_recursor_host_entries | host entry overrides        | []                                |
| no       | pdns_recursor_config       | pdns recursor configuration | see [playbook vars](recursor.yml) |
|          |                            |                             |                                   |

### [server.yml](nginx.yml):
| required | variable                              | description                                                | default                                |
| ---      | ---                                   | ---                                                        | ---                                    |
| *yes*    | pdns_url                              | pdns management url                                        | 'https://{{ ansible_fqdn }}'           |
| no       | pdns_recursors                        | list of hosnames for pdns recursors to register zones with | []                                     |
| *yes*    | pdns_zones                            | list of zones to add to the server                         | []                                     |
| no       | pdns_master                           | whether to configure the server as master or slave         | no                                     |
| no       | pdns_master_ips                       | ips for pdns masters (only used if pdns_master is false)   | (*)                                    |
| no       | pdns_psql_db                          | pdns postgresql database                                   | pdns                                   |
| no       | pdns_psql_user                        | pdns postgresql user                                       | pdns                                   |
| *yes*    | pdns_psql_pass                        | pdns postgresql password                                   | n/a                                    |
| *yes*    | pdns_api_key                          | pdns api key                                               | n/a                                    |
| no       | pdns_api_proto                        | pdns protocol for localhost access                         | http                                   |
| no       | pdns_api_port                         | pdns tcp port for localhost access                         | 8081                                   |
| no       | pdns_soa_master                       | default zone soa master                                    | '{{ ansible_fqdn }}'                   |
| no       | pdns_soa_ttl                          | default zone soa ttl                                       | 3600                                   |
| *yes*    | acme_certificate_aws_accesskey_id     | an ec2 key id with route53 management rights               | lookup('env', 'AWS_ACCESS_KEY_ID')     |
| *yes*    | acme_certificate_aws_accesskey_secret | an ec2 key secret                                          | lookup('env', 'AWS_SECRET_ACCESS_KEY') |

(*):
```
pdns_master_ips: "{{ play_hosts | selectattr('pdns_master', 'defined') | selectattr('pdns_master', 'equalto', True) | map(attribute='ansible_default_ipv4') | map(attribute='address') | list }}"
```

### [records.yml](records.yml):
| required | variable       | description                        | default |
| ---      | ---            | ---                                | ---     |
| *yes*    | pdns_api_key   | pdns api key                       | n/a     |


### [register.yml](register.yml):
| required | variable       | description                        | default |
| ---      | ---            | ---                                | ---     |
| *yes*    | pdns_api_key   | pdns api key                       | n/a     |

## Example inventory

### hosts.yml
```{yaml}
pdns_servers:
   hosts:
   resolv01.nephelai.io:
   resolv02.nephelai.io

pdns_servers:
  hosts:
    ns01.nephelai.io:
      pdns_master: yes
    ns02.nephelai.io:
  vars:
    pdns_url: https://pdns.nephelai.io
    pdns_zones:
      - name: nephelai.io
        ns:
          - ns1.nephelai.io
          - ns2.nephelai.io
     pdns_recursors: "{{ groups['pdns_servers'] }}"
```

## Dependencies

All playbooks have the following role dependencies

* [nephelaiio.plugins](https://galaxy.ansible.com/nephelaiio/plugins)
* [nephelaiio.docker](https://galaxy.ansible.com/nephelaiio/docker)
* [nephelaiio.pip](https://galaxy.ansible.com/nephelaiio/pip)
* [nephelaiio.acme_certificate_route53](https://galaxy.ansible.com/nephelaiio/acme_certificate_route53)

See the [requirements](https://raw.githubusercontent.com/nephelaiio/ansible-role-requirements/master/requirements.txt) and [meta](meta.yml) files for more details

## Example Invocation

```
git checkout https://galaxy.ansible.com/nephelaiio/ansible-playbooks-pdns pdns
ansible-playbook -i inventory/ pdns/recursor.yml
ansible-playbook -i inventory/ pdns/server.yml
```

## Testing (TODO)

Please make sure your environment has [docker](https://www.docker.com) installed in order to run role validation tests. Additional python dependencies are listed in the [requirements](https://raw.githubusercontent.com/nephelaiio/ansible-role-requirements/master/requirements.txt)

This role is tested automatically against the following distributions (docker images):

  * Ubuntu Bionic
  * Ubuntu Xenial

You can test the role directly from sources using command ` molecule test `

## License

This project is licensed under the terms of the [MIT License](/LICENSE)
