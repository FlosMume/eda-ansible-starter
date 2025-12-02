# 02 – Rulebooks Theory: The Foundation of Event-Driven Ansible (EDA)

Rulebooks are at the core of **Event-Driven Ansible (EDA)**. They define **how events are consumed**, **how logic is evaluated**, and **what automated actions are executed**.  
If events are the “signals,” then **rulebooks are the “brain”** that interprets those signals and decides what to do.

This chapter provides a complete conceptual and practical foundation for understanding, designing, and implementing EDA rulebooks.

---

# 1. What Is a Rulebook?

A **rulebook** is a YAML file that describes *event-processing logic* in three main components:

1. **Event Sources** – Where events originate  
2. **Rules** – Conditions that evaluate events  
3. **Actions** – What EDA should do when conditions are true  

Rulebooks are executed by the **Rulebook Executor**, which listens continuously to event sources and triggers automation whenever rules match.

---

# 2. Structure of a Rulebook

A typical rulebook has the following high-level structure:

```yaml
- name: my_rulebook
  hosts: localhost
  sources:
    - <event_source_plugin>: <source_configuration>

  rules:
    - name: <rule_name>
      condition: <boolean_expression>
      actions:
        - <action_type>: <parameters>
```

### Required Sections:
| Section | Purpose |
|--------|---------|
| `name` | Logical name of the rulebook |
| `hosts` | Target hosts for actions |
| `sources` | Event source plugins & configuration |
| `rules` | List of conditional logic and actions |

---

# 3. Event Sources

Event sources are **plugins** that stream events into the rulebook.

Common examples:
- Webhooks  
- Monitoring alerts  
- File/watch streams  
- Kafka or RabbitMQ messages  
- Network or security events  
- Custom Python event emitters

Example:

```yaml
sources:
  - ansible.eda.webhook:
      host: 0.0.0.0
      port: 5000
```

---

# 4. Rules: Logic & Conditions

Each **rule** describes *when* to take action.

A rule has:
- A **name**
- A **condition**
- One or more **actions**

Example:

```yaml
rules:
  - name: restart_on_cpu_alert
    condition: event.title contains "CPU"
    actions:
      - run_playbook:
          name: fix_cpu_issue.yml
```

---

## 4.1 Condition Syntax

Supported operators include:

### String operators
- `contains`
- `==`
- `!=`
- `startswith`
- `endswith`

### Logical operators
- `and`
- `or`
- `not`

### Numeric comparison
`>`, `<`, `>=`, `<=`

### Existence check
`event.field is defined`

Example:

```yaml
condition: event.severity == "CRITICAL" and event.value > 90
```

---

# 5. Actions

Actions describe **what happens** when a rule matches.

Common action types:
- `run_playbook`  
- `run_module`  
- `debug`  
- `set_fact`  
- `post_event`  
- `shutdown`  

Example:

```yaml
actions:
  - run_playbook:
      name: restart_service.yml
  - debug:
      msg: "CPU issue handled successfully."
```

---

# 6. Rulebook Execution Flow

```
[ Event Source ] 
        ↓
[ Event Stream ]
        ↓
[ Rulebook Executor ]
        ↓
Evaluate rules
        ↓
If condition true → run actions
```

---

# 7. Multiple Rules in a Rulebook

Rules may be evaluated in sequence or independently.

```yaml
rules:
  - name: critical_alert
    condition: event.severity == "CRITICAL"
    actions:
      - run_playbook:
          name: handle_critical.yml

  - name: moderate_alert
    condition: event.severity == "WARNING"
    actions:
      - run_playbook:
          name: handle_warning.yml
```

---

# 8. Rules Without Conditions

Rules can trigger on **every event**:

```yaml
rules:
  - name: always_log
    actions:
      - debug:
          var: event
```

---

# 9. Variables & Facts

### Global variables:

```yaml
vars:
  admin_email: "ops@example.com"
```

### Dynamic variables:

```yaml
actions:
  - set_fact:
      service_name: "{{ event.service }}"
```

---

# 10. Best Practices

✔ Keep rulebooks readable  
✔ Avoid complex conditions  
✔ Debug events to understand payloads  
✔ Trigger small modular playbooks  
✔ Use version control  
✔ Monitor performance  

---

# 11. Production Example

```yaml
- name: dynatrace_auto_remediation
  hosts: localhost

  sources:
    - dynatrace.event:
        api_url: "{{ api_url }}"
        api_token: "{{ api_token }}"

  rules:
    - name: high_cpu_remediation
      condition: event.title contains "CPU" and event.severity == "CRITICAL"
      actions:
        - run_playbook:
            name: remediation/restart_service.yml

    - name: auto_create_ticket
      condition: event.severity == "CRITICAL"
      actions:
        - run_playbook:
            name: servicenow/create_ticket.yml

    - name: log_all_events
      actions:
        - debug:
            var: event
```

---

# 12. Summary

Rulebooks define:
- What events to listen to  
- How to evaluate them  
- What automated actions to run  

They form the logic core of real-time, autonomous automation in Event-Driven Ansible.

---
