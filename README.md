# centos_apache

This role installs and configures Apache HTTP Server on Enterprise Linux and Fedora.

License: MIT.

## Requirements

- ansible-core 2.17 or later.
- A target host from the platform table below.
- Privilege escalation. Set `become: true` in the playbook that uses this role.

## Platforms

| Platform | Versions |
| -------- | -------- |
| Rocky Linux | 9, 10 |
| AlmaLinux | 9, 10 |
| CentOS Stream | 9, 10 |
| Fedora | 43, 44, 45 |

Fedora 43 support ends on 2026-12-01. Remove that release from your inventory before that date.

## Role Variables

`meta/argument_specs.yml` defines these variables. Ansible checks the type of each variable at role entry. `defaults/main.yml` holds the same defaults.

| Variable | Default | Description |
| -------- | ------- | ----------- |
| `apache_start_servers` | `4` | Initial number of server processes to spawn on service start. |
| `apache_min_spare_servers` | `20` | Minimum number of idle worker processes. |
| `apache_max_spare_servers` | `40` | Maximum number of idle worker processes to retain after a traffic spike. |
| `apache_max_request_workers` | `200` | Maximum number of simultaneous connections. |
| `apache_max_connections_per_child` | `4500` | Maximum number of requests a worker process handles before it is recycled. Apache treats `0` as "no limit". |

The role writes these values to `/etc/httpd/conf.d/custom.conf`. The defaults suit a low-resource server.

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

## Testing

Tests use Molecule with the Podman driver. Install the test dependencies first:

```bash
pip install -r requirements.txt
```

Run the full test sequence:

```bash
molecule test
```

You need Podman on the control host and Python 3.13+ for the test venv (ansible-core 2.22 dropped 3.12 and is required to manage Fedora 45, whose images ship Python 3.14). The test creates one container for each of the nine platforms in the table above. The CentOS Stream and Fedora base images ship without systemd, so molecule builds a small layer on them (`Dockerfile.j2`) that installs systemd and python3; the Rocky and Alma images run pre-built. Molecule converges the role on every container, checks idempotence, and then runs the verify stage. The verify stage checks that httpd runs, that `/etc/httpd/conf.d/custom.conf` holds the tuned directives, that the default welcome and userdir files are absent, and that `http://localhost/` answers 200.

## License

MIT. See [LICENSE](LICENSE).

## Author

Mariusz Czyz
