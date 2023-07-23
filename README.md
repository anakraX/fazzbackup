# fazzbackup

> Designed and built by [gobackup/gobackup](https://github.com/gobackup/gobackup) and modified for personal needs.

## Installation

```shell
curl -sSL https://bifebriansyah.github.io/fazzbackupweb/install | sh
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

### Start Daemon & Web UI

fazzbackup bulit a HTTP Server for Web UI, you can start it by `fazzbackup start`.

It also will handle the backup schedule.

```bash
$ fazzbackup start

2023/03/15 23:00:30 [Config] Load config from default path.
Starting API server on port http://127.0.0.1:2703
```

> NOTE: If you wants start without daemon, use `fazzbackup run` instead.

## License

[MIT](https://choosealicense.com/licenses/mit/)
