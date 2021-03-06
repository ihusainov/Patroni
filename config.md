
**Postgresql + Patroni config**

```
haproxy:  ssh user@10.20.30.50
etcd-1:   ssh user@10.20.30.41
etcd-2:   ssh user@10.20.30.42
etcd-3:   ssh user@10.20.30.43
pgbouncer-1:   ssh user@10.20.30.71
pgbouncer-2:   ssh user@10.20.30.72
pgsql-master:  ssh user@10.20.30.90
pgsql-replica-1:  ssh user@10.20.30.91
pgsql-replica-2:  ssh user@10.20.30.92
pgsql-replica-3:  ssh user@10.20.30.93
```

**Install PostgreSQL**

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql-13 vim jq

systemctl stop postgresql
```
```
vim /etc/postgresql/13/main/postgresql.conf
vim /etc/postgresql/13/main/pg_hba.conf
```
**Install patroni**
```
apt -y install python3-pip python3-dev libpq-dev
pip3 install --upgrade setuptools pip
pip install psycopg2-binary
pip install patroni[etcd]
```
**Create patroni directory**
```
mkdir -p /etc/patroni
mkdir -p /mars/patroni
mkdir -p /db/pg_archived
```
**Edit patroni config file on every instance**
```
vim /etc/patroni/patroni.yml

chmod 600 /etc/patroni/patroni.yml
chown -R postgres: /etc/patroni
chown -R postgres: /mars/patroni
chown -R postgres: /db/pg_archived
```
**Create systemd unit file**
```
vim /etc/systemd/system/patroni.service
```
```
[Unit]
Description=Patroni needs to orchestrate a high-availability PostgreSQL
Documentation=https://patroni.readthedocs.io/en/latest/
After=syslog.target network.target
[Service]
User=postgres
Group=postgres
Type=simple
ExecStart=/usr/local/bin/patroni /etc/patroni/patroni.yml
Restart=no
[Install]
WantedBy=multi-user.target
```
**Start systemd service patroni**
```
systemctl enable patroni.service
systemctl start patroni.service
systemctl status patroni.service
systemctl stop patroni.service
systemctl restart patroni.service
```
**Check Patroni**
```
curl http://10.20.30.90:8008/patroni | jq
```

**Get etcd hosts**
```
curl http://10.20.30.41:2379/v2/members | jq
curl http://10.20.30.41:2379/v2/machines
```

**Work with cluster info**
```
patronictl -c /etc/patroni/patroni.yml remove mars
patronictl -c /etc/patroni/patroni.yml list
```
