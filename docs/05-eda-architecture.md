# 05 – Event-Driven Ansible (EDA) Architecture

Event-Driven Ansible (EDA) introduces a modern, scalable, real-time automation architecture capable of responding instantly to signals across hybrid IT environments.  
Its architecture is built on modular components that receive events, evaluate logic, and trigger automated actions through the Ansible ecosystem.

---

# 1. High-Level Architecture Overview

EDA’s core architecture includes:

1. **Event Sources** – Systems or plugins that emit structured events  
2. **Rulebook Engine** – Evaluates event logic and runs automation  
3. **Actions Layer** – Playbooks, modules, outbound integrations  
4. **Optional Enterprise Components** – Message brokers, Automation Controller, APIs

### Event Processing Flow

```
Event Source → Event Stream → Rulebook Engine → Evaluate Rules → Trigger Actions
```

---

# 2. Event Sources

Event sources act as producers, feeding real-time data into EDA.

## 2.1 Common Source Types
- **Webhook** (GitHub/GitLab, CI systems)  
- **Monitoring Tools** (Dynatrace, Prometheus, Splunk, Elastic)  
- **File Watchers**  
- **Kafka / RabbitMQ**  
- **Timers**  
- **Custom Python plugins**

## 2.2 Example Source

```yaml
sources:
  - ansible.eda.webhook:
      host: 0.0.0.0
      port: 5000
```

---

# 3. Rulebook Engine (Rulebook Executor)

The rulebook engine is the core logic processor of EDA.

### Responsibilities:
- Listens to event sources  
- Parses event payloads  
- Applies rulebook logic  
- Executes corresponding actions  

### Continuous Loop

```
listen → parse → evaluate → act → repeat
```

### Characteristics:
- Stateless by default  
- Executes multiple rulebooks  
- Designed for real-time processing  

---

# 4. Actions Layer

Actions define what EDA should perform when conditions match.

## 4.1 Action Types
- **run_playbook** – Execute Ansible playbooks  
- **run_module** – Execute individual modules  
- **set_fact** – Store dynamic variables  
- **debug** – Print messages/events  
- **post_event** – Emit new events  
- **shutdown** – Stop execution  

## 4.2 Example Action Flow

```
Rulebook → run_playbook → Automation Controller → Target Hosts
```

---

# 5. Optional Enterprise Components

EDA can run stand-alone or integrated with Red Hat Ansible Automation Platform (AAP).

## 5.1 Message Broker (Redis/Kafka/RabbitMQ)
Used for:
- Event buffering  
- Load balancing  
- High-volume ingestion  
- Ordering and reliability  

## 5.2 Automation Controller
Provides:
- RBAC  
- Credential management  
- Logging  
- Workflow automation  
- Scalable job execution  

## 5.3 EDA Controller (AAP Component)
Offers:
- Rulebook management  
- Multi-team support  
- Observability dashboards  
- Scalable architecture for rulebook workers  

---

# 6. Deployment Models

## 6.1 Local Developer Mode
Best for learning/testing.

```
rulebook → local plugins → local actions
```

## 6.2 Small-Scale Deployment
```
Event Sources → Rulebook Engine → Playbooks → Target Systems
```

## 6.3 Enterprise Deployment (AAP Integrated)

```
+---------------------------+
|   EDA Controller (API)    |
+-------------+-------------+
              |
              v
     +--------+--------+
     | Rulebook Workers |
     | Redis/Kafka MQ   |
     +--------+--------+
              |
              v
+-------------+--------------+
| Automation Controller (AAP) |
+-----------------------------+
              |
              v
      Playbooks & Actions
```

---

# 7. Event Flow Example (Dynatrace → EDA → Playbook → ServiceNow)

1. Dynatrace sends CPU alert webhook  
2. EDA receives event  
3. Rulebook checks severity:  
   ```
   condition: event.severity == "CRITICAL"
   ```
4. EDA triggers remediation playbook  
5. Playbook restarts service, collects diagnostics  
6. Playbook opens a ServiceNow ticket  
7. EDA logs and waits for the next event  

---

# 8. Best Practices

✔ Keep rulebooks stateless  
✔ Use message brokers for high-volume environments  
✔ Delegate orchestration to playbooks, not rulebooks  
✔ Enable logging and observability  
✔ Test event formats using `debug: var=event`  

---

# 9. Summary

| Component | Description |
|----------|-------------|
| **Event Sources** | Stream real-time events |
| **Rulebook Engine** | Applies conditional logic |
| **Actions Layer** | Runs automated responses |
| **Message Broker (Optional)** | Scales event throughput |
| **Automation Controller** | Provides enterprise workflow orchestration |

EDA enables an **event → evaluate → automate** lifecycle supporting real-time, autonomous IT operations.

---
