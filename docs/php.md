# Install PHP

To install older unsupported versions of PHP, you will need to add a repository to `brew` (only currently maintained versions of PHP are in the default repositories):

```bash
brew tap exolnet/deprecated
```

Install required PHP versions (add or remove versions depending on what you need):

```bash
brew install php@5.6
brew install php@7.3
brew install php@7.4
```

Make sure required directories are writeable:

```bash
sudo chown -R $(whoami):staff /usr/local/include/php/
sudo chown -R $(whoami):staff /usr/local/lib/php/
sudo chown -R $(whoami):staff /usr/local/etc/php/
```

Change the default PHP version for the system (change the version number in the `brew link` command if you want to use a different version by default):

```bash
brew unlink php
brew link --overwrite --force php@7.4
```

## Fix Configuration Files

HomeBrew sometimes does not correctly copy PHP configurations to the correct locations. Fix that here:

```bash
# Install configuration files for PHP 5.6
cd $(brew --prefix php@5.6)
cp -R .bottle/* /usr/local/

# Install configuration files for PHP 7.3
cd $(brew --prefix php@7.3)
cp -R .bottle/* /usr/local/

# Install configuration files for PHP 7.4
cd $(brew --prefix php@7.4)
cp -R .bottle/* /usr/local/
```

## Change PHP-FPM Configurations

To keep port `9000` for XDebug, and to make PHP-FPM ports easy to identify, use the PHP versions in the new port numbers.

### Edit config for PHP 5.6

```bash
open /usr/local/etc/php/5.6/php-fpm.conf -a TextEdit
```

### Change:

```bash
user = _www
group = _www

# ...

listen = 127.0.0.1:9000
```

### To:

```bash
user = username
group = staff


# ...

listen = 127.0.0.1:9056
```

### Edit configs for other PHP versions

```bash
open /usr/local/etc/php/7.3/php-fpm.d/www.conf -a TextEdit
open /usr/local/etc/php/7.4/php-fpm.d/www.conf -a TextEdit
```

Change username and group, then change ports based on the PHP version:

```bash
# In all configs, change:
listen = 127.0.0.1:9000

# To:

# For PHP 7.3:
listen = 127.0.0.1:9073

# For PHP 7.4:
listen = 127.0.0.1:9074

# Etc.
```

## Start PHP-FPM Services

```bash
brew services start php@5.6
brew services start php@7.3
brew services start php@7.4
```
