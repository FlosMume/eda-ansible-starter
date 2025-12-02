# Lab 1 — Run Your First Event-Driven Ansible Rulebook

## Overview

In this first hands-on lab, you will run your initial **Event-Driven Ansible (EDA)** rulebook and observe how Ansible Automation Platform reacts to incoming events in real time. By the end of this exercise, you will understand the basic components of a rulebook, how event sources work, and how actions are triggered.

This lab assumes no prior EDA experience — only that you have a working environment prepared for Ansible Automation Platform and EDA execution.

## Learning Objectives

By completing this lab, you will be able to:

- Understand the structure and purpose of an **EDA rulebook**
- Run a rulebook locally using the **`ansible-rulebook`** command
- Use a simple **event source plugin** to generate events
- Observe **rule matching**, **conditions**, and **actions** in execution
- Validate that your environment is correctly set up for later labs

## 1. What Is a Rulebook?

A **rulebook** is the core logic file for Event-Driven Ansible. It describes:

1. **Event Sources** → where events come from  
2. **Rules** → how to evaluate events  
3. **Actions** → what to do when rules match  

Rulebooks are written in YAML and acted upon by the **EDA Rulebook Engine**.

## 2. Lab Prerequisites

Before you begin, ensure that the following are installed and working:

- Ansible Automation Platform or local Ansible EDA runtime  
- `ansible-rulebook` CLI  
- Python 3.9+ environment  
- Internet access (if using external event sources)

To verify the rulebook engine is installed:

```bash
ansible-rulebook --version
```

You should see a version string confirming the engine is ready.

## 3. Create Your First Rulebook

Create a new file named:

```
first-rulebook.yml
```

Populate it with the following example:

```yaml
---
- name: My First EDA Rulebook
  hosts: localhost

  sources:
    - ansible.eda.generic:
        payload:
          - message: "hello world"
          - message: "trigger action"

  rules:
    - name: Print incoming messages
      condition: event.message is defined
      action:
        debug:
          msg: "Received event: {{ event.message }}"
```

### What This Rulebook Does

- The `generic` source emits a list of predefined events.  
- For each event, the rule checks whether `event.message` exists.  
- If true, the rulebook prints the message using a debug action.

## 4. Run the Rulebook

Execute:

```bash
ansible-rulebook -r first-rulebook.yml -i localhost,
```

Expected output:

```
Starting sources...
Processing event: { "message": "hello world" }
Received event: hello world

Processing event: { "message": "trigger action" }
Received event: trigger action
```

## 5. Understand the Execution Flow

1. **Event Source Plugin Loads** — emits events.  
2. **Events Enter the Rule Engine** — each event is evaluated.  
3. **Condition Evaluation** — rule matches if `event.message` exists.  
4. **Action Execution** — displays the message.

## 6. Troubleshooting Tips

### Missing or outdated packages
```bash
pip install ansible-rulebook ansible-eda
```

### YAML formatting issues
Ensure proper indentation and that the file starts with `---`.

### Missing inventory
Be sure your inventory syntax uses `localhost,`.

## 7. Next Steps

With your environment now validated, proceed to:

- Lab 2 — Understanding Event Sources  
- Lab 3 — EDA vs Ansible Playbooks  
- Lab 4 — Sources → Rules → Actions  
- Lab 5 — EDA Architecture Deep Dive
