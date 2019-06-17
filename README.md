# db-cloner

DB Cloner will fetch a dump of a production or staging database and 
seed it into the vagrant's database. Thus this is primarily for development but could, in theory, be used
in staging or production environments.

## Role Variables

Variable | Default | Notes
--- | --- | ---
db_cloner_user | vagrant | a server user that can open ssh tunnels
db_cloner_sql_dump_path | /tmp | No trailing slash 
__DB SSH USER__ | | 
db_cloner_ssh_user | user | the ssh user needed to access the remote database 
db_cloner_listening_port | 3310 | -
db_cloner_remote_host | localhost | -
__REMOTE DB__ | |
db_cloner_remote_db_user | root | -
db_cloner_remote_db_pass | secret | -
db_cloner_remote_db_name | database | -
db_cloner_remote_db_port | 3306 | -
db_cloner_remote_db_host | 127.0.0.1 | -
__LOCAL DB__ | |
db_cloner_local_db_user | root | -
db_cloner_local_db_pass | secret | -
db_cloner_local_db_name | database | -
db_cloner_local_db_port | 3306 | -
db_cloner_local_db_host | 127.0.0.1 | -

## Functional Overview

the `db_cloner_user` uses the `db_cloner_ssh_user` to open an ssh tunnel to host that can connect to the remote database.
I.e., it's unlike that vagrant@127.0.0.1 is allowed to open an mysql socket to your production database, so you need to 
route the requests through a server that can connect to the remote database.

From there, the script opens a mysql socket and dumps the remote database into the `db_cloner_sql_dump_path`

Finally the script executes the sql file on your local db.


## Multiple database on single provision
if you need to run the role multiple times, you must pass in different variables for each subsequent run. Any variable 
that is not overridden will use any global vars (i.e., set in the var/main.yml file), then any default values.

```yaml
# vars/main.yml
db_cloner_remote_db_host: a.host.with.multile.databases
```

```yaml
- role: db-cloner
  vars:
    db_cloner_local_db_name: local_a
    db_cloner_remote_db_name: remote_a
    db_cloner_remote_db_user: remote_db_a_user
    db_cloner_remote_db_pass: remote_db_a_pass
- role: db-cloner
  vars:
    db_cloner_local_db_name: local_b
    db_cloner_remote_db_name: remote_b
    db_cloner_remote_db_user: remote_db_b_user
    db_cloner_remote_db_pass: remote_db_b_pass
```