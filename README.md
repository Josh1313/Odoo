# Odoo Installation on Google Cloud with Docker and Nginx

### Important Note:
To ensure the application functions correctly, you must open the required ports for communication. For the Odoo app, ensure the following ports are open:
- **Port 9069**: To access the Odoo web interface.
- **Port 9072**: For WebSocket communication.

Additionally, the database password in the `config` file must match the one in the Docker secrets. The default password is `datapathfinder`, but you can change it to your preference. Remember to update the configuration accordingly.

---

### Docker Compose Configuration
Here is the `docker-compose.yml` file setup:

```yaml
services:
  web:
    image: odoo:latest
    depends_on:
      - db
    restart: always
    ports:
      - "9069:8069" # Odoo port - your app will be accessible on this port
      - "9072:8072" # Odoo WebSocket port for communication
    volumes:
      - odoo-web-data-project:/var/lib/odoo
      - ./config:/etc/odoo
      - ./addons:/mnt/extra-addons
    environment:
      - PASSWORD_FILE=/run/secrets/postgresql_password
    secrets:
      - postgresql_password

  db:
    image: postgres:latest
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_PASSWORD_FILE=/run/secrets/postgresql_password
      - POSTGRES_USER=odoo
      - PGDATA=/var/lib/postgresql/data/pgdata
    restart: always
    volumes:
      - odoo-db-data-project:/var/lib/postgresql/data
    secrets:
      - postgresql_password

volumes:
  odoo-web-data-project:
  odoo-db-data-project:

secrets:
  postgresql_password:
    file: odoo_pass
```

### User Configuration
To create a user and configure your Odoo instance:
1. Open the `odoo.conf` file in the `./config` directory.
2. Update the `db_password` field to match your chosen password.
3. Restart the Docker Compose services to apply the changes.

configuration:
```ini
[options]
addons_path = /mnt/extra-addons
admin_passwd = admin
db_host = db
db_port = 5432
db_user = odoo
db_password = datapathfinder
proxy_mode = True
```

Once these steps are complete, access your Odoo app via `http://<your-domain>:9069`.

