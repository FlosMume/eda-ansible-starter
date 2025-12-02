# Lab 2 — Create a Custom Rulebook (Revised, Fully Working Version)

## Overview
This lab teaches you how to build a **custom Event-Driven Ansible (EDA) rulebook** that listens for webhook events and triggers a **local playbook**.  
Unlike earlier versions of this lab, this revised version:

- Works **entirely locally**
- Requires **no Controller (AWX/AAP)**
- Requires **no sudo**
- Uses a safe **simulated playbook action**
- Has fully validated JSON → rulebook → playbook integration

Everything runs cleanly on WSL, Linux, or macOS.

---

## Learning Objectives

You will learn to:

- Configure a webhook event source  
- Build rules that evaluate JSON fields  
- Pass webhook payloads to playbooks  
- Safely simulate a service restart using a local file  
- Trigger automation with `curl`

---

## Project Structure

```
eda-ansible-starter/
│
├── rulebooks/
│   └── custom-webhook-rulebook.yml
│
├── playbooks/
│   └── restart-service.yml
│
└── inventory.ini
```

---

# 1. Create the Playbook (Safe, Non-Root)

`playbooks/restart-service.yml`

```yaml
---
- name: Simulate restarting a service on the target host
  hosts: "{{ target_host }}"
  gather_facts: no

  tasks:
    - name: Log simulated restart
      ansible.builtin.debug:
        msg: "Simulated restart of {{ service_name }} on {{ inventory_hostname }}"

    - name: Append to log file so you can verify execution
      ansible.builtin.lineinfile:
        path: /tmp/eda_service_restart.log
        create: yes
        line: "Simulated restart of {{ service_name }} at {{ lookup('pipe', 'date') }}"
```

This avoids systemd or sudo issues and works on all machines.

---

# 2. Create the Rulebook

`rulebooks/custom-webhook-rulebook.yml`

```yaml
---
- name: Custom Webhook Rulebook
  hosts: localhost

  sources:
    - ansible.eda.webhook:
        host: 0.0.0.0
        port: 5000

  rules:

    - name: Handle critical events
      condition: event.payload.severity == "critical"
                 and event.payload.host is defined
                 and event.payload.service is defined
      actions:
        - debug:
            msg: "🔥 Critical alert received: {{ event.payload.message }}"

        - run_playbook:
            name: playbooks/restart-service.yml
            extra_vars:
              target_host: "{{ event.payload.host }}"
              service_name: "{{ event.payload.service }}"

    - name: Handle non-critical events
      condition: event.payload.severity != "critical"
      action:
        debug:
          msg: "ℹ️ Non-critical event received: {{ event.payload }}"
```

---

# 3. Create the Inventory File

`inventory.ini`

```ini
[all]
localhost ansible_connection=local
```

---

# 4. Run the Rulebook

Terminal 1:

```bash
ansible-rulebook -r rulebooks/custom-webhook-rulebook.yml -i inventory.ini
```

Leave this running.  
This starts the webhook server.

---

# 5. Trigger Automation With JSON Events

## Critical Event (Triggers the Playbook)

Terminal 2:

```bash
curl -X POST -H "Content-Type: application/json"   -d '{
        "severity": "critical",
        "host": "localhost",
        "service": "cron",
        "message": "Database unreachable"
      }'   http://localhost:5000
```

Expected in Terminal 1:

```
🔥 Critical alert received: Database unreachable
```

And Ansible will execute your playbook.

### Verify the result:

```bash
cat /tmp/eda_service_restart.log
```

You should see a timestamped log entry.

---

## Warning Event (No Playbook Triggered)

```bash
curl -X POST -H "Content-Type: application/json"   -d '{
        "severity": "warning",
        "message": "Disk usage high"
      }'   http://localhost:5000
```

Expected output:

```
ℹ️ Non-critical event received: {...}
```

---

# 6. Summary

In this revised Lab 2, you successfully:

- Built and executed a **custom EDA rulebook**
- Used webhook events as event sources
- Passed JSON fields into playbook variables
- Safely simulated automated remediation actions
- Verified execution through `/tmp/eda_service_restart.log`

This version works reliably on any local machine without requiring elevated privileges or external services.

---

# Next Steps

Proceed to **Lab 3** to integrate playbooks more deeply,  
or ask for a **combined ZIP** of Labs 1–5 for GitHub.
