[source-guide]: https://www.dionysopoulos.me/custom-apache-and-php-server-on-macos-the-definitive-2019-edition/

# AMP Stack Setup Documentation

This repository contains instructions to install and configure an AMP+ stack on macOS. It is not comprehensive, but rather is a basic working server setup that can be further customized as needed.

### Credits

*The following setup is distilled from [this guide by nikosdion][source-guide], with added customization.*

## Prerequisites

*MacOS 10.15.0 Catalina and later use `zsh` by default.* This guide assumes you are using `bash@4.0.0+`. If not using `bash`, some commands may need to be adjusted slightly.

### Install Xcode

Install Xcode, then run:

```bash
sudo xcodebuild -license accept
```

### Install HomeBrew

Follow installation instructions for [HomeBrew](https://brew.sh/), then run:

```bash
brew update
brew upgrade
```

### Disable existing stack

At this stage, you will need to disable any existing AMP stack you may already be running (MAMP, XAMPP, etc.). **Export your MySQL databases first**, as you will need to import them later.

### Backup default config files

Before modifying configuration files in the next steps, it is a good idea to backup the default versions after installation. Something like the following should be sufficient (commands may require `sudo`):

```bash
cp /path/to/<file>.conf /path/to/<file>.conf.default
```

To revert all changes made to a config file should something go wrong, run the inverse and repeat the process:

```bash
rm /path/to/<file>.conf
cp /path/to/<file>.conf.default /path/to/<file>.conf
```

## Installation

1. [Install Apache](docs/apache.md)
2. [Install MySQL](docs/mysql.md)
3. [Install PHP](docs/php.md)
4. [Install Redis (optional)](docs/redis.md)
5. [Configure Apache with PHP-FPM](docs/apache-php.md)
6. [Configure domains and subdomains](docs/domains.md)
7. [Helper scripts](docs/helper-scripts.md)

## Additional Components

See [the original guide][source-guide] to install/configure additional components:

- XDebug
- MailHog
- SSL
- PECL extensions
