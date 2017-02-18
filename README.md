# ansible-persistent-data

*Sets up easy to use export/import scripts for a given application.*

It's a common need to be able to export all persistent data from a given project into an archive
that could then be easily imported back in. This is typically needed for backup/restore scenarios,
but also in scenario where we transfer an application from an environment to another. This role
helps you do that.

It provisions two scripts in a given base path: `export_data.sh` and `import_data.sh`. The "export"
part spits a `tar` to `stdout` that can be fed to the "import" part's `stdin`.

Two types of persistent data are supported by this role: folders and databases.

`persistent_data_folders` if a list of absolute paths to include in our tar. They will be included
as-is in the exported tar. On import time, those paths will be **overwritten** by the contents of
the corresponding folder in the tar.

`persistent_data_databases` (see `defaults/main.yml` for the format) archive a postgres or mariadb
database and puts it into the tar. On import time, that database will be **overwritten** by the
archived database in the tar.

## Requirements

* Ansible 2.2+
* One of the following target OS:
  * Debian 8 (jessie)

## Usage

```yaml
- role: persistent-data
  persistent_data_user: myuser
  persistent_data_path: /opt/myproject
  persistent_data_folders:
    - /opt/myproject/user-images
  persistent_data_databases:
    - name: mydb.sql
      type: mariadb
      dbname: myproject
      dbuser: myuser
      dbpass: hunter2
```

```console
$ ssh user@host '/opt/myproject/export_data.sh | gzip -c' > myproject-dump.tar.gz
$ ssh user@otherhost 'gzip -dc | /opt/myproject/import_data.sh' < myproject-dump.tar.gz
```
