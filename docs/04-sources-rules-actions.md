# 04 – Sources, Rules, and Actions in Event-Driven Ansible (EDA)

In Event-Driven Ansible (EDA), **sources**, **rules**, and **actions** form the core building blocks of event-driven automation.  
Together, they describe *where events come from*, *how EDA interprets them*, and *what automated responses are executed*.

---

# 1. Sources: Where Events Come From

A **source** is any system, tool, or service that emits events to EDA. Sources continuously feed data into the EDA rule engine.

## 1.1 Common Source Types

### Built-in Event Sources
- **webhook** – receives events via HTTP  
- **file_watch** – watches for file changes  
- **kafka** – consumes Kafka topic messages  
- **rabbitmq** – consumes message queue data  
- **timer** – emits scheduled/time-based events  
- **process** – executes a command and streams stdout as events  

### Monitoring Integrations
- Dynatrace (`dynatrace.event`)
- Prometheus Alertmanager
- Splunk / Elastic / SIEM webhooks

## 1.2 Example Source Definition

```yaml
sources:
  - ansible.eda.webhook:
      host: 0.0.0.0
      port: 5000
```

---

# 2. Rules: How EDA Interprets Events

A **rule** evaluates incoming events and determines whether automation should run.

Every rule contains:
1. A **name**  
2. A **condition**  
3. One or more **actions**  

## 2.1 Example Rule

```yaml
rules:
  - name: react_to_file_change
    condition: event.change == "modified"
    actions:
      - run_playbook:
          name: handle_file_modification.yml
```

---

## 2.2 Condition Syntax

Supported operators:

### String Operators
- `contains`
- `==`, `!=`
- `startswith`, `endswith`

### Logical Operators
- `and`, `or`, `not`

### Numeric Operators
- `>`, `<`, `>=`, `<=`

### Existence Check
```
event.field is defined
```

### Example:

```yaml
condition: event.severity == "CRITICAL" and event.host contains "web"
```

---

# 3. Actions: What Happens When a Rule Matches

Actions describe the automated response to a matched rule.

## 3.1 Common Action Types

| Action | Description |
|--------|-------------|
| **run_playbook** | Executes an Ansible playbook |
| **run_module** | Runs a single Ansible module |
| **set_fact** | Stores dynamic variables |
| **debug** | Prints troubleshooting info |
| **post_event** | Sends a new event to EDA |
| **shutdown** | Stops the rulebook |

## 3.2 Example Action Block

```yaml
actions:
  - run_playbook:
      name: restart_service.yml
  - debug:
      msg: "Service restarted due to threshold breach."
```

---

# 4. Complete Example: Sources + Rules + Actions

```yaml
- name: log_monitoring_rulebook
  hosts: localhost

  sources:
    - ansible.eda.file_watch:
        paths:
          - /var/log/app.log

  rules:
    - name: detect_log_change
      condition: event.change == "modified"
      actions:
        - run_playbook:
            name: restart_application.yml
        - debug:
            msg: "Log file changed → application restarted"
```

---

# 5. Best Practices

✔ Keep rulebooks readable and modular  
✔ Use playbooks for complex workflows  
✔ Test new sources using `debug: var=event`  
✔ Avoid overly complex conditions inside rulebooks  
✔ Ensure observability and logging are enabled  

---

# 6. Summary

| Component | Description |
|----------|-------------|
| **Sources** | Provide event streams (webhooks, logs, monitoring tools, message queues) |
| **Rules** | Interpret events and evaluate conditions |
| **Actions** | Trigger automated responses such as running playbooks |

Together, they implement the **event → logic → automation** lifecycle that powers Event-Driven Ansible.

---
