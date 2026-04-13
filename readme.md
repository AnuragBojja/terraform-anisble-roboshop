# Ansible Roboshop Roles

## Overview
This repository is a role-based Ansible implementation for Roboshop. It uses `main.yaml` as a generic role runner and stores service logic in `roles/<service>/tasks/main.yaml` with shared helpers in `roles/common`.

## Project Artifacts
- `main.yaml`: generic playbook that runs role `{{ service_name }}` on host group `{{ service_name }}`.
- `inventory.ini`: host-group mapping and connection variables.
- `01-ec2-instance.yaml`: EC2 + Route53 bootstrap automation.
- `roles/`: role folders for each component.
- `group_vars/`: shared variables.

## Components
- Data and messaging: `mongodb`, `redis`, `mysql`, `rabbitmq`
- Application services: `cart`, `catalogue`, `user`, `shipping`, `payment`
- Edge service: `frontend`
- Shared role: `common`

## Execution Pattern
```bash
ansible-playbook -i inventory.ini main.yaml -e "service_name=mongodb"
ansible-playbook -i inventory.ini main.yaml -e "service_name=redis"
ansible-playbook -i inventory.ini main.yaml -e "service_name=mysql"
ansible-playbook -i inventory.ini main.yaml -e "service_name=rabbitmq"
ansible-playbook -i inventory.ini main.yaml -e "service_name=catalogue"
ansible-playbook -i inventory.ini main.yaml -e "service_name=user"
ansible-playbook -i inventory.ini main.yaml -e "service_name=cart"
ansible-playbook -i inventory.ini main.yaml -e "service_name=shipping"
ansible-playbook -i inventory.ini main.yaml -e "service_name=payment"
ansible-playbook -i inventory.ini main.yaml -e "service_name=frontend"
```

## Documentation Index
- `roles/common/common.md`
- `roles/mongodb/mongodb.md`
- `roles/redis/redis.md`
- `roles/mysql/mysql.md`
- `roles/rabbitmq/rabbitmq.md`
- `roles/cart/cart.md`
- `roles/catalogue/catalogue.md`
- `roles/user/user.md`
- `roles/shipping/shipping.md`
- `roles/payment/payment.md`
- `roles/frontend/frontend.md`
