# AIOps Ansible Agent: Professor's Workbench Provisioner

This repository implements an **Intelligence-First** methodology for managing university infrastructure. It uses a 20B parameter model via **Cursor MCP** to act as a conversational interface for professors to spin up student environments.

---

## 🏗 Architecture Overview
1. **The Brain:** Cursor AI / AnythingLLM (using `.cursorrules` and `catalog/`).
2. **The Bridge:** Ansible MCP Server (deployed via AAP Operator).
3. **The Engine:** Red Hat Ansible Automation Platform (AAP) 2.6.
4. **The Target:** OpenShift / Multi-cloud student workbenches.

---

## 🚀 Setup Instructions

### 1. Deploy the Ansible Operator
- Log into OpenShift.
- Navigate to **OperatorHub** and search for **Ansible Automation Platform**.
- Install the Operator into the `aap` namespace.

### 2. Configure the Platform (UI & Token)
- Create an **AnsiblePlatform** instance via the Operator.
- Access the Controller UI once the Route is live.
- Create an **Application** (Manual/Personal).
- Generate a **Personal Access Token (PAT)** for a user with 'Execute' permissions on your templates.
  - *Keep this token handy; it will be your `CONTROLLER_TOKEN`.*

### 3. Deploy the MCP Server (The "Bridge")
Deploy the MCP component using the AAP Operator (YAML View) add your own public base url:
```yaml
spec:
  allow_write_operations: true
  public_base_url: 'https://ansibleplatform-aap.apps.cluster-xxxxx.xxxxx.sandbox####.opentlc.com'
```

### 4. Connect Cursor
- Open Cursor Settings (top right) -> **Tools & MCP**.
- + **New MCP Server**:

```json
{
  "mcpServers": {
    "remote-job-mgmt": {
      "type": "http",
      "url": "https://<YOUR_MCP_SERVER_ROUTE>/job_management/mcp",
      "headers": {
        "Authorization": "Bearer ${env:MY_SERVICE_TOKEN}"
      }
    },
    "remote-inv-mgmt": {
      "type": "http",
      "url": "https://<YOUR_MCP_SERVER_ROUTE>/inventory_management/mcp",
      "headers": {
        "Authorization": "Bearer ${env:MY_SERVICE_TOKEN}"
      }
    }
  }
}
```
---

## 📁 Project Structure
- `.cursorrules`: **The Behavioral Layer.** Dictates the "Professor's Assistant" persona and safety guardrails.
- `catalog/`: **The Knowledge Layer.** A markdown-based menu of available workbench environments the AI is allowed to suggest.
- `playbooks/`: **The Execution Layer.** Ansible YAML files defining the actual infrastructure logic.
- `inventory/`: **The Target Layer.** Definitions of the student clusters and namespaces.

---

## 🎓 How to Use (The Professor's Workflow)
1. **Start a Chat:** Open Cursor and use the 20B model.
2. **The Ask:** "I need a lab for my 40 students."
3. **The Interview:** The agent will reference `catalog/workbenches.md`, ask which stack you need, and confirm your name.
4. **The Launch:** Once you say "Proceed," the agent calls the `launch_job_template` tool via the MCP bridge to trigger the Ansible

## 🤝 Human-in-the-Loop (HITL) Setup
To prevent unmanaged resource spikes, this agent triggers **Workflow Job Templates**.

### 1. Create the Workflow in AAP
- Create a new **Workflow Job Template**.
- Add an **Approval Node** as the first or second step.
- Link the "Success" path of the Approval Node to your actual Provisioning Playbook.

### 2. Configure Notifications
- In AAP, go to **Notifications**.
- Create a "Slack" or "Webhook" (ServiceNow) notification.
- Toggle the notification to trigger on **"Workflow Approval Required"**.
- Now, when the AI agent calls the API, the Admin gets a Slack message with a "Click here to approve" link.
