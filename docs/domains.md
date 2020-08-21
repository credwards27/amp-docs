# Domains and Subdomains

## Hosts File

All local domains and subdomains need to be defined in `/etc/hosts` in order to resolve to `localhost`, otherwise the system will send the request to the outside internet.

Make sure the following block is present at the top of `/etc/hosts`:

```bash
127.0.0.1       localhost
255.255.255.255 broadcasthost
::1             localhost
```

Use the following formats for all domains and subdomains in `/etc/hosts`:

```bash
127.0.0.1   domain.local
::1         domain.local

127.0.0.1   subdomain.domain.local
::1         subdomain.domain.local
```

**Apache does not need to be restarted after editing `/etc/hosts`.**

## Apache Virtual Hosts

To enable subdomains, you will need to configure Apache to use virtual hosts:

Open the main Apache config file:

```bash
open /usr/local/etc/httpd/httpd.conf -a TextEdit
```

Uncomment the following line:

```bash
#Include /usr/local/etc/httpd/extra/httpd-vhosts.conf
```

Open the virtual hosts file:

```bash
open /usr/local/etc/httpd/extra/httpd-vhosts.conf -a TextEdit
```

### Arbitrary subdomains

The following template can be used to define a domain with arbitrary subdomains:

```bash
# Default domain definition
<VirtualHost *:80>
    ServerName domain.local
    
    DocumentRoot "/system/path/to/root/domain/directory"
    
    ServerAdmin admin@misc.local
    
    <Directory "/system/path/to/root/domain/directory">
        Options Indexes FollowSymLinks
        AllowOverride All
        DirectoryIndex index.html index.php
        Require all granted
        
        # See https://wiki.apache.org/httpd/PHP-FPM
        <FilesMatch "\.php$">
            SetHandler "proxy:fcgi://127.0.0.1:9074/"
        </FilesMatch>
    </Directory>
</VirtualHost>

# Arbitrary subdomain mapping
<VirtualHost *:80>
    ServerAlias *.domain.local
    
    VirtualDocumentRoot "/system/path/to/root/domain/%1"
    
    ServerAdmin admin@domain.local
    UseCanonicalName Off
    
    <Directory "/system/path/to/root/domain">
        Options Indexes FollowSymLinks
        AllowOverride All
        DirectoryIndex index.html index.php
        Require all granted
        
        # See https://wiki.apache.org/httpd/PHP-FPM
        <FilesMatch "\.php$">
            SetHandler "proxy:fcgi://127.0.0.1:9074/"
        </FilesMatch>
    </Directory>
</VirtualHost>
```

Replace the paths with the actual directory paths to the site files, and change the PHP-FPM ports if using a different PHP version for the sites.

#### Considerations

Using this configuration achieves the following setup:

- Subdomains will be mapped to directories of the same name under the `VirtualDocumentRoot`
    - I.e. `http://my-subdomain.domain.local` will serve files from `/system/path/to/root/domain/my-subdomain/`
- All subdomains will use the same PHP version
    - If different versions are needed, you can place a `<FilesMatch "\.php$">` block with a different PHP-FPM port number in a `<Directory>` block with a specific subdirectory or in the subdomain directory's `.htaccess` file; or configure a predefined subdomain setup as described below

### Predefined subdomains

The following template can be used to define specific subdomains:

#### Multiple domains/subdomains serving files from the same directory

```bash
<VirtualHost *:80>
    ServerName domain.local
    ServerAlias www.domain.local
    ServerAlias subdomain.domain.local
    
    DocumentRoot "/system/path/to/root/domain/directory"
    
    ServerAdmin admin@misc.local
    
    <Directory "/system/path/to/root/domain/directory">
        Options FollowSymLinks
        AllowOverride All
        DirectoryIndex index.html index.php
        Order allow,deny
        Allow from all
        Require all granted
        
        # See https://wiki.apache.org/httpd/PHP-FPM
        <FilesMatch "\.php$">
            SetHandler "proxy:fcgi://127.0.0.1:9074/"
        </FilesMatch>
    </Directory>
</VirtualHost>
```

#### One or more domains/subdomains serving files from a specific directory

```bash
<VirtualHost *:80>
    ServerAlias another-subdomain.domain.local
    
    DocumentRoot "/system/path/to/root/another-domain/directory"
    
    ServerAdmin admin@misc.local
    
    <Directory "/system/path/to/root/another-domain/directory">
        Options FollowSymLinks
        AllowOverride All
        DirectoryIndex index.html index.php
        Order allow,deny
        Allow from all
        Require all granted
        
        # See https://wiki.apache.org/httpd/PHP-FPM
        <FilesMatch "\.php$">
            SetHandler "proxy:fcgi://127.0.0.1:9074/"
        </FilesMatch>
    </Directory>
</VirtualHost>
```

For simplicity, the default domain should be defined first using a `ServerName` directive, while subdomains of that domain should be defined using `ServerAlias` directives.

If both of these blocks were to be added to `httpd-vhosts.conf`, the following would be true:

- `http://domain.local`, `http://www.domain.local`, and `http://subdomain.domain.local` will all serve the same files from `/system/path/to/root/domain/directory/`
- `http://another-subdomain.domain.local` will serve files from `/system/path/to/root/another-domain/directory/`

## Restart Apache

```bash
brew services restart httpd
```

## Testing Setup

Place a file named `index.php` in the root directory of a configured site directory with the following code:

```php
<?php

phpinfo();
exit;
```

If successful, navigating to the configured domain/subdomain should show configuration information for the configured PHP version.
