**ETCD**


**Create install file**
```
vim etcd_installer.sh
```

**Entry install file etcd_installer.sh**
```
ETCD_VER=v3.4.14
# choose either URL
GOOGLE_URL=https://storage.googleapis.com/etcd
GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
DOWNLOAD_URL=${GOOGLE_URL}
rm -f /mars/etcd-${ETCD_VER}-linux-amd64.tar.gz
rm -rf /mars/etcd/ && mkdir -p /mars/etcd/

curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /mars/etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf /mars/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /mars/etcd --strip-components=1
rm -f /mars/etcd-${ETCD_VER}-linux-amd64.tar.gz
```

**Create directory for etcd**
```
mkdir -p /etc/etcd/
mkdir -p /mars/etcd/data-dir
mkdir -p /mars/etcd/wal-dir
```
**Insert data from file etcd-2.conf.yml**
```
vim  /etc/etcd/etcd.yml
```
```
sudo useradd -s /bin/bash -d /mars/etcd/  etcd
chown -R etcd: /mars/etcd/
chmod 700 /mars/etcd/
```

**Create systemd config file**
```
vim /etc/systemd/system/etcd.service
```
```
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target
[Service]
User=etcd
Type=notify
ExecStart=/mars/etcd/etcd --config-file /etc/etcd/etcd.yml
Restart=always
[Install]
WantedBy=multi-user.target
```
```
systemctl enable etcd.service
systemctl start etcd.service
systemctl status etcd.service
systemctl restart etcd.service
```

**Check etcd**
```
curl -L http://10.20.30.42:2379/v2/keys
```
```
/mars/etcd/etcdctl --endpoints="10.20.30.41:2379,10.20.30.42:2379,10.20.30.43:2379" endpoint status -w table
/mars/etcd/etcdctl --endpoints="10.20.30.41:2379,10.20.30.42:2379,10.20.30.43:2379" endpoint health -w table
```




