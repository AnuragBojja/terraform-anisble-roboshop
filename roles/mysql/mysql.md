# MySQL Role Documentation

## Component Purpose
MySQL stores relational data consumed by the `shipping` service.

## Source Artifacts Used
- `mysql.yaml`

## Inputs and Dependencies
- Inventory group: `mysql`
- Package: `mysql-server`
- Service name: `mysqld`
- Root password target: `RoboShop@1` (hardcoded in current playbook)

## Task-by-Task Breakdown
1. Install MySQL server
What: installs `mysql-server`.
Why: provides DB daemon and tooling required for shipping schema/data.

2. Start and enable MySQL service
What: starts `mysqld`, enables boot start.
Why: ensures DB is online and persistent across host restart.

3. Set root password
What: executes `mysql_secure_installation --set-root-pass RoboShop@1`.
Why: configures root credentials used later by shipping DB import tasks.

## Configuration Details
- Root password is set through command execution, not secret manager integration.
- No TLS configuration or restricted bind settings are included in this role.

## Service Lifecycle
- Final expected state: `mysqld` running and enabled.

## Data Bootstrap
- Schema and data import are not in this role.
- Import happens in `shipping.yaml` via `community.mysql.mysql_db`.

## Validation and Expected Results
- `systemctl status mysqld` should show active/running.
- `mysql -h mysql.anuragaws.shop -u root -pRoboShop@1 -e "show databases;"`
  - Expected: command succeeds and lists system databases.

## Risks and Operational Notes
- Command-based root password setup may not be idempotent across reruns.
- Hardcoded root password is a security risk; should be vaulted/templated.
- No least-privilege application user creation here.

