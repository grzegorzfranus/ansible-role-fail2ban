# Changelog

All notable changes to this project will be documented in this file.

## [1.0.2] - 2025-07-04

### Added ✅
- Added molecule.yml configuration file for Docker-based testing
- Enhanced Molecule testing framework with proper Docker platform configuration
- Added support for environment variable-based testing configuration

### Fixed 🔧
- Fixed handler name mismatch causing "handler not found" errors
- Added emojis to notify statements in tasks to match handler names
- Corrected all notify statements to use "🔄 Fail2ban | handlers | Restart fail2ban service"
- Fixed all notify statements to use "🔄 Fail2ban | handlers | Reload systemd daemon"
- Fixed task naming convention to use file-based categories instead of module types
- Corrected all task names to follow "Fail2ban | [filename] | [description]" pattern
- Fixed yamllint errors: removed trailing spaces and added newline at end of file
- Achieved 100% compliance with yamllint and ansible-lint standards

## [1.0.1] - 2024-12-19

### Added ✅
- Status emojis in ALL task names following core development rules
- Enhanced task naming with "Fail2ban | category | description" pattern
- Comprehensive validation messages with success/failure indicators
- Enhanced error reporting and debugging capabilities
- Post-installation and post-upgrade verification tasks
- Configuration summary reporting in all task files
- Enhanced template headers with proper documentation
- Comprehensive README formatting with status emojis throughout
- Advanced playbook examples with NFTables integration
- Detailed troubleshooting section with common solutions
- File structure documentation with emoji indicators
- Development workflow and testing instructions

### Fixed 🔧
- Converted all legacy ansible modules to ansible.builtin format
- Fixed ansible-lint violations (no-handler, package-latest)
- Removed trailing spaces and added missing newlines (yamllint compliance)
- Improved conditional execution patterns consistency
- Enhanced systemd integration with proper handler usage
- Fixed orphaned jail file cleanup logic in custom_jails.yml
- Corrected assert task emojis to use 🧪 pattern matching example standards

### Changed 🔄
- ALL task files updated with Ansible best practices
- Enhanced section headers with clear comment blocks
- Improved YAML dictionary format for all module parameters
- Updated handlers with enhanced logging and error handling
- Enhanced Molecule test files with status emojis and better structure
- Upgraded template documentation following Ansible standards
- README.md completely redesigned with enhanced formatting and structure
- Consistent tagging strategy across all task files
- Assert tasks standardized with 🧪 emoji following .cursor/examples pattern

### Improved 🚀
- **Task Naming**: Consistent emoji-based naming throughout all tasks
- **Documentation**: Enhanced inline documentation and comments
- **Validation**: Comprehensive variable validation with detailed error messages
- **Reporting**: Status reporting after each major operation
- **Testing**: Enhanced Molecule tests with better verification
- **Linting**: Full compliance with ansible-lint and yamllint standards
- **User Experience**: Better feedback and status indicators
- **Maintainability**: Improved code organization and structure

### Code Quality 🧪
- 100% ansible-lint compliance achieved
- 100% yamllint compliance achieved
- Enhanced error handling throughout all tasks
- Improved idempotency with better change detection
- Added backup options for configuration file changes
- Enhanced retry logic for package operations

## [1.0.0] - 2024-03-20

### Added ✅
- Initial release of ansible-role-fail2ban
- Fail2ban package installation and configuration
- Support for Ubuntu 22.04 (Jammy Jellyfish)
- Support for Debian 12 (Bookworm)
- Support for Rocky Linux 9 and EL 9
- Custom jail configuration support
- Email notification configuration
- Progressive ban time features (bantime.increment)
- Logrotate configuration for fail2ban logs
- nftables and iptables backend support
- Custom filters for OpenVPN and FreeIPA GUI
- Comprehensive variable validation
- Systemd service integration
- Role action control (install, configure, upgrade, etc.)

### Features 🚀
- **Multi-OS Support**: Ubuntu, Debian, Rocky Linux, EL
- **Flexible Configuration**: Comprehensive variable system
- **Custom Jails**: Easy deployment of custom jail files
- **Security**: Progressive ban times and advanced filtering
- **Monitoring**: Built-in logrotate and service management
- **Firewall Integration**: Support for both nftables and iptables

### Security 🔒
- Default ignore list includes localhost and IPv6
- Configurable IP whitelist support
- Progressive ban time to handle repeat offenders
- Email notifications for security events
