# Install MySQL

```bash
brew install mysql@5.7
brew link mysql@5.7 --force
brew services start mysql@5.7
```

The default username is `root` and the default password is empty; you can change it with this command:

```bash
mysqladmin --user=root password "newpassword"
```

## Create Databases

You can create databases using a GUI or from the command line:

```bash
mysql -u root -p

# Enter password

CREATE SCHEMA `database_name` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
exit;
```

If you have set up other users, replace `root` with the username you prefer to use (assuming it has database creation privileges). You can also replace `utf8mb4` and `utf8mb4_unicode_ci` with a different character set and/or collation as needed.

## Import Databases (If Necessary)

You can now create the databases that you need to import from your old configuration, then import the `.sql` files using a GUI or the command line:

```bash
mysql -u username -p datebase_name < export.sql
```
