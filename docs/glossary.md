# Glossary – Event-Driven Ansible (EDA)

This glossary defines essential terminology used throughout Event-Driven Ansible (EDA), including rulebooks, events, sources, architecture, and automation workflows.

---

## A

### Action
A task executed by EDA when a rule’s condition evaluates to true. Examples include running a playbook, executing a module, sending a webhook, or printing debug output.

### Ansible Automation Platform (AAP)
Red Hat’s enterprise automation suite including Automation Controller, EDA Controller, execution environments, and governance tools.

---

## C

### Condition
A logical expression evaluated against the incoming event payload. If the condition returns *true*, the corresponding actions are triggered.

### Controller
Refers to Automation Controller or EDA Controller. Provides RBAC, logging, clustering, credential management, and centralized workflow orchestration.

---

## D

### Debug Action
An EDA action used to print event data or messages for troubleshooting or event-structure validation.

---

## E

### EDA (Event-Driven Ansible)
A real-time automation framework that listens for events, evaluates rule logic, and triggers automated actions using Ansible.

### EDA Controller
AAP component that manages rulebooks, event workers, event streams, and observability for event-driven automation.

### Event
A structured message—typically JSON—representing a change or signal from a system, service, or device.

### Event Payload
The data content of an event. Accessible in rulebooks using the `event.<field>` syntax.

### Event Source
Any system, plugin, or integration that emits events to EDA.

---

## F

### Fact
A dynamic variable created during rule execution using `set_fact`. Useful for stateful logic and passing values to actions or playbooks.

---

## I

### Integration
Any external system connected to EDA—such as monitoring tools, SIEMs, ITSM systems, message brokers, or cloud services.

---

## M

### Message Broker
Middleware used for distributing, buffering, or scaling event streams. Examples: Redis Streams, Kafka, RabbitMQ.

### Module (Ansible Module)
A single unit of Ansible automation executed via the `run_module` action.

---

## P

### Playbook
A YAML-defined automation workflow executed on managed hosts. Commonly triggered by EDA as part of remediation.

### Producer
Any source plugin or external service generating events for EDA.

---

## R

### Redis
A high-performance in-memory data store often used as a message broker or event buffer in scalable EDA deployments.

### Remediation
Automated corrective response triggered when certain event conditions are met (e.g., restarting a service).

### Rule
A block of logic in a rulebook containing a name, a condition, and one or more actions.

### Rulebook
A YAML document defining event sources, rules, conditions, and associated actions.

### Rulebook Engine
The runtime component that listens for events, evaluates rules, and executes actions.

---

## S

### Self-Healing
Automation pattern where infrastructure issues trigger automatic remediation through EDA and Ansible playbooks.

### Source Plugin
A plugin that enables EDA to consume events from systems such as webhooks, log watchers, Kafka, or Dynatrace.

---

## T

### Timer Source
A built-in EDA source that emits periodic events—functionally similar to cron jobs.

### Trigger
Any event or condition that initiates an EDA action.

---

## W

### Webhook
An HTTP endpoint used by external systems to send real-time events to EDA.

### Worker (EDA Worker)
A runtime process used in EDA Controller deployments to execute rulebooks at scale.

---

# Summary

This glossary provides standardized definitions for key Event-Driven Ansible terms, supporting understanding of rulebooks, events, architecture, integrations, and real-time automation design.

