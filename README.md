# The Ultimate Spring Boot Course

This repository contains the starter project for Part 2 of my Spring Boot course:

[https://codewithmosh.com/p/spring-boot-building-apis](https://codewithmosh.com/p/spring-boot-building-apis)

## About this Repository 

This project is based on the final project from Part 1 of the course, but I’ve cleaned it up and removed unnecessary playground code so we can focus on building APIs in Part 2.

You’ll be cloning this repository and coding along with me as we extend the project.

To get started, clone the repository to your local machine:

```sh
git clone https://github.com/mosh-hamedani/spring-api-starter

cd spring-api
```

---

## Connect IntelliJ IDEA Database tool to Docker MySQL

This project includes a MySQL database via Docker Compose and is already configured for Spring Boot and Flyway.

- Docker Compose file: `docker-compose.yml`
- Service name: `db`
- Image: `mysql:8.4`
- Mapped port: `3306:3306`
- Database: `spring-ecommerce`
- Root password: `password`

### 1) Start MySQL in Docker

From the project root, run:

```sh
docker compose up -d
# or: docker-compose up -d (older Docker versions)
```

Wait until the container is healthy and listening on port 3306. You can verify with:

```sh
docker ps
```

### 2) IntelliJ IDEA Database connection

In IntelliJ IDEA (Ultimate):

1. Open the Database tool window (View > Tool Windows > Database).
2. Click + (New) > Data Source > MySQL.
3. Fill in the connection details:
   - Host: `localhost`
   - Port: `3306`
   - User: `root`
   - Password: `password`
   - Database: `spring-ecommerce`
4. Click "Test Connection". IntelliJ will prompt you to download the MySQL driver if needed; allow it.
5. Click OK to save.

If your Docker engine runs in a VM (some Linux setups), use the VM IP instead of `localhost`.

### 3) Optional JDBC parameters

If you see timezone or SSL warnings, you can set the following in the Advanced tab or add to the URL:

```
?serverTimezone=UTC&useSSL=false
```

### 4) Create a dedicated MySQL user (optional)

By default only `root` is created (with password `password`). You can create an app user:

```sql
CREATE USER 'app'@'%' IDENTIFIED BY 'app_password';
GRANT ALL PRIVILEGES ON `spring-ecommerce`.* TO 'app'@'%';
FLUSH PRIVILEGES;
```

Then use `app` / `app_password` in IntelliJ or in `src/main/resources/application.yaml`.

### 5) Spring Boot configuration (already aligned)

`src/main/resources/application.yaml` is already set to connect to the Docker MySQL:

```
spring.datasource.url=jdbc:mysql://localhost:3306/spring-ecommerce?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=password
```

Flyway is enabled and migrations live under `src/main/resources/db/migration`. They will run on application start and also via the Flyway Maven plugin.

### 6) Troubleshooting

- Port already in use: Stop any local MySQL or change the host port in docker-compose: `"3307:3306"` and use port 3307 in IntelliJ.
- Connection refused: Ensure the container is running (`docker ps`) and that port 3306 is published.
- Auth plugin issues: MySQL 8 uses `caching_sha2_password` which IntelliJ’s driver supports. Ensure you let IntelliJ download the latest driver.
- Network firewalls/VPNs: Temporarily disable or create rules to allow localhost:3306.
