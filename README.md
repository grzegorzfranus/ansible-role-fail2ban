# 🛡️ Ansible Role: Fail2ban

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-fail2ban)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-fail2ban)](https://github.com/grzegorzfranus/ansible-role-fail2ban/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-fail2ban/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-fail2ban/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs, configures, and manages **Fail2ban**, an intrusion prevention framework that protects systems from brute-force attacks and other malicious behavior. It works by monitoring log files for selected patterns and taking action when these patterns match malicious activity.

## 🚀 Main Actions

- 📦 **Install** Fail2ban package and dependencies
- 🔧 **Configure** Fail2ban core settings  
- 🛠️ **Set up** ban policies and notification settings
- 🏗️ **Deploy** custom jail configurations
- 🔍 **Configure** custom filters for specialized services
- 📝 **Set up** logrotate for Fail2ban logs (optional)
- 🔄 **Upgrade** Fail2ban package (when requested)

## 📋 Requirements

### 🖥️ Supported Operating Systems
List of officially supported operating systems:

| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 22.04 (Jammy) | ✅ **Supported** |
| Debian | 12 (Bookworm) | ✅ **Supported** |
| Rocky Linux | 9 | ✅ **Supported** |
| EL | 9 | ✅ **Supported** |

### 🔧 Software Requirements

| Component | Version | Status |
|-----------|---------|---------|
| **Ansible** | >= 2.15 | ✅ **Required** |
| **Python** | >= 3.9 | ✅ **Required** |

### ⚠️ Setup Module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### 🔐 Root Access
This role requires root access, so either configure it in your inventory files, run it in a playbook with a global `become: true` or invoke the role in your playbook like:

```yaml
- hosts: servers
  become: true
  roles:
    - role: grzegorzfranus.fail2ban
```

## ⚙️ Role Variables

### 1. 🎯 General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_role_action` | Define which parts of the role to execute (Options: 'all', 'install', 'configure', 'logrotate', 'custom_jails', 'upgrade') | `"all"` |
| `fail2ban_role_mode` | Define role mode for firewall backend (nftables/iptables) | `""` |
| `fail2ban_service_enabled` | Enable/disable Fail2ban service on boot | `true` |
| `fail2ban_configure_logrotate` | Enable/disable logrotate configuration for Fail2ban logs | `true` |

### 2. 📝 Logrotate Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_logrotate_options` | Dictionary of logrotate settings | See below |
| `fail2ban_logrotate_options.archive_directory_path` | Directory where archived logs will be stored | `/var/log/fail2ban` |
| `fail2ban_logrotate_options.frequency` | How often to rotate logs | `"daily"` |
| `fail2ban_logrotate_options.count` | Number of rotated log files to keep | `30` |
| `fail2ban_logrotate_options.missingok` | Don't error if log file is missing | `true` |
| `fail2ban_logrotate_options.compress` | Compress rotated logs using gzip | `true` |
| `fail2ban_logrotate_options.nocreate` | Don't create new empty log file | `false` |
| `fail2ban_logrotate_options.copytruncate` | Use copy+truncate instead of move | `false` |
| `fail2ban_logrotate_options.dateext` | Add date extension to rotated logs | `true` |

### 3. 🔧 Daemon Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_socket` | Socket file for daemon communication | `/var/run/fail2ban/fail2ban.sock` |
| `fail2ban_pidfile` | PID file for the daemon | `/var/run/fail2ban/fail2ban.pid` |
| `fail2ban_loglevel` | Log level (CRITICAL, ERROR, WARNING, NOTICE, INFO, DEBUG) | `"INFO"` |
| `fail2ban_logtarget` | Log target (file, SYSTEMD-JOURNAL, SYSLOG, STDERR, STDOUT) | `/var/log/fail2ban.log` |
| `fail2ban_syslog_target` | Syslog target for fail2ban | `/var/log/fail2ban.log` |
| `fail2ban_syslog_facility` | Syslog facility number | `1` |

### 4. 🏢 Jail Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_protocol` | Default protocol to use in jail definitions | `"tcp"` |
| `fail2ban_ignoreself` | Whether to ignore the local IP addresses | `"true"` |
| `fail2ban_ignoreip` | List of IPs or CIDR ranges to never ban | `["127.0.0.1/8", "::1"]` |

### 5. 🚫 Ban Settings

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

### 6. 📧 Email Notification Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_email_notification_enabled` | Enable/disable email notifications | `false` |
| `fail2ban_destemail` | Destination email for notifications | `"root@localhost"` |
| `fail2ban_sender` | Sender email for notifications | `"root@{{ ansible_fqdn }}"` |
| `fail2ban_mta` | Mail transport agent (sendmail, mail) | `"sendmail"` |

### 7. 🛠️ Custom Jail Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `fail2ban_custom_jail_files` | List of custom jail configurations | `[]` |
| `fail2ban_custom_jails_path` | Path to custom jail files | `"files/jail.d"` |
| `fail2ban_custom_actions_path` | Path to custom action files | `"files/action.d"` |
| `fail2ban_custom_filters_path` | Path to custom filter files | `"files/filter.d"` |

**Custom Jail Example:**
```yaml
fail2ban_custom_jail_files:
  - name: sshd-custom  # This will create /etc/fail2ban/jail.d/sshd-custom.conf
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

## 🔍 Included Custom Filters

The role includes the following custom filters that can be used in your jail configurations:

### 🔒 OpenVPN
- **Filter**: `files/filter.d/openvpn.conf`
- **Description**: Detects authentication failures in OpenVPN logs
- **Usage**: Add a custom jail for OpenVPN and use `filter = openvpn`

### 🌐 FreeIPA GUI
- **Filter**: `files/filter.d/freeipa-gui.conf`
- **Description**: Detects unauthorized access attempts to the FreeIPA web interface
- **Usage**: Add a custom jail for FreeIPA GUI and use `filter = freeipa-gui`

## 📖 Example Playbooks

### 🔧 Basic Configuration
```yaml
---
- name: "🛡️ Configure Fail2ban Protection"
  hosts: servers
  become: true
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

### 🛠️ Advanced Configuration with Custom Jails
```yaml
---
- name: "🛡️ Configure Advanced Fail2ban Protection"
  hosts: servers
  become: true
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
```

### 🔥 NFTables Integration
```yaml
---
- name: "🛡️ Configure Fail2ban with NFTables"
  hosts: firewalls
  become: true
  vars:
    fail2ban_role_mode: "nftables"
    fail2ban_service_enabled: true
    fail2ban_configure_logrotate: true
  roles:
    - role: grzegorzfranus.fail2ban
```

## 🏷️ Tags

Available tags for selective execution:

| Tag | Description | Usage |
|-----|-------------|-------|
| `always` | ✅ Always run tasks | Core functionality |
| `asserts` | 🧪 Run assertion tasks | Variable validation |
| `vars` | 📋 Load variables | Variable loading |
| `install` | 📦 Install Fail2ban packages | Package installation |
| `configure` | 🔧 Configure Fail2ban service | Service configuration |
| `custom_jails` | 🛠️ Configure custom jail files | Custom jail setup |
| `logrotate` | 📝 Configure logrotate | Log rotation setup |
| `upgrade` | 🔄 Upgrade Fail2ban packages | Package upgrades |

**Example with tags:**
```bash
# Install only
ansible-playbook playbook.yml --tags "install"

# Configure without installation
ansible-playbook playbook.yml --tags "configure,custom_jails"

# Everything except upgrades
ansible-playbook playbook.yml --skip-tags "upgrade"
```

## 📁 File Structure

```
ansible-role-fail2ban/
├── .github/                  # 🔄 GitHub Actions workflows
│   └── workflows/           # CI/CD automation
├── CHANGELOG.md              # 📝 Version history and changes
├── LICENSE                   # ⚖️ Apache-2.0 license
├── README.md                # 📖 This documentation file
├── defaults/
│   └── main.yml             # ⚙️ Default configuration variables
├── files/                   # 📁 Static files for custom filters
│   ├── action.d/           # Custom action files
│   └── filter.d/           # Custom filter files
│       ├── freeipa-gui.conf # 🌐 FreeIPA GUI filter
│       └── openvpn.conf    # 🔒 OpenVPN filter
├── handlers/
│   └── main.yml             # 🔄 Service restart and reload handlers
├── meta/
│   └── main.yml             # 📋 Role metadata and Galaxy information
├── molecule/                # 🧪 Molecule testing framework
│   └── default/            # Default test scenario
│       ├── molecule.yml    # Test configuration
│       ├── converge.yml    # Role execution playbook
│       ├── prepare.yml     # Test preparation tasks
│       └── verify.yml      # Verification tests
├── tasks/
│   ├── main.yml            # 🎯 Main task orchestration
│   ├── assert.yml          # ✅ Variable validation and system checks
│   ├── install.yml         # 📦 Package installation
│   ├── configure.yml       # 🔧 Service configuration
│   ├── custom_jails.yml    # 🛠️ Custom jail management
│   ├── logrotate.yml       # 📝 Log rotation configuration
│   └── upgrade.yml         # 🔄 Package upgrades
├── templates/              # 📄 Configuration templates
│   ├── fail2ban/          # Core Fail2ban configuration
│   │   ├── fail2ban.local.j2 # Main daemon configuration
│   │   └── jail.local.j2   # Jail configurations
│   ├── logrotate/         # Log rotation configurations
│   │   ├── debian/        # Debian-specific templates
│   │   └── redhat/        # RedHat-specific templates
│   └── systemd/           # Systemd service overrides
└── vars/
    └── main.yml            # 🔧 Internal role variables
```

## 🔧 Troubleshooting

### Common Issues

#### 🚨 Service Won't Start
```bash
# Check service status
sudo systemctl status fail2ban

# Check configuration syntax
sudo fail2ban-client -t

# View recent logs
sudo journalctl -u fail2ban -n 50
```

#### 🔍 Debugging Jail Issues
```bash
# Test jail configuration
sudo fail2ban-client status

# Check specific jail
sudo fail2ban-client status sshd

# View banned IPs
sudo fail2ban-client get sshd banip
```

#### 📧 Email Notification Issues
```bash
# Test email configuration
echo "Test" | mail -s "Test Subject" admin@example.com

# Check MTA logs
sudo journalctl -u postfix -n 20  # For postfix
```

### 🧪 Testing
```bash
# Run molecule tests
molecule test

# Run ansible-lint
ansible-lint

# Run yamllint
yamllint .
```

### 🔄 Development Workflow
1. **🍴 Fork** the repository and create your branch from `main`
2. **✅ Make** your changes with clear, descriptive commit messages
3. **🧪 Ensure** your code passes all Molecule and lint tests
4. **📝 Submit** a pull request describing your changes and the motivation
5. **💬 For major changes**, please open an issue first to discuss what you would like to change

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).

---
**🛡️ Keep your systems secure with Fail2ban! 🚀**
