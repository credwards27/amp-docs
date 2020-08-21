# Install Apache

```bash
brew install httpd
brew link httpd
brew services start httpd
```

This installs, links the binaries in the main system path locations (sometimes manually required depending on your `brew` setup), and then starts Apache so that it will run automatically on startup.

## Fix Ownership

Depending on your system configuration, this is sometimes necessary:

```bash
sudo mkdir /usr/local/var/run/httpd
sudo chown -R $(whoami):admin /usr/local/var/run/httpd
```

## Configure

```bash
open /usr/local/etc/httpd/httpd.conf -a TextEdit
```

Apply the following changes:

### Change:

```bash
User _www
Group _www
```

### To (use your actual username):

```bash
User username
Group admin
```

### Change:

```bash
Listen 8080
```

### To:

```bash
Listen *:80
```

### Make sure this is present:

```bash
<Directory />
    AllowOverride none
    Require all denied
</Directory>
```

### Look for:

```bash
DocumentRoot "/usr/local/var/www"
<Directory "/usr/local/var/www">
```

### And change the entire block as follows:

```bash
# Configure the default root directory
DocumentRoot "/system/path/to/root/sites/directory"
<Directory "/system/path/to/root/sites/directory">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   AllowOverride FileInfo AuthConfig Limit
    #
    AllowOverride All

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>
```

## Restart Apache

```bash
brew services restart httpd
```
