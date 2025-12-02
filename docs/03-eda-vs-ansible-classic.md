# 03 – Event-Driven Ansible (EDA) vs. Classic Ansible

Event-Driven Ansible (EDA) and Classic Ansible are **complementary automation paradigms** within the Ansible ecosystem. While Classic Ansible focuses on **configuration management, orchestration, and procedural automation**, EDA introduces **real-time, event‑reactive automation** that responds instantly to signals from IT systems, applications, and infrastructure.

---

# 1. What Is Classic Ansible?

Classic Ansible is the established automation framework built on:

- **Playbooks**
- **Modules**
- **Inventory**
- **Idempotent execution**

Best suited for:

- Configuration management  
- Provisioning  
- Application deployment  
- Patching  
- Multi‑host orchestration  

Classic Ansible runs **on demand**, **on schedule**, or **via CI/CD pipelines**.

---

# 2. What Is Event‑Driven Ansible (EDA)?

EDA enables **reactive automation**, where actions occur automatically based on **events** such as:

- Monitoring alerts  
- Webhooks  
- Log changes  
- Security events  
- Kafka/RabbitMQ messages  
- Custom event emitters  

EDA uses **rulebooks**, which define:

- Event sources  
- Conditions  
- Automated actions  

Ideal for:

- Real‑time remediation  
- Alert-driven workflows  
- Self‑healing infrastructure  
- ITSM workflow automation  
- Continuous operational response  

---

# 3. Core Conceptual Differences

| Feature | **Classic Ansible** | **Event‑Driven Ansible (EDA)** |
|--------|----------------------|-------------------------------|
| Trigger | Manual or scheduled | Automatically triggered by events |
| Logic | Playbooks | Rulebooks |
| Execution | Procedural | Reactive |
| Mode | Push-based | Event‑driven |
| Best Use | Provisioning, config mgmt | Incident response, monitoring integration |
| Speed | Minutes/hours | Milliseconds/seconds |

---

# 4. How They Work Together

EDA can trigger Classic Ansible playbooks to create **closed‑loop automation**.

Example workflow:

1. Monitoring system detects a CPU spike  
2. Sends alert → EDA receives it  
3. Rulebook checks if alert is critical  
4. If true → run Ansible playbook  
5. Playbook remediates issue and notifies team  

EDA handles **detection**, Classic Ansible handles **action**.

---

# 5. When to Use Classic Ansible

Use Classic Ansible for:

- **Configuration management**
- **Infrastructure provisioning**
- **Application deployments**
- **Scheduled maintenance**
- **Orchestration across multiple systems**

---

# 6. When to Use Event‑Driven Ansible (EDA)

Use EDA when automation must respond to **external events**:

- **Real-time incident response**
- **Alert-driven workflows**
- **Security automation**
- **Self-healing infrastructure**
- **Automatic ticket creation/closure**
- **Webhook-driven automation**

---

# 7. Architectural Comparison

### Classic Ansible
```
User / CI Pipeline → Playbook → Inventory → Hosts
```

### EDA
```
Event Source → Event Stream → Rulebook → Automated Action
```

EDA listens continuously, unlike Classic Ansible’s manual or scheduled runs.

---

# 8. Example Comparison

### Classic Ansible (manual run)
```bash
ansible-playbook restart_service.yml
```

### EDA (reactive automation)
```yaml
- name: restart_on_alert
  condition: event.severity == "CRITICAL"
  actions:
    - run_playbook:
        name: restart_service.yml
```

---

# 9. Strengths & Limitations

| Area | Classic Ansible | EDA |
|------|----------------|-----|
| Consistency | Strong via idempotence | Depends on event quality |
| Speed | Batch-based | Immediate |
| Event Awareness | Limited | Core capability |
| Complex workflows | Strong via roles & playbooks | Lightweight logic engine |

---

# 10. Summary

### **Classic Ansible → “Do this now / on schedule.”**  
### **EDA → “Do this when the event happens.”**

Together, they enable:

- Real-time remediation  
- Autonomous operations  
- Consistent configuration  
- Intelligent automation pipelines  

Using both gives teams a full-stack, event-aware automation platform.

---
