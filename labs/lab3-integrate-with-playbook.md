# Lab 3 — Integrate Event-Driven Ansible Rulebooks with Playbooks

## Overview

In this lab, you will learn how to connect **Event-Driven Ansible (EDA)** rulebooks with traditional **Ansible playbooks**. This is one of the most common patterns in enterprise automation: an event triggers a rule, the rule evaluates conditions, and a **playbook is executed to take action** on remote systems.

This lab guides you through:
- Designing a rulebook that calls a playbook  
- Passing event data into the playbook  
- Running and validating the integrated workflow  

By the end of this lab, you will understand how EDA extends classic automation by adding **event responsiveness**.

## Learning Objectives

After completing this lab, you will be able to:

- Build rulebooks that trigger Ansible playbooks  
- Use event payloads as variables within playbooks  
- Understand how the `run_playbook` action works  
- Validate rulebook–playbook interactions end to end  
- Apply best practices for integrating event-driven and configuration-driven automation

## 1. Why Use Playbooks with Rulebooks?

Rulebooks are designed to evaluate events and make decisions.  
Playbooks are designed to **configure, remediate, or operate** systems.

EDA bridges these two layers:

| Component | Role |
|----------|------|
| **Rulebook** | Listens for events, evaluates logic, decides actions |
| **Playbook** | Performs automation tasks on managed hosts |

This keeps automation predictable, modular, and maintainable.

## 2. Example Use Case

You will implement a workflow where:

> A webhook sends an event with a hostname and service name.  
> If `severity == "critical"`, the rulebook triggers a playbook to restart the service.

This mirrors typical DevOps/SRE remediation patterns.

## 3. Create the Supporting Playbook

Create:

```
restart-service.yml
```

```yaml
---
- name: Restart a service on the target host
  hosts: "{{ target_host }}"
  gather_facts: no

  vars:
    service_name: "{{ service_name }}"

  tasks:
    - name: Restarting the service
      ansible.builtin.service:
        name: "{{ service_name }}"
        state: restarted

    - name: Confirm service status
      ansible.builtin.service_facts:
      register: svc_facts

    - name: Output service state
      debug:
        msg: "Service {{ service_name }} is now {{ svc_facts.ansible_facts.services[service_name].state }}"
```

## 4. Create the Rulebook

Create:

```
rulebook-integrate-with-playbook.yml
```

```yaml
---
- name: Integrate Rulebook with Playbook
  hosts: localhost

  sources:
    - ansible.eda.webhook:
        host: 0.0.0.0
        port: 5001

  rules:

    - name: Trigger restart on critical events
      condition: event.payload.severity == "critical"
      action:
        run_playbook:
          name: restart-service.yml
          extra_vars:
            target_host: "{{ event.payload.host }}"
            service_name: "{{ event.payload.service }}"
```

## 5. Start the Rulebook Engine

```bash
ansible-rulebook -r rulebook-integrate-with-playbook.yml -i inventory.ini
```

Sample `inventory.ini`:

```
[all]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.20
```

## 6. Trigger the Workflow

### Critical event

```bash
curl -X POST -H "Content-Type: application/json"   -d '{
        "severity": "critical",
        "host": "server1",
        "service": "nginx"
      }'   http://localhost:5001
```

This should:
- Trigger the rule  
- Execute the playbook  
- Restart the service on the target host  

### Non-critical test

```bash
curl -X POST -H "Content-Type: application/json"   -d '{"severity": "warning", "host": "server1", "service": "nginx"}'   http://localhost:5001
```

Expected: No action taken.

## 7. Data Flow Summary

| Stage | Description |
|-------|-------------|
| **Webhook** | Sends JSON payload |
| **Rulebook** | Evaluates condition and triggers action |
| **Playbook** | Performs remediation on target systems |

## 8. Best Practices

### Event Handling
- Validate payload fields  
- Add fallback rules  

### Security
- Restrict or authenticate webhook listeners  
- Validate event input before passing it to playbooks  

### Playbook Design
- Remain idempotent and predictable  
- Log actions for audits  

### Rulebook Architecture
- Keep rules lightweight; delegate work to playbooks  
- Use meaningful variable names and consistent structures  

## 9. Summary

You built an end-to-end integration where:
- A **rulebook** listens for webhook events  
- A **condition** filters based on severity  
- A **playbook** responds to a critical alert  
- Event data flows automatically from webhook → rulebook → playbook → target systems  

This event-driven remediation pattern is foundational in modern automation platforms.
