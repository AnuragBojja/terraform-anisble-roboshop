# catalogue Role Documentation

## Component Purpose
The catalogue service is a Node.js backend API that serves product catalog data from MongoDB.

## Source Artifacts Used
- `catalogue.yaml`
- `catalogue.service`
- `mongo.repo`

## Inputs and Dependencies
- Inventory group: `catalogue`
- Runtime: Node.js 20
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/catalogue-v3.zip`
- External DB: `mongodb.anuragaws.shop`
- Mongo shell client package: `mongodb-mongosh`

## Task-by-Task Breakdown
1. Disable default Node.js module
What: disables generic Node.js module.
Why: avoids stream conflicts.

2. Enable Node.js 20 module
What: enables `nodejs:20`.
Why: ensures compatible runtime.

3. Install Node.js
What: installs Node package.
Why: provides app execution runtime.

4. Create `roboshop` user
What: creates system, no-login user.
Why: runs service with non-interactive account.

5. Remove `/app`
What: deletes old app directory.
Why: forces clean deployment.

6. Recreate `/app`
What: creates deployment directory.
Why: destination for extracted artifact.

7. Remove old artifact
What: deletes `/tmp/catalogue.zip`.
Why: avoids stale package use.

8. Download artifact
What: downloads `catalogue-v3.zip`.
Why: retrieves release bundle.

9. Extract artifact
What: unarchives into `/app`.
Why: places application files.

10. Install npm dependencies
What: executes npm install in `/app`.
Why: resolves Node dependencies.

11. Install service unit
What: copies `catalogue.service` to systemd path.
Why: declares how app is started and configured.

12. Install Mongo repo config
What: copies `mongo.repo` to YUM repo directory.
Why: required to install mongosh client package.

13. Install Mongo shell client
What: installs `mongodb-mongosh`.
Why: used to inspect DB existence and load seed data.

14. Check if `catalogue` database exists
What: runs mongosh expression and registers result.
Why: keeps data-load conditional, avoiding repeated imports.

15. Load master data conditionally
What: executes `master-data.js` only when DB is absent.
Why: bootstraps initial product data for first-time deployments.

16. Reload daemon and start catalogue
What: enables and starts systemd service.
Why: activates backend API after code/data prep.

## Configuration and Environment Variables
From `catalogue.service`:
- `MONGO=true`
- `MONGO_URL="mongodb://mongodb.anuragaws.shop:27017/catalogue"`
- `ExecStart=/bin/node /app/server.js`

## Service Lifecycle
- Service name: `catalogue`
- Boot behavior: enabled
- Logs tagged with `SyslogIdentifier=catalogue`

## Data Bootstrap
- Conditional data load from `/app/db/master-data.js`.
- Idempotency logic relies on DB-name index result comparison.

## Validation and Expected Results
- `systemctl status catalogue` should be active/running.
- DB existence check:
  - `mongosh --host mongodb.anuragaws.shop --eval "show dbs"`
  - Expected: `catalogue` listed.
- API reachability:
  - `curl http://catalogue.anuragaws.shop:8080/`
  - Expected: HTTP response without connection error.

## Risks and Operational Notes
- Naming mismatch exists (`catalogue` in playbook host vs `catalogue` in service binary naming); keep consistent with current files.
- Uses `shell` for import and `command` for DB check, so strict idempotency depends on command output format.
- Hardcoded Mongo hostname limits environment portability.

