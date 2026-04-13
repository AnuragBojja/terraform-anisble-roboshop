# Frontend Role Documentation

## Component Purpose
Frontend role deploys static web assets and configures Nginx as reverse proxy for all backend API routes.

## Source Artifacts Used
- `frontend.yaml`
- `nginx.conf`

## Inputs and Dependencies
- Inventory group: `frontend`
- Package/runtime: Nginx 1.24 module stream
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/frontend-v3.zip`
- Upstream backends configured in Nginx:
  - `catalogue.anuragaws.shop:8080`
  - `user.anuragaws.shop:8080`
  - `cart.anuragaws.shop:8080`
  - `shipping.anuragaws.shop:8080`
  - `payment.anuragaws.shop:8080`

## Task-by-Task Breakdown
1. Disable default Nginx module
What: runs `dnf module disable nginx -y`.
Why: clears default stream before selecting specific version.

2. Enable Nginx 1.24 stream
What: enables `nginx:1.24`.
Why: enforces expected package version stream.

3. Install Nginx
What: installs `nginx` package.
Why: provides web server and reverse proxy capability.

4. Start and enable Nginx
What: starts service and enables boot start.
Why: ensures initial service availability.

5. Remove default HTML directory
What: deletes `/usr/share/nginx/html/`.
Why: removes any stale/default site content.

6. Recreate HTML directory
What: creates `/usr/share/nginx/html/`.
Why: prepares clean directory for static UI extract.

7. Download frontend artifact
What: downloads `frontend-v3.zip` to `/tmp/frontend.zip`.
Why: obtains build output for UI.

8. Extract frontend artifact
What: unarchives into `/usr/share/nginx/html/`.
Why: publishes static files served by Nginx.

9. Remove default Nginx config
What: deletes `/etc/nginx/nginx.conf`.
Why: replaces baseline config with application-specific reverse proxy config.

10. Copy custom Nginx config
What: copies repo `nginx.conf` to `/etc/nginx/nginx.conf`.
Why: configures API proxy paths and static behavior.

11. Restart Nginx
What: restarts and enables service.
Why: applies updated config and content.

## Configuration and Reverse Proxy Behavior
Key `nginx.conf` behavior:
- Serves static files from `/usr/share/nginx/html`.
- Provides `/health` endpoint via `stub_status`.
- Proxies:
  - `/api/catalogue/` -> `http://catalogue.anuragaws.shop:8080/`
  - `/api/user/` -> `http://user.anuragaws.shop:8080/`
  - `/api/cart/` -> `http://cart.anuragaws.shop:8080/`
  - `/api/shipping/` -> `http://shipping.anuragaws.shop:8080/`
  - `/api/payment/` -> `http://payment.anuragaws.shop:8080/`

## Service Lifecycle
- Service name: `nginx`
- Expected final state: restarted, running, enabled.

## Data Bootstrap
- Not a DB role; deploys static UI files only.

## Validation and Expected Results
- `nginx -t` should report syntax OK.
- `systemctl status nginx` should be active/running.
- Browser or curl checks:
  - `curl http://frontend.anuragaws.shop/` should return UI HTML.
  - `curl http://frontend.anuragaws.shop/health` should return Nginx status.

## Risks and Operational Notes
- Full removal of `/etc/nginx/nginx.conf` and html directory is destructive.
- Upstream names are hardcoded; environment changes need config edits.
- Any unavailable backend service will cause corresponding `/api/*` route failures.

