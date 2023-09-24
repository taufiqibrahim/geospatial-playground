# Install PostgreSQL 16 on Ubuntu

## Configure the PostgreSQL Repository
```bash
sudo apt update -y
sudo apt install gnupg2 wget vim -y
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
sudo apt update -y
```

## Install PostgreSQL 16 on Ubuntu 22.04
```bash
sudo apt install -y postgresql-16-postgis-3
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo service postgresql start
```

## Edit Connection and Login

### /etc/postgresql/16/main/postgresql.conf
```
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

listen_addresses = '*'
```

### /etc/postgresql/16/main/pg_hba.conf
```
# IPv4 local connections:
...
# we add below line
host    all             all             0.0.0.0/0               md5
...
```

## Create Users
Create user with admin priviledge.
```
CREATE ROLE admin WITH LOGIN SUPERUSER CREATEDB CREATEROLE PASSWORD 'Passw0rd';
```

Create a user with permissions to manage the database
```
# create a test database, use the command
CREATE DATABASE demodb;

CREATE USER demouser WITH ENCRYPTED PASSWORD 'supersecret';
GRANT ALL PRIVILEGES ON DATABASE demodb TO demouser;
```
