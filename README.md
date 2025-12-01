# 🎯 Event-Driven Ansible (EDA) — Starter Learning Kit
This repository provides a hands-on learning environment for **Event-Driven Ansible (EDA)**, including rulebooks, Ansible Automation Platform workflows, event sources, and practical examples that connect theory to real automation scenarios.

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

---

## 📌 What You Will Learn
This repository guides you through:

- ✔ Fundamentals of Event-Driven Automation  
- ✔ Difference between EDA & traditional Ansible automation  
- ✔ How event sources, rules, and actions work  
- ✔ How to write and run Ansible **rulebooks**  
- ✔ How to trigger automation from:
  - file changes  
  - webhooks  
  - service alerts  
  - Git updates  

You will build real workflows that react to **live events**.

---

## 📂 Repository Structure

```
docs/      → Theory & explanations  
setup/     → Installation guides  
rulebooks/ → EDA rulebook examples  
playbooks/ → Classic Ansible playbooks called by rulebooks  
labs/      → Hands-on exercises  
utils/     → Helper scripts & event generators  
```

---

## 🚀 Quick Start: Run Your First Rulebook

### 1️⃣ Install Ansible (WSL2 recommended)
```
pip install ansible
```

### 2️⃣ Install the EDA rulebook engine
```
pip install ansible-rulebook
```

### 3️⃣ Install the EDA collection
```
ansible-galaxy collection install ansible.eda
```

### 4️⃣ Run a rulebook
```
ansible-rulebook -r rulebooks/filewatch.yml
```

---

## 📘 Example Rulebook
`rulebooks/filewatch.yml`:

```yaml
sources:
  - ansible.eda.filewatch:
      path: /tmp/messages

rules:
  - name: Restart service when file changes
    condition: event.change == "modified"
    action:
      run_playbook:
        name: ../playbooks/restart_service.yml
```

---

## 🧪 Labs Included
| Lab | Topic |
|-----|-------|
| **Lab 1** | Run your first rulebook |
| **Lab 2** | Build your own rulebook |
| **Lab 3** | Integrate rulebooks with playbooks |
| **Lab 4** | File-based event monitoring |
| **Lab 5** | Webhook-triggered automation |

Each lab is 15–30 minutes.

---

## 🧠 Who Is This For?
- Beginners in Ansible  
- DevOps engineers exploring automation  
- SREs wanting event-triggered workflows  
- Students learning IaC / DevOps fundamentals  

This repo is suitable for **self-study**, **classroom**, or **workshop preparation**.

---

## ⭐ Future Expansion (Optional)
Potential modules to add later:

- Kafka event source examples  
- GitHub Actions → EDA integration  
- Monitor GPU jobs (NVIDIA context)  
- Slack / Teams notification pipelines  
- Kubernetes event triggers  

---

