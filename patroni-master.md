**Add user mars_admin**
```
sudo vim /etc/postgresql/13/main/pg_hba.conf
```
```
create database mars_db;
create user mars_admin;
ALTER USER mars_admin WITH ENCRYPTED PASSWORD 'mars_admin_password';
GRANT ALL PRIVILEGES ON DATABASE mars_db TO mars_admin;
GRANT USAGE ON SCHEMA pg_catalog TO mars_admin;
```
