# Ansible Role: Fail2ban

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-fail2ban)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-fail2ban)](https://github.com/grzegorzfranus/ansible-role-fail2ban/releases)|[![CI](https://github.com/grzegorzfranus/ansible-role-fail2ban/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-fail2ban/actions/workflows/ci.yml)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs, configures, and manages **Fail2ban**, an intrusion prevention framework that protects systems from brute-force attacks and other malicious behavior. It works by monitoring log files for selected patterns and taking action when these patterns match malicious activity.

## Features

- **Package Management**: Install and upgrade Fail2ban package with EPEL support for EL-family systems
- **Daemon Configuration**: Full control over logging, socket, PID, and syslog settings
- **Jail Management**: Deploy core jail configuration and custom jail files with orphan cleanup
- **Ban Policies**: Progressive ban times, configurable thresholds, and IP whitelisting
- **Email Notifications**: Optional email alerts on ban/unban events
- **Custom Filters**: Included filters for OpenVPN and FreeIPA GUI
- **Logrotate**: Automated log rotation with OS-specific templates
- **NFTables Support**: Systemd override for nftables firewall backend
- **Comprehensive Validation**: Argument specs and runtime assertions for all variables

## Requirements

### Supported operating systems

List of officially supported operating systems for this role:

| OS Family | Version | Status |
|-----------|---------|--------|
| Ubuntu | 24.04 (Noble) | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |
| Rocky Linux | 9 | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |
| EL | 9 | ![supported](https://img.shields.io/badge/supported-brightgreen.svg) |

### Ansible version

Ansible >= 2.16

### Python version

Python >= 3.9

### Setup module

The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access

This role requires root access for package installation and service management. Each task explicitly defines `become: true` where privilege escalation is needed — there is no global `become: true` requirement.

## Quick Start

### 1. Basic Fail2ban Installation

```yaml
---
- name: Configure Fail2ban Protection
  hosts: servers
  roles:
    - role: grzegorzfranus.fail2ban
```

### 2. Custom Ban Policy

```yaml
---
- name: Configure Fail2ban with Custom Policy
  hosts: servers
  vars:
    fail2ban_ignoreip:
      - 127.0.0.1/8
      - ::1
      - 192.168.1.0/24
    fail2ban_bantime: "1h"
    fail2ban_findtime: "30m"
    fail2ban_maxretry: 3
  roles:
    - role: grzegorzfranus.fail2ban
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory fail2ban-setup.yml
```

## Configuration

### Default Configuration

The role comes with production-ready defaults:

```yaml
# Role behavior
fail2ban_role_action: "all"
fail2ban_service_enabled: true
fail2ban_configure_logrotate: true

# Ban settings
fail2ban_bantime: "10m"
fail2ban_findtime: "10m"
fail2ban_maxretry: 5
fail2ban_bantime_increment: true
```

## Variables

### General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_role_action` | Define which parts of the role to execute. Options: `all`, `install`, `configure`, `logrotate`, `custom_jails`, `upgrade`. Note: `all` excludes `upgrade` — use explicit `upgrade` action or `--tags upgrade` | `"all"` |
| `fail2ban_role_mode` | Define role mode for firewall backend (nftables/iptables) | `""` |
| `fail2ban_service_enabled` | Enable/disable Fail2ban service on boot | `true` |
| `fail2ban_configure_logrotate` | Enable/disable logrotate configuration for Fail2ban logs | `true` |
| `fail2ban_enable_epel` | Enable EPEL repo on EL-family systems (ignored on non-EL) | `true` |

### Logrotate Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_logrotate_options` | Dictionary of logrotate settings | See below |
| `fail2ban_logrotate_options.archive_directory_path` | Directory where archived logs will be stored | `"/var/log/fail2ban"` |
| `fail2ban_logrotate_options.frequency` | How often to rotate logs | `"daily"` |
| `fail2ban_logrotate_options.count` | Number of rotated log files to keep | `30` |
| `fail2ban_logrotate_options.missingok` | Don't error if log file is missing | `true` |
| `fail2ban_logrotate_options.compress` | Compress rotated logs using gzip | `true` |
| `fail2ban_logrotate_options.nocreate` | Don't create new empty log file | `false` |
| `fail2ban_logrotate_options.copytruncate` | Use copy+truncate instead of move | `false` |
| `fail2ban_logrotate_options.dateext` | Add date extension to rotated logs | `true` |

### Daemon Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_socket` | Socket file for daemon communication | `"/var/run/fail2ban/fail2ban.sock"` |
| `fail2ban_pidfile` | PID file for the daemon | `"/var/run/fail2ban/fail2ban.pid"` |
| `fail2ban_loglevel` | Log level (CRITICAL, ERROR, WARNING, NOTICE, INFO, DEBUG) | `"INFO"` |
| `fail2ban_logtarget` | Log target (file, SYSTEMD-JOURNAL, SYSLOG, STDERR, STDOUT) | `"/var/log/fail2ban.log"` |
| `fail2ban_syslog_target` | Syslog target for fail2ban | `"/var/log/fail2ban.log"` |
| `fail2ban_syslog_facility` | Syslog facility number | `1` |

### Jail Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_protocol` | Default protocol to use in jail definitions | `"tcp"` |
| `fail2ban_ignoreself` | Whether to ignore the local IP addresses (boolean) | `true` |
| `fail2ban_ignoreip` | List of IPs or CIDR ranges to never ban | `["127.0.0.1/8", "::1"]` |

### Ban Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_bantime` | Duration that a host is banned | `"10m"` |
| `fail2ban_findtime` | Time window to count failures | `"10m"` |
| `fail2ban_maxretry` | Number of failures before a host is banned | `5` |
| `fail2ban_bantime_increment` | Enable progressive ban time | `true` |
| `fail2ban_bantime_rndtime` | Random time to add to ban time | `"30m"` |
| `fail2ban_bantime_maxtime` | Maximum ban time | `"60d"` |
| `fail2ban_bantime_factor` | Multiplier for progressive ban calculation | `2` |
| `fail2ban_dbpurgeage` | Time after which to purge database entries | `"30d"` |

### Email Notification Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_email_notification_enabled` | Enable/disable email notifications | `false` |
| `fail2ban_destemail` | Destination email for notifications | `"root@localhost"` |
| `fail2ban_sender` | Sender email for notifications | `"root@{{ ansible_facts['fqdn'] }}"` |
| `fail2ban_mta` | Mail transport agent (sendmail, mail) | `"sendmail"` |

### Custom Jail Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_custom_jail_files` | List of custom jail configurations (each item requires `name` and `content` keys) | `[]` |
| `fail2ban_custom_jails_path` | Path to custom jail files | `"files/jail.d"` |
| `fail2ban_custom_actions_path` | Path to custom action files | `"files/action.d"` |
| `fail2ban_custom_filters_path` | Path to custom filter files | `"files/filter.d"` |

**Custom Jail Example:**
```yaml
fail2ban_custom_jail_files:
  - name: sshd-custom
    content: |
      [sshd-custom]
      enabled = true
      filter = sshd
      port = ssh
      logpath = /var/log/auth.log
      maxretry = 3
      bantime = 3600
  - name: apache-badbots
    content: |
      [apache-badbots]
      enabled = true
      filter = apache-badbots
      port = http,https
      logpath = /var/log/apache2/access.log
      maxretry = 2
```

### Internal Variables (Paths and Service/Package)

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_service_name` | System service name | `"fail2ban"` |
| `fail2ban_package_name` | Package name to install | `"fail2ban"` |
| `fail2ban_dir_config_path` | Base configuration directory | `"/etc/fail2ban"` |
| `fail2ban_log_directory_path` | Default log directory | `"/var/log/fail2ban"` |
| `fail2ban_logrotate_config_path` | Path to logrotate configuration file | `"/etc/logrotate.d/fail2ban"` |
| `fail2ban_default_log_path` | Default log file path used by templates | `"/var/log/fail2ban.log"` |

Note: These are internal role variables defined in `vars/main.yml`. Most users can keep the defaults.

## Role Properties

| Property | Value | Description |
|----------|-------|-------------|
| **Idempotent** | Yes | Running the role multiple times with the same parameters produces the same result. |
| **Atomic** | No | The role can be partially applied. A failure mid-execution may leave the system in an intermediate state. |
| **Check Mode** | Supported | Most tasks work in check mode. Mutating commands are skipped. |
| **Diff Mode** | Supported | Template tasks support diff mode for change preview. |

## Role Output

This role does not set any public output facts. All internal facts use the `__fail2ban_` double-underscore prefix and are not part of the public interface.

To inspect diagnostic data at runtime, run the playbook with `-v` or `-vv` verbosity flags — the role provides detailed debug output at `verbosity: 1`.

## Included Custom Filters

The role includes the following custom filters that can be used in your jail configurations:

### OpenVPN
- **Filter**: `files/filter.d/openvpn.conf`
- **Description**: Detects authentication failures in OpenVPN logs
- **Usage**: Add a custom jail for OpenVPN and use `filter = openvpn`

### FreeIPA GUI
- **Filter**: `files/filter.d/freeipa-gui.conf`
- **Description**: Detects unauthorized access attempts to the FreeIPA web interface
- **Usage**: Add a custom jail for FreeIPA GUI and use `filter = freeipa-gui`

## Tags

| Tag | Description |
|-----|-------------|
| `always` | Tasks that always run (variable loading and validation) |
| `setup` | Setup and configuration tasks |
| `init` | Initial environment setup and variable loading |
| `validate` | Variable validation and system checks |
| `install` | Package installation tasks |
| `configure` | Service and role configuration tasks |
| `logrotate` | Logrotate-specific configuration tasks |
| `never` | Never run unless explicitly called (used by upgrade task) |
| `upgrade` | Upgrade Fail2ban packages (explicit action required) |

> **Important:** The `upgrade` tag has `never` applied, meaning it will not run during normal execution. You must explicitly request it using `--tags upgrade` or set `fail2ban_role_action: "upgrade"`.

**Example with tags:**
```bash
# Install only
ansible-playbook playbook.yml --tags "install"

# Configure without installation
ansible-playbook playbook.yml --tags "configure"

# Upgrade Fail2ban to latest version (explicit action required)
ansible-playbook playbook.yml --tags "upgrade"

# Or use the role action variable
ansible-playbook playbook.yml -e "fail2ban_role_action=upgrade"
```

## Example Playbook

```yaml
---
- name: Configure Fail2ban Protection
  hosts: servers
  vars:
    # Enhanced ban settings
    fail2ban_bantime: "2h"
    fail2ban_findtime: "15m"
    fail2ban_maxretry: 3
    fail2ban_bantime_increment: true

    # Email notifications
    fail2ban_email_notification_enabled: true
    fail2ban_destemail: "admin@example.com"

    # Custom ignore list
    fail2ban_ignoreip:
      - 127.0.0.1/8
      - ::1
      - 192.168.1.0/24
      - 10.0.0.0/8

    # Custom jails
    fail2ban_custom_jail_files:
      - name: sshd-strict
        content: |
          [sshd-strict]
          enabled = true
          filter = sshd
          port = ssh
          logpath = /var/log/auth.log
          maxretry = 2
          bantime = 3600
          findtime = 300
      - name: openvpn
        content: |
          [openvpn]
          enabled = true
          filter = openvpn
          port = 1194
          logpath = /var/log/openvpn.log
          maxretry = 3
          bantime = 1800
  roles:
    - role: grzegorzfranus.fail2ban

# NFTables Integration
- name: Configure Fail2ban with NFTables
  hosts: firewalls
  vars:
    fail2ban_role_mode: "nftables"
    fail2ban_service_enabled: true
    fail2ban_configure_logrotate: true
  roles:
    - role: grzegorzfranus.fail2ban
```

## Validation Behavior

- After deploying templates, the role validates configuration with `fail2ban-client -t` and fails early if the syntax is invalid.
- The Fail2ban service is restarted only when enabled and after a successful configuration test.

## Security considerations

- Each task explicitly defines `become: true` where privilege escalation is needed. There is no global `become: true`.
- Configurable IP whitelisting via `fail2ban_ignoreip` prevents accidental self-lockout.
- Progressive ban times via `fail2ban_bantime_increment` handle repeat offenders.

## File Structure

```
ansible-role-fail2ban/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       ├── ci.yml           # CI pipeline (reusable ansible-ci.yml)
│       └── release.yml      # Release Please + Galaxy publish
├── .release-please-manifest.json # Release Please version manifest
├── release-please-config.json # Release Please configuration
├── CHANGELOG.md              # Version history and changes
├── LICENSE                   # Apache-2.0 license
├── README.md                # This documentation file
├── defaults/
│   └── main.yml             # Default configuration variables
├── files/                   # Static files for custom filters
│   ├── action.d/           # Custom action files
│   └── filter.d/           # Custom filter files
│       ├── freeipa-gui.conf # FreeIPA GUI filter
│       └── openvpn.conf    # OpenVPN filter
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   ├── main.yml             # Role metadata and Galaxy information
│   └── argument_specs.yml   # Ansible-native argument validation (CoP §3.1.20)
├── molecule/                # Molecule testing framework
│   └── default/            # Default test scenario
│       ├── molecule.yml    # Test configuration
│       ├── converge.yml    # Role execution playbook
│       ├── prepare.yml     # Test preparation tasks
│       └── verify.yml      # Verification tests
├── tasks/
│   ├── main.yml            # Main task orchestration
│   ├── assert.yml          # Runtime variable validation
│   ├── install.yml         # Package installation
│   ├── configure.yml       # Service configuration
│   ├── custom_jails.yml    # Custom jail management
│   ├── logrotate.yml       # Log rotation configuration
│   └── upgrade.yml         # Package upgrades
├── templates/              # Configuration templates
│   ├── fail2ban/          # Core Fail2ban configuration
│   │   ├── fail2ban.local.j2 # Main daemon configuration
│   │   └── jail.local.j2   # Jail configurations
│   ├── logrotate/         # Log rotation configurations
│   │   ├── debian/        # Debian-specific templates
│   │   └── redhat/        # RedHat-specific templates
│   └── systemd/           # Systemd service overrides
└── vars/
    └── main.yml            # Internal role variables
```

## Troubleshooting

### Service Issues

```bash
# Check service status
sudo systemctl status fail2ban

# Check configuration syntax
sudo fail2ban-client -t

# View recent logs
sudo journalctl -u fail2ban -n 50
```

### Jail Issues

```bash
# Test jail configuration
sudo fail2ban-client status

# Check specific jail
sudo fail2ban-client status sshd

# View banned IPs
sudo fail2ban-client get sshd banip
```

### Email Notification Issues

```bash
# Test email configuration
echo "Test" | mail -s "Test Subject" admin@example.com

# Check MTA logs
sudo journalctl -u postfix -n 20
```

### Testing

```bash
# Run molecule tests
molecule test

# Run ansible-lint
ansible-lint

# Run yamllint
yamllint .
```

## CI/CD Pipeline

### CI Pipeline

Runs on every Pull Request via centralized reusable workflow:

1. **Branch Name Lint** — enforces naming conventions (`feature/`, `bugfix/`, etc.)
2. **PR Title Lint** — enforces Conventional Commits naming conventions on PR titles
3. **YAML Lint** — validates all YAML files
4. **Ansible Lint** — enforces best practices and guidelines compliance
5. **Security Scan** — TruffleHog secret detection
6. **Molecule Tests** — matrix across Ubuntu 24.04, Ubuntu 22.04, Debian 12, Debian 11, and Rocky Linux 9
7. **Merge Check** — aggregated status gate for branch protection

### Release & Publish

Automated via [Release Please](https://github.com/googleapis/release-please):

1. Merge to `main` → Release Please creates a Release PR with changelog
2. Merge Release PR → creates Git tag + GitHub Release
3. Galaxy publish triggers automatically on release using centralized action

### Test Environment Features
- **Dynamic Platform Matrix**: Molecule automatically spins up containers matching the requested matrix distro.
- **Systemd Integration**: Proper container capabilities configured (`/sys/fs/cgroup`, privileged) to test Fail2ban's systemd services.

## Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages:
  - `feat:` — new features (minor version bump)
  - `fix:` — bug fixes (patch version bump)
  - `docs:` — documentation changes
  - `refactor:` — code refactoring
  - `test:` — test additions
  - `ci:` — CI/CD changes
  - `chore:` — maintenance tasks
- Use branch naming convention: `feature/`, `bugfix/`, `hotfix/`, `docs/`, `refactor/`, `test/`, `chore/`, `ci/`
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Submit a pull request describing your changes
- For major changes, please open an issue first to discuss what you would like to change

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.

## License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
