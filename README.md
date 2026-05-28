# Synapse server docker configuration

This is a docker configuration for the Synapse server. It uses the official Synapse image and mounts the configuration
file and data directory from the host.

## Usage

### 1. Create data directory and configuration file

Found a directory on your host machine to store the Synapse data. For example, you can create a directory called
`synapse-data` in your home directory. Then create the homeserver.yaml configuration file in that directory. You can use
the example configuration file provided by Synapse as an example:

```yaml
server_name: "guilledev.com"
pid_file: /data/homeserver.pid
listeners:
  - port: 8008
    tls: false
    type: http
    x_forwarded: true
    resources:
      - names: [ client, federation ]
        compress: false
database:
  name: psycopg2
  txn_limit: 10000
  args:
    user: synapse_user
    password: synapse_password
    database: synapse_db
    host: synapse_db
    port: 5432
    cp_min: 5
    cp_max: 10
log_config: "/data/guilledev.com.log.config"
media_store_path: "/data/media_store"
registration_shared_secret: "REDACTED"
report_stats: true
macaroon_secret_key: "REDACTED"
form_secret: "REDACTED"
signing_key_path: "/data/guilledev.com.signing.key"
trusted_key_servers:
  - server_name: "matrix.org"
```

### 2. Update log configuration file

The first time you run the container it will create the log configuration file at
`/data/family.guilledev.com.log.config`. This can lead to some permissions issues if the container can't access to the
file `/homeserver.log`. If is the case, change that configuration value to `/data/homeserver.log` and make sure the
container has permissions to write to that file.

### 3. Create the .env file

Create a `.env` file in the same directory as the `docker-compose.yml` file with the following content:

```env
SYNAPSE_DATA_PATH=/home/data

SYNAPSE_DB_USER=synapse_user
SYNAPSE_DB_PASSWORD=synapse_password
SYNAPSE_DB_NAME=synapse_db
SYNAPSE_DB_PATH=/home/db
```

### 4. Run the container

You can run the container using the following command:

```sh
docker compose up -d --build
```
