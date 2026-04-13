# MongoDB Role Documentation

## Component Purpose
MongoDB stores document data for Roboshop services, mainly `catalogue` and `user`.

## Source Artifacts Used
- `mongodb.yaml`
- `mongo.repo`

## Inputs and Dependencies
- Inventory group: `mongodb`
- Package source: custom `mongo.repo`
- Service name: `mongod`
- No direct app artifact download in this role

## Task-by-Task Breakdown
1. Copy MongoDB repo file
What: copies `mongo.repo` to `/etc/yum.repos.d/mongo.repo`.
Why: ensures the host can install the intended MongoDB version from the configured repository.

2. Install MongoDB package
What: installs `mongodb-org` via DNF.
Why: installs database binaries, service files, and client tools.

3. Start and enable MongoDB
What: starts `mongod` and enables it at boot.
Why: ensures DB is available now and after reboot.

4. Allow remote connections
What: replaces `127.0.0.1` with `0.0.0.0` in `/etc/mongod.conf`.
Why: allows other hosts (catalogue/user services) to connect over network.

5. Restart MongoDB
What: restarts `mongod`.
Why: applies bind configuration change made in previous step.

## Configuration Details
- Repo configuration in `mongo.repo` targets EL9 path for MongoDB 7.0.
- Bind address broadens exposure to all interfaces.

## Service Lifecycle
- Managed via `ansible.builtin.service`.
- Final expected state: `started`, `enabled: yes`.

## Data Bootstrap
- This role does not load application data.
- Data load is done from `catalogue` role when catalogue DB is absent.

## Validation and Expected Results
- `systemctl status mongod` should show active/running.
- `ss -lntp | grep 27017` should show MongoDB listening.
- Remote host check from app node:
  - `mongosh --host mongodb.anuragaws.shop --eval "db.runCommand({ping:1})"`
  - Expected: command returns `ok: 1`.

## Risks and Operational Notes
- Bind to `0.0.0.0` increases attack surface; limit access with security groups/firewall.
- `replace` is simple string replacement; if config format changes, task may stop being accurate.
- No authentication hardening is applied in this role.

