# Payment Role Documentation

## Component Purpose
Payment service is a Python backend component that coordinates cart/user interactions and publishes/consumes through RabbitMQ.

## Source Artifacts Used
- `payment.yaml`
- `payment.service`

## Inputs and Dependencies
- Inventory group: `payment`
- Runtime/build deps: `python3`, `gcc`, `python3-devel`
- Artifact: `https://roboshop-artifacts.s3.amazonaws.com/payment-v3.zip`
- External dependency endpoints from unit file:
  - `CART_HOST=cart.anuragaws.shop`
  - `USER_HOST=user.anuragaws.shop`
  - `AMQP_HOST=rabbitmq.anuragaws.shop`

## Task-by-Task Breakdown
1. Install Python build/runtime packages
What: installs `python3`, `gcc`, `python3-devel`.
Why: supports Python runtime and native dependency compilation.

2. Create `roboshop` system user
What: creates system account with `/app` home and no shell.
Why: standard service user model for isolation.

3. Remove `/app`
What: deletes existing directory.
Why: clean deployment baseline.

4. Recreate `/app`
What: creates deployment path.
Why: extraction destination.

5. Remove old artifact
What: deletes `/tmp/payment.zip`.
Why: avoids stale package reuse.

6. Download payment artifact
What: retrieves `payment-v3.zip`.
Why: pulls deployable service content.

7. Extract artifact
What: unarchives zip into `/app`.
Why: places source and runtime files.

8. Install Python dependencies
What: runs pip with `/app/requirements.txt` using pip3.9.
Why: installs app-required Python packages.

9. Install service unit
What: copies `payment.service` to systemd directory.
Why: defines environment and startup command.

10. Reload daemon and start payment
What: enables and starts `payment` with daemon reload.
Why: activates service using latest unit configuration.

## Configuration and Environment Variables
From `payment.service`:
- `CART_HOST`, `CART_PORT`
- `USER_HOST`, `USER_PORT`
- `AMQP_HOST`, `AMQP_USER`, `AMQP_PASS`
- `WorkingDirectory=/app`
- `ExecStart=/usr/local/bin/uwsgi --ini payment.ini`

## Service Lifecycle
- Service name: `payment`
- Runs as `User=root` in current unit file.
- Enabled at boot and started after deployment.

## Data Bootstrap
- No direct DB schema import in this role.
- Runtime relies on upstream services and broker availability.

## Validation and Expected Results
- `systemctl status payment` should be active/running.
- `journalctl -u payment -n 50 --no-pager` should show uWSGI startup.
- Port and dependency checks:
  - `nc -z rabbitmq.anuragaws.shop 5672`
  - `nc -z cart.anuragaws.shop 8080`
  - `nc -z user.anuragaws.shop 8080`

## Risks and Operational Notes
- Service runs as root in current unit file; this is a privilege risk.
- Hardcoded broker credentials should move to secrets management.
- Dependency failures in cart/user/rabbitmq will surface as payment startup/runtime errors.

