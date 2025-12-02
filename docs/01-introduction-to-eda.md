# 01 – Introduction to Event-Driven Ansible (EDA)

Event-Driven Ansible (EDA) is a modern automation paradigm within the **Ansible Automation Platform (AAP)** that enables systems to react **immediately and intelligently** to real-time events. Instead of waiting for scheduled playbook runs or manual triggers, EDA continuously monitors event sources, evaluates rules, and executes automated actions — all in milliseconds.

## 1. What is Event-Driven Ansible?

Event-Driven Ansible is a framework for creating **responsive, real-time automation** using:

- **Event Sources** (e.g., webhooks, monitoring tools, logs, message queues)
- **Rulebooks** (declarative rules that match events and specify actions)
- **Ansible Actions** (running playbooks, calling modules, sending notifications, etc.)

EDA continually listens for events, processes them, and triggers automation workflows that would otherwise require human intervention.

## 2. Why Event-Driven Automation?

Traditional Ansible is **pull- or schedule-based**.  
EDA introduces **push-style, event-reactive automation**, enabling:

### ✔ Real-time response
React automatically to outages, alerts, or infrastructure changes.

### ✔ Reduced manual operations
Eliminates repetitive tasks such as restarting services, scaling resources, or notifying teams.

### ✔ Operational efficiency
Automates responses to signals from monitoring tools like Dynatrace, Prometheus, Splunk, and ServiceNow.

### ✔ Improved reliability
Standardizes and automates workflows, reducing error and enforcing consistent practices.

## 3. Key Concepts in EDA

### 3.1 Event Sources
Event sources feed data into EDA, triggering rule evaluation.

Common examples:
- Webhooks
- Monitoring tool alerts
- Log/file watchers
- Message systems (Kafka, RabbitMQ)
- Network events
- Custom Python-based sources

### 3.2 Rules and Rulebooks

A **rulebook** defines:
- **Sources** (where events originate)
- **Rules** (conditions)
- **Actions** (automated responses)

Example:

```yaml
- name: Restart web service when CPU spikes
  hosts: localhost
  sources:
    - dynatrace.event:
        api_url: "{{ api_url }}"
        api_token: "{{ api_token }}"
  rules:
    - name: high CPU alert
      condition: event.title contains "CPU"
      actions:
        - run_playbook:
            name: restart_apache.yml
```

### 3.3 Actions

Actions include:
- Running playbooks
- Executing modules
- Sending notifications
- Calling REST APIs
- Writing logs or updating data stores

Actions can be synchronous or asynchronous.

## 4. Architecture Overview

Typical EDA architecture includes:

- **EDA Controller** — manages rulebooks and automation flow  
- **Rulebook Executor** — processes rules and triggers actions  
- **EDA API** — configuration and operational interface  
- **Event Bus / Message Queue** — optional middleware  
- **Integration Plugins** — extend event sources and action types  

Runs standalone or as part of enterprise AAP.

## 5. Common Use Cases

### Automated Incident Response
Automatically restart failed services, create tickets, notify teams.

### Self-Healing Infrastructure
Detect unhealthy conditions and automatically fix issues.

### Network Automation
Respond to:
- Interface failures
- Routing changes
- Firewall alerts

### Security & Compliance
Automate:
- Patch remediation  
- Host isolation  
- Policy enforcement  

### Workflow Automation
Trigger actions from:
- GitHub webhooks  
- Jira ticket creation  
- CI/CD events  

## 6. EDA vs. Classic Ansible

| Feature | Classic Ansible | Event-Driven Ansible |
|--------|------------------|----------------------|
| Trigger | Manual / Scheduled | Event-based |
| Focus | Playbooks | Rulebooks |
| Workflow | Human-driven | Signal-driven |
| Ideal For | Configuration mgmt | Real-time automation |

EDA complements — not replaces — classic Ansible.

## 7. Benefits for Developers & Operators

- Create reactive automation without complex scripting  
- Integrate automation across monitoring, security, network, and cloud tools  
- Use familiar Ansible syntax  
- Enable self-healing and autonomous operations  

## 8. What You Will Learn in This Repo

This learning series includes:
- Theory of event-driven architecture  
- Rulebook design  
- Hands-on labs  
- Working integrations  
- Architecture diagrams  
- Best practices & glossary  

By the end, you'll be able to build **production-ready real-time automation workflows**.

## 9. Further Reading

- Red Hat Event-Driven Ansible Documentation  
- Rulebook Reference  
- Source Plugin Guide  
- AAP Architecture Overview  
