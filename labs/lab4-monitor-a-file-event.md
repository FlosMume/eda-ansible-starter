# Lab 4 — Monitor a File Event Using Event-Driven Ansible

## Overview

Event-Driven Ansible (EDA) can respond to changes occurring on the local filesystem.  
This lab teaches you how to detect file events—such as creation, modification, deletion, or movement—and trigger automated actions in real time.

## Learning Objectives

You will learn to:

- Use the `ansible.eda.file` event source plugin  
- Detect file system changes  
- Extract event metadata  
- Trigger actions or playbooks in response to file events  
- Build maintainable monitoring workflows  

## 1. Real-World Use Cases

Examples include:

- Monitoring application logs  
- Triggering processing when new data files arrive  
- Detecting unauthorized configuration changes  
- Running ETL workflows when files appear  
- Archiving or cleaning up deleted files  

## 2. Create the Rulebook

Create:

```
file-monitor-rulebook.yml
```

Insert:

```yaml
---
- name: Monitor a directory for file events
  hosts: localhost

  sources:
    - ansible.eda.file:
        paths:
          - /tmp/eda-watch
        recursive: false
        events:
          - created
          - modified
          - deleted
          - moved

  rules:

    - name: Log file creations
      condition: event.type == "created"
      action:
        debug:
          msg: "📁 File created: {{ event.src_path }}"

    - name: Log file modifications
      condition: event.type == "modified"
      action:
        debug:
          msg: "✏️ File modified: {{ event.src_path }}"

    - name: Log file deletions
      condition: event.type == "deleted"
      action:
        debug:
          msg: "🗑️ File deleted: {{ event.src_path }}"

    - name: Log file moves
      condition: event.type == "moved"
      action:
        debug:
          msg: "📦 File moved from {{ event.src_path }} to {{ event.dest_path }}"
```

## 3. Prepare the Environment

Create a directory to watch:

```bash
mkdir -p /tmp/eda-watch
```

Ensure proper read permissions.

## 4. Start the Rulebook Engine

```bash
ansible-rulebook -r file-monitor-rulebook.yml -i localhost,
```

You should see logs indicating that the file watcher is active.

## 5. Generate Test Events

### Create a file

```bash
touch /tmp/eda-watch/testfile.txt
```

### Modify the file

```bash
echo "Hello EDA" >> /tmp/eda-watch/testfile.txt
```

### Move the file

```bash
mv /tmp/eda-watch/testfile.txt /tmp/eda-watch/testfile2.txt
```

### Delete the file

```bash
rm /tmp/eda-watch/testfile2.txt
```

Each operation will generate corresponding debug output.

## 6. Event Metadata

The `file` plugin produces fields such as:

| Field | Description |
|--------|-------------|
| `event.type` | created, modified, deleted, moved |
| `event.src_path` | Path of affected file |
| `event.dest_path` | New path (if moved) |
| `event.is_directory` | Indicates directory events |

## 7. Extending the Workflow

### Example: trigger a playbook when a new file appears

```yaml
- name: Process new files
  condition: event.type == "created"
  action:
    run_playbook:
      name: process-file.yml
      extra_vars:
        filepath: "{{ event.src_path }}"
```

### Filter for a specific file type

```yaml
condition: event.type == "created" and event.src_path.endswith(".log")
```

### Monitor recursively

```yaml
recursive: true
```

## 8. Best Practices

### Performance
- Monitor only required directories  
- Avoid recursion unless needed  

### Security
- Do not monitor sensitive paths without proper access controls  

### Reliability
- Ensure directories exist before running rulebooks  
- Add fallback logic for unknown event types  

### Scalability
- Combine file monitoring with queueing (Kafka, Redis Streams) for heavy workloads  

## 9. Summary

In this lab, you learned how to:

- Monitor filesystem changes with EDA  
- Detect file creation, modification, deletion, and movement  
- Trigger actions based on event metadata  
- Extend automation workflows with playbooks  

You now have the foundation to build reactive file-based automation workflows.

