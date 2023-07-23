# fazzbackup

> Designed and built by [gobackup/gobackup](https://github.com/gobackup/gobackup) and modified for personal needs.

## Installation

```shell
curl -sSL https://anakrax.github.io/fazzbackupweb/install | sh
```

after that, you will get `/usr/local/bin/fazzbackup` command.

```bash
$ fazzbackup -h
NAME:
   fazzbackup - Backup your databases, files to FTP / SCP / S3 / GCS and other cloud storages.

USAGE:
   fazzbackup [global options] command [command options] [arguments...]

VERSION:
   1.3.0

COMMANDS:
   perform
   start    Start as daemon
   run      Run GoBackup
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help (default: false)
   --version, -v  print the version (default: false)
```

## Configuration

fazzbackup will seek config files in:

-   ~/.gobackup/gobackup.yml
-   /etc/gobackup/gobackup.yml

Example config: [gobackup_test.yml](https://github.com/huacnlee/gobackup/blob/master/gobackup_test.yml)

```yml
models:
    gitlab_app:
        databases:
            gitlab_db:
                type: postgresql
                database: gitlab_production
                username: gitlab
                password:
            gitlab_redis:
                type: redis
                mode: sync
                rdb_path: /var/db/redis/dump.rdb
                invoke_save: true
        storages:
            s3:
                type: s3
                bucket: my_app_backup
                region: us-east-1
                path: backups
                access_key_id: $S3_ACCESS_KEY_Id
                secret_access_key: $S3_SECRET_ACCESS_KEY
        compress_with:
            type: tgz
```

## Usage

### Perform backup

```bash
$ fazzbackup perform
```

### Backup schedule

FazzBackup built in a daemon mode, you can use `fazzbackup start` to start it.

You can configure the `schedule` for each models, it will run backup task at the time you set.

#### For example

Configure your schedule in `gobackup.yml`

```yml
models:
  my_backup:
    schedule:
      # At 04:05 on Sunday.
      cron: "5 4 * * sun"
    storages:
      local:
        type: local
        path: /path/to/backups
    databases:
      mysql:
        type: mysql
        host: localhost
        port: 3306
        database: my_database
        username: root
        password: password
  other_backup:
    # At 04:05 on every day.
    schedule:
      every: "1day",
      at: "04:05"
    storages:
      local:
        type: local
        path: /path/to/backups
    databases:
      mysql:
        type: mysql
        host: localhost
        port: 3306
        database: my_database
        username: root
        password: password
```

#### Perfom Backup On Production

Scheduling database backup using cronjob. don't forget put your credential into `~/.bashrc` and execute command `fazzbackup run`

```yml
models:
    fazz_dbsg:
        schedule:
            cron: '0 1 * * *' # 01:00 AM every day
        databases:
            psql:
                type: postgresql
                host: localhost
                port: 5432
                database: $PG_DBNAME #credential on bashrc
                username: $PG_USER
                password: $PG_PASSWORD
        storages:
            docean:
                type: spaces
                bucket: fazzbucket
                region: sgp1
                path: fazzdb
                access_key_id: $SPACE_ACCESS_KEY_ID #credential on bashrc
                secret_access_key: $SPACE_SECRET_ACCESS_KEY
        compress_with:
            type: tgz
```

### Start Daemon & Web UI

fazzbackup bulit a HTTP Server for Web UI, you can start it by `fazzbackup start`.

It also will handle the backup schedule.

```bash
$ fazzbackup start

2023/03/15 23:00:30 [Config] Load config from default path.
Starting API server on port http://127.0.0.1:2703
```

> NOTE: If you wants start without WEB UI, use `fazzbackup run` instead.

### Signal handling

fazzbackup will handle the following signals:

-   `HUP` - Hot reload configuration.
-   `QUIT` - Graceful shutdown.

```bash
$ ps aux | grep fazzbackup
fazztra+ 3541141  0.0  0.9 734464 20076 ?        Ssl  Apr18   4:46 fazzbackup run

# Reload configuration
$ kill -HUP 20443
# Exit daemon
$ kill -QUIT 20443
```

## License

[MIT](https://choosealicense.com/licenses/mit/)
