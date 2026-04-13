# User Role Documentation

## Component Purpose
The user service is a Node.js backend handling user-related operations and sessions, using MongoDB and Redis.

## Source Artifacts Used
- `user.yaml`
- `user.service`

## Inputs and Dependencies
- Intended inventory group: `user`
- Runtime: Node.js 20
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/user-v3.zip`
- External services from unit file:
  - `MONGO_URL="mongodb://mongodb.anuragaws.shop:27017/users"`
  - `REDIS_URL='redis://redis.anuragaws.shop:6379'`

## Task-by-Task Breakdown
1. Disable default Node.js module
What: disables existing module stream.
Why: prepares for explicit Node 20 enablement.

2. Enable Node.js 20
What: enables `nodejs:20`.
Why: runtime version alignment with application.

3. Install Node.js
What: installs `nodejs`.
Why: provides interpreter and package manager support.

4. Create `roboshop` system user
What: creates non-login user with `/app` home.
Why: least-privilege process ownership model.

5. Remove `/app`
What: deletes prior app directory.
Why: ensures clean deployment each run.

6. Recreate `/app`
What: creates directory for deployment.
Why: required extraction path.

7. Remove old zip
What: deletes `/tmp/user.zip`.
Why: prevents stale deployment artifacts.

8. Download user artifact
What: downloads `user-v3.zip`.
Why: obtains packaged application.

9. Extract artifact
What: unarchives to `/app`.
Why: places runnable code on host.

10. Install npm dependencies
What: npm install in `/app`.
Why: resolves module dependencies.

11. Install service unit
What: copies `user.service` to systemd path.
Why: defines runtime settings and process startup.

12. Reload daemon and start user service
What: enables and starts `user`.
Why: activates service with new configuration.

## Configuration and Environment Variables
From `user.service`:
- `MONGO=true`
- `REDIS_URL`
- `MONGO_URL`
- `ExecStart=/bin/node /app/server.js`

## Service Lifecycle
- Service name: `user`
- Enabled for boot and started immediately.

## Data Bootstrap
- No explicit schema import in this playbook.
- Application is expected to initialize/use required collections at runtime.

## Validation and Expected Results
- `systemctl status user` should be active/running.
- `journalctl -u user -n 50 --no-pager` should show successful startup.
- Dependency reachability from user host:
  - `nc -z mongodb.anuragaws.shop 27017`
  - `nc -z redis.anuragaws.shop 6379`

## Risks and Operational Notes
- Current `user.yaml` does not declare `hosts:` explicitly; this is a functional risk and should be corrected in code.
- `/app` directory wipe is destructive on each run.
- Hardcoded infrastructure endpoints reduce reuse across environments.

