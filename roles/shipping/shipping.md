# Shipping Role Documentation

## Component Purpose
Shipping service is a Java backend that calculates shipping behavior and reads relational data from MySQL.

## Source Artifacts Used
- `shipping.yaml`
- `shipping.service`

## Inputs and Dependencies
- Inventory group: `shipping`
- Build/runtime tooling: `maven`, `mysql` client
- Python libs for Ansible DB module: `PyMySQL`, `cryptography`
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/shipping-v3.zip`
- External dependencies:
  - `DB_HOST=mysql.anuragaws.shop`
  - `CART_ENDPOINT=cart.anuragaws.shop:8080`

## Task-by-Task Breakdown
1. Install Maven and MySQL client
What: installs `maven` and `mysql` via DNF.
Why: Maven builds app JAR; mysql client supports related DB operations.

2. Install Python libraries
What: installs `PyMySQL` and `cryptography` using pip3.9.
Why: `community.mysql.mysql_db` requires Python DB drivers on target.

3. Create `roboshop` user
What: creates system user with no shell.
Why: service process isolation.

4. Remove `/app`
What: deletes existing deployment directory.
Why: ensures clean deployment.

5. Recreate `/app`
What: creates fresh app directory.
Why: staging location for artifact extraction.

6. Remove old shipping zip
What: deletes `/tmp/shipping.zip`.
Why: avoids stale artifact contamination.

7. Download shipping artifact
What: fetches `shipping-v3.zip`.
Why: retrieves deployment package.

8. Extract shipping artifact
What: unarchives into `/app`.
Why: places source/build files.

9. Build application
What: runs `mvn clean package` in `/app`.
Why: compiles code and produces JAR artifact.

10. Rename built JAR
What: moves `target/shipping-1.0.jar` to `shipping.jar`.
Why: aligns with unit `ExecStart` path `/app/shipping.jar`.

11. Install shipping systemd unit
What: copies `shipping.service` to `/etc/systemd/system/`.
Why: declares process startup with required environment.

12. Import SQL files into MySQL
What: imports schema and data files:
- `/app/db/schema.sql`
- `/app/db/app-user.sql`
- `/app/db/master-data.sql`
Why: bootstraps database structures and seed data required by service.

13. Reload daemon and start shipping
What: starts and enables service with daemon reload.
Why: runs newly built artifact under managed service lifecycle.

## Configuration and Environment Variables
From `shipping.service`:
- `CART_ENDPOINT=cart.anuragaws.shop:8080`
- `DB_HOST=mysql.anuragaws.shop`
- `ExecStart=/bin/java -jar /app/shipping.jar`

## Service Lifecycle
- Service name: `shipping`
- Boot: enabled
- Runtime logs tagged by `SyslogIdentifier=shipping`

## Data Bootstrap
- Explicit MySQL imports executed every run via loop.
- Uses root login:
  - host: `mysql.anuragaws.shop`
  - user: `root`
  - password: `RoboShop@1`

## Validation and Expected Results
- `systemctl status shipping` should be active/running.
- `mysql -h mysql.anuragaws.shop -u root -pRoboShop@1 -e "use cities; show tables;"`
  - Expected: schema objects exist (database/table names depend on SQL scripts).
- `curl http://shipping.anuragaws.shop:8080/` should return HTTP response.

## Risks and Operational Notes
- Build step (`mvn clean package`) increases deployment time and depends on external repos.
- DB import every run can be non-idempotent depending on SQL script content.
- Hardcoded root DB credentials and endpoints should be externalized.

