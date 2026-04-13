# Cart Role Documentation

## Component Purpose
The cart service is a Node.js backend component for shopping cart operations.  
It depends on `redis` for cache/state and `catalogue` for product information.

## Source Artifacts Used
- `cart.yaml`
- `cart.service`

## Inputs and Dependencies
- Inventory group: `cart`
- Runtime: Node.js 20
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/cart-v3.zip`
- External services from unit file:
  - `REDIS_HOST=redis.anuragaws.shop`
  - `CATALOGUE_HOST=catalogue.anuragaws.shop`
  - `CATALOGUE_PORT=8080`

## Task-by-Task Breakdown
1. Disable default Node.js module
What: `dnf module disable nodejs -y`.
Why: avoids old/default stream conflicts.

2. Enable Node.js 20 stream
What: `dnf module enable nodejs:20 -y`.
Why: pins runtime version expected by artifact.

3. Install Node.js
What: installs `nodejs` package.
Why: provides Node runtime and NPM ecosystem.

4. Create `roboshop` system user
What: creates non-login user with home `/app`.
Why: isolates service execution from normal users.

5. Remove `/app`
What: deletes existing app directory.
Why: guarantees clean deployment state.

6. Recreate `/app`
What: creates application directory.
Why: destination path for extracted artifact.

7. Remove old zip
What: deletes `/tmp/cart.zip`.
Why: avoids stale artifact conflicts.

8. Download artifact
What: fetches `cart-v3.zip` to `/tmp/cart.zip`.
Why: retrieves deployable build.

9. Extract artifact
What: unarchives zip into `/app`.
Why: places application source/runtime files.

10. Install npm dependencies
What: runs npm install via `community.general.npm`.
Why: resolves required Node modules.

11. Install systemd unit
What: copies `cart.service` to `/etc/systemd/system/cart.service`.
Why: defines process startup, user, and environment.

12. Reload daemon and start cart
What: `systemd_service` with `daemon_reload`, `enabled`, `started`.
Why: applies new unit and ensures service uptime.

## Configuration and Environment Variables
From `cart.service`:
- `REDIS_HOST`
- `CATALOGUE_HOST`
- `CATALOGUE_PORT`
- `ExecStart=/bin/node /app/server.js`

These variables connect cart to backing dependencies.

## Service Lifecycle
- Service name: `cart`
- Startup target: `multi-user.target`
- Syslog identifier: `cart`

## Data Bootstrap
- No DB schema import in this role.
- Service starts and initializes runtime behavior using external dependencies.

## Validation and Expected Results
- `systemctl status cart` should be active/running.
- `journalctl -u cart -n 50 --no-pager` should show normal startup.
- `curl http://cart.anuragaws.shop:8080/health` (if health endpoint exists)
  - Expected: success response (or at least no connection refusal).

## Risks and Operational Notes
- Full `/app` delete is destructive; local changes are removed on each run.
- Hardcoded upstream hostnames in unit file reduce portability.
- Service starts only after Redis and catalogue are reachable; sequencing matters.

