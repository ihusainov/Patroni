
**Install confd on VM with pgbouncer**
```
cd /usr/local/bin
wget https://github.com/kelseyhightower/confd/releases/download/v0.16.0/confd-0.16.0-linux-amd64
mv confd-0.16.0-linux-amd64 confd
chmod +x confd
mkdir -p /etc/confd/{conf.d,templates}
```

**Edit pgbouncer config**
```
vim /etc/confd/conf.d/pgbouncer.toml
```
```
[template]

prefix = "/mars-cluster/mars"
owner = "postgres"
mode = "0644"
src = "pgbouncer.tmpl"
dest = "/etc/pgbouncer/pgbouncer.ini"
reload_cmd = "systemctl reload pgbouncer"
keys = ["/members/","/leader"]
```

**Edit pgbouncer template**
```
vim /etc/confd/templates/pgbouncer.tmpl
```
```
[databases]

{{with get "/leader"}}{{$leader := .Value}}{{$leadkey := printf "/members/%s" $leader}}{{with get $leadkey}}{{$data := json .Value}}{{$hostport := base (replace (index (split $data.conn_url "/") 2) "@" "/" -1)}}{{ $host := base (index (split $hostport ":") 0)}}{{ $port := base (index (split $hostport ":") 1)}}* = host={{ $host }} port={{ $port }} pool_size=10{{end}}{{end}}

[pgbouncer]
logfile = /var/log/postgresql/pgbouncer.log
pidfile = /var/run/postgresql/pgbouncer.pid
listen_addr = *
listen_port = 6432
#unix_socket_dir = /var/run/postgresql
auth_type = md5
auth_file = /etc/pgbouncer/userlist.txt
#auth_hba_file = /etc/pgbouncer/pg_hba.txt
admin_users = pg_admin
stats_users = pg_admin
pool_mode = transaction
max_client_conn = 7500
default_pool_size = 100
```
**Change permission**
```
chown -R postgres: /etc/confd
```
**Check to work**
```
/usr/local/bin/confd -prefix=/mars-cluster/mars -backend etcd  -node http://10.20.30.71:2379 -log-level debug -interval=10
```
