
**Install pgbouncer**
```
sudo apt-get install pgbouncer
```
**Edit config files pgbouncer.ini**
```
vim /etc/pgbouncer/pgbouncer.ini
```

**pgbouncer.ini**
```
;; database name = connect string
;;
;; connect string params:
;;   dbname= host= port= user= password=
;;   client_encoding= datestyle= timezone=
;;   pool_size= connect_query=
;;   auth_user=
[databases]

* = host=10.20.30.71 port=5432

pool_mode = transaction

; total number of clients that can connect
max_client_conn = 2500

; default pool size.  20 is good number when transaction pooling
; is in use, in session pooling it needs to be the number of
; max clients you want to handle at any moment
default_pool_size = 50

;; Minimum number of server connections to keep in pool.
;min_pool_size = 0

; any, trust, plain, crypt, md5, cert, hba, pam
auth_type = md5
auth_file = /etc/pgbouncer/userlist.txt

;;; Users allowed into database 'pgbouncer'

; comma-separated list of users, who are allowed to change settings
admin_users = mars_admin

; comma-separated list of users who are just allowed to use
;    SHOW command
stats_users = mars_admin
```

**Secret md5 file userlist**
```
cat /etc/pgbouncer/userlist.txt

"mars_admin" "md5a511c60150441d77e0ee7da4d8ebef6b"
"root" "md521232f297a57a5a743894a0e4a801fc3"
```



