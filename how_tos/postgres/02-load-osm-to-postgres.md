# Load OSM Data To Postgres

## Prerequisites

Install osm2pgsql

Install osmium
```bash
sudo apt-get install -y osmium-tool
```

## Extract From PBF Using Specific Bounding Box
```bash
cd data/osm
osmium extract --bbox=106.50028,-6.500447,107.100219,-5.913358 \
    -o greaterjakarta.osm.pbf \
    java-latest.osm.pbf
```

## Load to Postgres Using osm2pgsql

```bash
osm2pgsql \
--username $OSM2PGSQL_USER --database $OSM2PGSQL_DB --host $OSM2PGSQL_HOST --port $OSM2PGSQL_PORT --password \
--hstore  \
--slim --drop \
--cache 4000 \
--number-processes 2 \
--multi-geometry \
../../data/osm/greaterjakarta.osm.pbf
```

## Load Using pgosm flex

Create database
```sql
CREATE DATABASE pgosm;
```

Connect to that database and create extension
```sql
CREATE EXTENSION postgis;
CREATE ROLE pgosm_flex WITH LOGIN PASSWORD 'mysecretpassword';
CREATE SCHEMA osm AUTHORIZATION pgosm_flex;
GRANT CREATE ON DATABASE your_db_name
    TO pgosm_flex;
GRANT CREATE ON SCHEMA public
    TO pgosm_flex;
```

```bash
source .pgosm.env
```

Run the container with the additional environment variables.
```bash
mkdir pgosm-data
docker run --name pgosm -d --rm \
    -v ~/pgosm-data:/app/output \
    -v /etc/localtime:/etc/localtime:ro \
    -e POSTGRES_USER=$POSTGRES_USER \
    -e POSTGRES_PASSWORD=$POSTGRES_PASSWORD \
    -e POSTGRES_HOST=$POSTGRES_HOST \
    -e POSTGRES_DB=$POSTGRES_DB \
    -e POSTGRES_PORT=$POSTGRES_PORT \
    -p 15432:5432 -d rustprooflabs/pgosm-flex
```

Load data
```bash
docker exec -it \
    pgosm python3 docker/pgosm_flex.py \
    --ram=5 \
    --region=custom \
    --subregion=jakarta \
    --input-file=greaterjakarta.osm.pbf
```