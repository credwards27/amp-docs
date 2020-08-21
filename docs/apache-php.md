# Configuring Apache With PHP-FPM

Open the the apache config file to make the following changes:

```bash
open /usr/local/etc/httpd/httpd.conf -a TextEdit
```

## Enable Modules

Find the following lines and uncomment them:

```bash
#LoadModule deflate_module lib/httpd/modules/mod_deflate.so
#LoadModule mime_magic_module lib/httpd/modules/mod_mime_magic.so
#LoadModule expires_module lib/httpd/modules/mod_expires.so
#LoadModule proxy_module lib/httpd/modules/mod_proxy.so
#LoadModule proxy_http_module lib/httpd/modules/mod_proxy_http.so
#LoadModule proxy_fcgi_module lib/httpd/modules/mod_proxy_fcgi.so
#LoadModule vhost_alias_module lib/httpd/modules/mod_vhost_alias.so
#LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so
```

## Configure PHP Handlers

Find the main site root block (configured during [Apache installation](apache.md)):

```bash
DocumentRoot "/system/path/to/root/sites/directory"
<Directory "/system/path/to/root/sites/directory">
```

Add the following to the `<Directory>` block. To use a different PHP version, replace `9074` with the port number of a different version as configured during the [PHP installation](php.md):

```bash
<FilesMatch "\.php$">
    SetHandler "proxy:fcgi://localhost:9074/"
</FilesMatch>
```

## Restart Apache

```bash
brew services restart httpd
```
