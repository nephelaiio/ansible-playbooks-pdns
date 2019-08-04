# nephelaiio.playbooks-pdns

[![Build Status](https://travis-ci.org/nephelaiio/ansible-playbooks-pdns.svg?branch=master)](https://travis-ci.org/nephelaiio/ansible-playbooks-pdns)

A set of ansible playbooks to install and configure [PowerDNS](https://www.powerdns.com/).

## Playbook descriptions

The following lists the group targets and descriptions for every playbook

| playbook     | description                                                                   | target         |
| ---          | ---                                                                           | ---            |
| recursor.yml | install and configure [pdns recursor](https://www.powerdns.com/recursor.html) | pdns_recursors |
| server.yml   | install and configure [pdns server](https://www.powerdns.com/auth.html)       | pdns_servers   |
| client.yml   | configure resolvers for recursor clients                                      | pdns_clients   |

## Playbook variables

The following parameters are available/required for playbook invocation

### [recursor.yml](recursor.yml):
No variables should be overloading. You can still review the vars section in the [playbook definition](recursor.yml) if you wish to tweak playbook internal behavior

### [server.yml](nginx.yml):
| required | variable                              | description                                                  | default                                |
| ---      | ---                                   | ---                                                          | ---                                    |
| no       | pdns_url                              | pdns management url                                          | 'https://{{ ansible_fqdn }}'           |
| no       | pdns_recursors                        | list of pdns recursors to register zones with                | []                                     |
| no       | pdns_domains                          | list of domain names to add to the server                    | []                                     |
| no       | pdns_master                           | whether to configure the server as master or slave           | no                                     |
| no       | pdns_master_ips                       | ips for pdns masters (only used if pdns_master is false) (*) | []                                     |
| no       | pdns_psql_db                          | pdns postgresql database                                     | pdns                                   |
| no       | pdns_psql_user                        | pdns postgresql user                                         | pdns                                   |
| *yes*    | pdns_psql_pass                        | pdns postgresql password                                     | n/a                                    |
| *yes*    | pdns_api_key                          | pdns api key                                                 | n/a                                    |
| no       | pdns_soa_master                       | default zone soa master                                      | '{{ ansible_fqdn }}'                   |
| no       | pdns_soa_ttl                          | default zone soa ttl                                         | 3600                                   |
| *yes*    | acme_certificate_aws_accesskey_id     | an ec2 key id with route53 management rights                 | lookup('env', 'AWS_ACCESS_KEY_ID')     |
| *yes*    | acme_certificate_aws_accesskey_secret | an ec2 key secret                                            | lookup('env', 'AWS_SECRET_ACCESS_KEY') |

(*): hint for pdns_master_ips:
```
pdns_master_ips: "{{ hostvars.values() | selectattr('pdns_master', 'defined') | selectattr('pdns_master', 'equalto', True) | list }}"
```

### [records.yml](nginx.yml):
| required | variable                              | description                                  | default                                |
| ---      | ---                                   | ---                                          | ---                                    |
| *yes*    | awx_url                               | target awx url                               | n/a                                    |

### [client.yml](configure.yml):
| required | variable      | description                                  | default |
| *yes*    | awx_url       | target awx url                               | n/a     |
| *no*     | awx_users     | [list of awx users](#Users)                  | []      |
| *no*     | awx_schedules | [list of awx template schedules](#Schedules) | []      |
| *no*     | awx_templates | [list of awx template](#Templates)           | []      |
| *no*     | awx_organizations | [list of awx template](#Organizations) | []      |

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
```

## Testin (TODO)

Please make sure your environment has [docker](https://www.docker.com) installed in order to run role validation tests. Additional python dependencies are listed in the [requirements](https://raw.githubusercontent.com/nephelaiio/ansible-role-requirements/master/requirements.txt)

This role is tested automatically against the following distributions (docker images):

  * Ubuntu Bionic
  * Ubuntu Xenial

You can test the role directly from sources using command ` molecule test `

## License

This project is licensed under the terms of the [MIT License](/LICENSE)
