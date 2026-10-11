# GRUB Protector

A Bash utility for setting up and removing password protection for GRUB on Debian and Ubuntu systems.

GRUB Protector automates the configuration of a GRUB superuser, generates a PBKDF2 password hash, modifies GRUB configuration scripts, and regenerates the GRUB configuration.

## Features

- **GRUB password protection** — Configures a GRUB superuser with a PBKDF2 password hash.
- **Password confirmation** — Requires the password to be entered twice.
- **Automatic backups** — Creates backup copies of the original GRUB configuration scripts before applying changes.
- **Menu entry configuration** — Modifies GRUB scripts to configure access restrictions for boot menu entries.
- **Configuration regeneration** — Runs `update-grub2` to regenerate the GRUB configuration.
- **Password removal** — Restores backed-up GRUB scripts and regenerates the configuration.
- **Operating system detection** — Supports Debian and Ubuntu.
- **Root privilege validation** — Requires root privileges for configuration changes.
- **Operation logging** — Records GRUB update output and password-hash generation errors in a log file.
- **Command-line interface** — Supports setting a password, removing password protection, and displaying help.

## Supported Operating Systems

- Debian GNU/Linux
- Ubuntu

Other distributions are not supported by the current implementation.

## Requirements

The following components are required:

- Bash
- GRUB 2
- `grub-mkpasswd-pbkdf2`
- `update-grub2`
- Standard Linux utilities, including `grep`, `awk`, `sed`, and `cp`

The script expects the following files to exist:

- `/etc/grub.d/40_custom`
- `/etc/grub.d/10_linux`

## Installation

Clone the repository:

```bash
git clone https://github.com/morteza-j/grub-protector.git
```

Enter the project directory:

```bash
cd grub-protector
```

Make the script executable:

```bash
chmod +x grub-protector.sh
```

Run the script as root when changing GRUB configuration.

## Usage

### Display Help

```bash
./grub-protector.sh --help
```

Displays the available options.

### Set a GRUB Password

```bash
./grub-protector.sh --set-password
```

Short option:

```bash
./grub-protector.sh -s
```

The script prompts for a GRUB username and password. The password must be entered twice.

It then attempts to:

1. Back up the original GRUB configuration scripts.
2. Generate a PBKDF2 password hash.
3. Update `/etc/grub.d/40_custom` with the GRUB superuser and password hash.
4. Modify `/etc/grub.d/10_linux` to adjust menu entry access.
5. Regenerate the GRUB configuration.

### Remove GRUB Password Protection

```bash
./grub-protector.sh --unset-password
```

Short option:

```bash
./grub-protector.sh -u
```

The script asks for confirmation before attempting to restore the original configuration scripts from the backup files.

If both backup files are available, it restores them and regenerates the GRUB configuration.

**Important:** Password removal depends on the backup files created by the script. Without both backups, the script cannot perform its intended restoration procedure.

## Files and Logging

| Path | Purpose |
|---|---|
| `/etc/grub.d/40_custom` | Stores the configured GRUB superuser and PBKDF2 password hash |
| `/etc/grub.d/40_custom.orig` | Backup of the original custom GRUB script |
| `/etc/grub.d/10_linux` | GRUB Linux menu entry generation script |
| `/etc/grub.d/10_linux.orig` | Backup of the original Linux menu generation script |
| `grub_setup.log` | Intended log file for GRUB operations |

The script defines its log path relative to `SCRIPT_DIR`. Ensure that this variable is initialized correctly before running the script; it is not defined in the supplied implementation.

## Security Considerations

- Use a strong, unique GRUB password.
- Protect the system against unauthorized physical access.
- Store backup files securely because they contain copies of configuration scripts.
- Keep a recovery method available before modifying the bootloader.
- Test changes on a non-production system before applying them to a critical machine.
- Verify the generated GRUB configuration and test boot behavior after making changes.
- Understand that GRUB password protection does not encrypt the operating system or protect data on its own.

### Important: GRUB Passwords and Boot Security

A GRUB password can restrict access to protected boot menu operations, but it is not a complete system security solution.

The script modifies `/etc/grub.d/10_linux` directly. Package upgrades may replace or change that file, potentially affecting the custom modifications. The script also relies on its own backup files to restore the previous configuration.

UEFI Secure Boot, firmware configuration, disk encryption, and physical access controls are separate security mechanisms that may be necessary for a more complete boot security strategy.

## Limitations

- The script is designed specifically for Debian and Ubuntu.
- It assumes the standard GRUB configuration paths.
- It relies on the existence of backup files to remove the password using its restoration procedure.
- It modifies a distribution-managed GRUB script directly.
- It does not independently validate the complete generated GRUB configuration before applying changes.
- It does not provide an automated recovery environment if the system becomes unbootable.

## Contributing

Contributions, bug reports, and suggestions are welcome.

Please test changes carefully and preserve the integrity of the system's boot configuration.

## License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.

See the [`LICENSE`](LICENSE) file for the full license text.

## Author

**J. Morteza**

Created on August 20, 2025.
