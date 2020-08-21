# Helper Scripts

The included helper scripts are available to assist with managing AMP stack configuration and service management.

## Consolidated Service Management

The following helper script is a shorthand for starting/restarting/stopping the AMP services.

### Setup

In your shell profile (`~/.bashrc`, `~/.bash_profile`, `~/.zshrc`, etc.), add the following line:

```bash
export AMP_SERVICES="httpd mysql@5.7 php@5.6 php@7.3 php@7.4 redis"
```

This is a space-separated list of service identifiers expected by `brew services`. Add or remove service identifiers depending on which ones were installed.

Source the file or open a new shell session to load the environment variable:

```bash
source ~/<profile-file>
```

Copy the [amp](../scripts/amp) script to a location in `$PATH` and make the copy executable:

```bash
cp /path/to/config-docs/scripts/amp /executable/path/for/user/
chmod u+x /executable/path/for/user/amp
```

### Usage

```bash
amp <action> [...<service-id>]
```

Running `amp` with no arguments will print basic help text and show the available configured services in the `$AMP_SERVICES` environment variable.

The `<action>` argument may be any of the following, and map directly

- `start`
- `restart`
- `stop`

The `<service-id>` arguments are optional, and can be one or more of the service names defined in `$AMP_SERVICES`. The specified `<action>` argument will be performed on all specified services. Note the following use cases:

```bash
# Restart all services listed in the $AMP_SERVICES environment variable
amp restart

# Start only apache
amp start httpd

# Stop PHP 5.6 and PHP 7.3
amp stop php@5.6 php@7.3
```

## Link Config Files

To make modifying global config files easier after initial installation, you can link to them in an one location.

Copy the [amp-link](../scripts/amp-link) script to a location in `$PATH` and make the copy executable:

```bash
cp /path/to/config-docs/scripts/amp-link /executable/path/for/user/
chmod u+x /executable/path/for/user/amp-link
```

Change the `BASE_DIR="${HOME}/amp` in the copied version to a different directory to store the configs somewhere else (no trailing slash). By default, the script will store configs in a new `amp/` directory in the current user's home directory.

You can also add or remove sections in the copied version based on what components are actually installed.

By default, the script uses hard links to copy the config files. This can be changed to use symlinks by changing all `ln` commands to `ln -s`.

Run the script to link the config files:

```bash
amp-link
```

As additional components are installed/removed, the copied version of this script can be updated to link the new config files.

### Benefits

- The config files can now be edited in the new config location, and the actual installation configs will be updated
- Should the source files move or be deleted for some reason, they will still exist in the new hardlinked location
    - See caveats if symlinks are used instead of hard links
- When backing up the configuration, the actual contents of the config files will be backed up and can be hardlinked back to the actual installation location
    - See caveats if symlinks are used instead of hard links

### Caveats

- If the source files are deleted, they will need to be recreated by hardlinking the files in the the new config directory, otherwise the association between the two locations will be lost
- In order to back up the MySQL data directory, the actual contents of the symlink need to be copied, not just the link itself
- **If symlinks are used instead of the default hard links:**
    - Modified confg changes will be lost if the actual installation config files are lost
    - When backing up the configs, care must be taken to ensure that the actual file data is backed up, not just the symlinks themselves
