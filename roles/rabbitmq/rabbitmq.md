# RabbitMQ Role Documentation

## Component Purpose
RabbitMQ handles asynchronous message flow used by the `payment` service.

## Source Artifacts Used
- `rabbitmq.yaml`
- `rabbitmq.repo`

## Inputs and Dependencies
- Inventory group: `rabbitmq`
- Repo file for Erlang + RabbitMQ packages
- Service name: `rabbitmq-server`
- Application credentials created: `roboshop` / `roboshop123`

## Task-by-Task Breakdown
1. Copy RabbitMQ repo file
What: copies `rabbitmq.repo` into `/etc/yum.repos.d/rabbitmq.repo`.
Why: ensures required package repositories and Erlang dependencies are available.

2. Install RabbitMQ server
What: installs `rabbitmq-server` package.
Why: installs message broker daemon and dependencies.

3. Start and enable RabbitMQ service
What: starts service and enables startup.
Why: broker must be running before app credentials are provisioned and used.

4. Create/update application user
What: creates `roboshop` user with full permissions on `/` vhost.
Why: payment service needs authenticated broker access for publish/consume flows.

## Configuration Details
- Repo file includes multiple baseurls for resiliency.
- Permission regex is broad (`.*`) for configure/read/write.

## Service Lifecycle
- Final expected state: `rabbitmq-server` active and enabled.

## Data Bootstrap
- Creates broker user and permissions; no queues/exchanges declared in this role.

## Validation and Expected Results
- `systemctl status rabbitmq-server` should be active/running.
- `rabbitmqctl list_users` should include `roboshop`.
- `rabbitmqctl list_permissions -p /` should show `roboshop` with regex permissions.

## Risks and Operational Notes
- Hardcoded credentials are security risk and should be vaulted.
- Wide permissions reduce least-privilege posture.
- `gpgcheck=0` in repo definitions weakens supply chain validation.

