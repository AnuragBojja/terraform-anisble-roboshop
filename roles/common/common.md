# Common Role Documentation

## Component Purpose
The `common` role contains reusable task files shared by service roles to avoid repeating setup logic.

## Source Artifacts Used
- `roles/common/tasks/app-setup.yaml`
- `roles/common/tasks/nodejs.yaml`
- `roles/common/tasks/python.yaml`
- `roles/common/tasks/java.yaml`
- `roles/common/tasks/systemd.yaml`

## Reusable Task Modules
1. `app-setup.yaml`
- What: creates `roboshop` user, recreates `/app`, downloads and extracts `{{service_name}}-v3.zip`.
- Why: standardizes application bootstrap for all service roles.

2. `nodejs.yaml`
- What: enables Node.js 20 and runs npm install from `/app`.
- Why: shared runtime setup for Node services (`cart`, `catalogue`, `user`).

3. `python.yaml`
- What: installs Python toolchain and pip dependencies from `requirements.txt`.
- Why: reusable install path for Python-based services (`payment`).

4. `java.yaml`
- What: installs Maven/mysql client, builds JAR, renames output to `shipping.jar`.
- Why: shared Java build flow for `shipping` service.

5. `systemd.yaml`
- What: templates `{{service_name}}.service.j2` into systemd, reloads and restarts service.
- Why: centralizes service management behavior.

## Inputs and Variables
- `service_name` is required by all common task files.
- Selected roles may additionally depend on role-specific variables from `roles/<role>/vars/main.yaml`.

## Validation and Expected Results
- App setup should leave extracted code in `/app`.
- Runtime-specific tasks should install dependencies without error.
- Systemd task should result in `systemctl status {{service_name}}` active/running.

## Risks and Operational Notes
- `/app` is removed on each run by `app-setup.yaml`; local/manual changes are lost.
- Artifact URL and naming convention are hardcoded to `{{service_name}}-v3.zip`.
- `command`-based steps in `nodejs.yaml` and `java.yaml` are less idempotent than dedicated modules.

