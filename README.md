# centos_apache

An Ansible role to install and configure Apache web server on CentOS 9, CentOS Stream 10, and Fedora 43.

Includes performance tuning for the prefork MPM via configurable role variables.

```conf
KeepAlive Off

<IfModule mpm_prefork_module>
    StartServers           4
    MinSpareServers        20
    MaxSpareServers        40
    MaxRequestWorkers      200
    MaxConnectionsPerChild 4500
</IfModule>
```

## Requirements

- Ansible >= 2.14
- Target hosts must be running CentOS 9, CentOS Stream 10, or Fedora 43
- The invoking playbook must set `become: true` or the role tasks will escalate privileges individually

## Role Variables

Override any of the following in `host_vars/`, `group_vars/`, or your playbook vars. The defaults
are defined in `defaults/main.yml` and are suitable for low-resource servers.

| Variable | Default | Description |
| -------- | ------- | ----------- |
| `apache_start_servers` | `4` | Initial number of worker processes spawned at startup |
| `apache_min_spare_servers` | `20` | Minimum number of idle worker processes to keep alive |
| `apache_max_spare_servers` | `40` | Maximum number of idle worker processes to retain after a traffic spike |
| `apache_max_request_workers` | `200` | Maximum number of simultaneous connections |
| `apache_max_connections_per_child` | `4500` | Requests a worker handles before being recycled |

## Dependencies

None.

## Example Playbook

```yaml
- hosts: webservers
  become: true

  roles:
    - role: mariuszczyz.centos_apache
```

Overriding variables:

```yaml
- hosts: webservers
  become: true
  vars:
    apache_max_request_workers: 400
    apache_max_connections_per_child: 10000

  roles:
    - role: mariuszczyz.centos_apache
```

## Installation

From Ansible Galaxy:

```bash
ansible-galaxy install mariuszczyz.centos_apache
```

Via `requirements.yml`:

```yaml
# Install from Ansible Galaxy
- src: mariuszczyz.centos_apache

# Or install from GitHub
- src: https://github.com/mariuszczyz/centos_apache
```

```bash
ansible-galaxy install -r requirements.yml
```

## License

BSD

## Author

Mariusz Czyz
