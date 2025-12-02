# Lab 2 — Create a Custom Event-Driven Ansible Rulebook

## Overview

In this lab, you will learn how to design and implement your own **custom Event-Driven Ansible (EDA)** rulebook. Unlike Lab 1—where you ran a predefined rulebook—this exercise walks you through the full lifecycle of **planning**, **building**, and **testing** a rulebook tailored to a real automation scenario.

You will choose an event source, define rule logic, execute actions, and validate expected behavior. Completing this lab prepares you to create more advanced integrations in later modules.

## Learning Objectives

By the end of this lab, you will be able to:

- Understand how to design a **custom rulebook workflow**
- Configure **event sources** such as webhooks or file watchers
- Write your own **rules**, **conditions**, and **actions**
- Trigger and inspect rulebook behavior using actual events
- Follow best practices for reusable, maintainable EDA rulebooks

## 1. Designing Your Automation Scenario

Before writing YAML, first define the **automation intent** of your rulebook.

**Scenario:**  
When a webhook sends a notification containing a severity field, trigger different actions depending on whether the severity is *info*, *warning*, or *critical*.

Common use cases:

- Monitoring and observability alerts
- Ticket creation workflows
- GitOps or CI/CD notifications
- ChatOps events

## 2. Create the Custom Rulebook File

Create a new file:

```
custom-webhook-rulebook.yml
```

Add this production-ready rulebook:

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
      actions:
        - debug:
            msg: "🔥 Critical alert received: {{ event.payload.message }}"
        - run_job_template:
            name: "Restart Service"
            organization: "Default"

    - name: Handle warning events
      condition: event.payload.severity == "warning"
      action:
        debug:
          msg: "⚠️ Warning received: {{ event.payload.message }}"

    - name: Handle informational events
      condition: event.payload.severity == "info"
      action:
        debug:
          msg: "ℹ️ Info event received: {{ event.payload.message }}"

    - name: Handle unmatched events
      condition: event.payload.severity is not defined
      action:
        debug:
          msg: "Received event without severity: {{ event.payload }}"
```

### Key Notes

| Component | Purpose |
|----------|---------|
| **webhook source** | Listens for incoming HTTP requests |
| **rules** | Compare event payload fields |
| **actions** | Print messages or run job templates |
| **fallback rule** | Ensures predictable handling of unknown events |

## 3. Start the Rulebook Engine

Run:

```bash
ansible-rulebook -r custom-webhook-rulebook.yml -i localhost,
```

You will see the webhook server start and listen on port 5000.

## 4. Send Test Events

Open another terminal to trigger events.

### Critical event

```bash
curl -X POST -H "Content-Type: application/json"   -d '{"severity": "critical", "message": "Database unreachable"}'   http://localhost:5000
```

### Warning event

```bash
curl -X POST -H "Content-Type: application/json"   -d '{"severity": "warning", "message": "CPU usage high"}'   http://localhost:5000
```

### Info event

```bash
curl -X POST -H "Content-Type: application/json"   -d '{"severity": "info", "message": "Scheduled task completed"}'   http://localhost:5000
```

### Missing severity

```bash
curl -X POST -H "Content-Type: application/json"   -d '{"message": "Unknown format"}'   http://localhost:5000
```

## 5. Rule Evaluation Flow

1. Webhook receives the payload  
2. Event becomes `event.payload`  
3. Rules evaluated **top-to-bottom**  
4. First matching rule executes  
5. Processing ends for that event  

## 6. Best Practices

### Rulebook design
- Place specific rules before general rules  
- Group logically related behaviors  

### Event design
- Ensure consistent fields for routing decisions  
- Use clear identifiers  

### Security
- Do not expose webhook ports without firewalling  
- Use TLS or authentication where required  

### Maintainability
- Use Jinja templates for reusable components  
- Commit rulebooks to source control  

## 7. Summary

In this lab, you created a custom EDA rulebook that:

- Listens for webhook events  
- Routes events by severity  
- Executes different actions based on rule matching  
- Provides safe handling of unexpected event formats  

This structure is foundational for enterprise automation workflows.
