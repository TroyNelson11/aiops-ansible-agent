# 🤖 AIOps Ansible Agent: The Professor’s Concierge

This repository transforms **Ansible Automation Platform (AAP)** and **OpenShift AI** into an "Intelligence-First" infrastructure service. 

Instead of professors filling out complex Jira tickets or navigating OpenShift YAML, they simply chat with an AI agent. The agent handles the discovery, maps requirements to a technical catalog, and triggers the automation—keeping a human-in-the-loop for final approval.

---

## 🌟 The Demo Experience
Imagine a professor needing a lab for 20 students for a "Deep Learning with PyTorch" course.
1. **The Interview:** The agent (running in **Cursor**) asks about their needs and consults a predefined `catalog/` of valid hardware/software stacks.
2. **The Request:** The agent packages the professor's metadata (Student count, Git repo, GPU needs) and sends it to AAP via a **Model Context Protocol (MCP)** bridge.
3. **The Gatekeeper:** An IT Admin receives a notification in AAP to approve the resource request.
4. **The Result:** 20 identical, pre-configured Jupyter workbenches appear in **OpenShift AI**, each pre-cloned with the professor's course materials.

---

## 🛠️ Architecture at a Glance

* **The Brain:** Cursor AI using `.cursorrules` to maintain a strict "Academic Assistant" persona.
* **The Knowledge:** A `catalog/` of YAML mappings that define what "flavors" of labs are allowed.
* **The Bridge:** **Ansible MCP Server**—a lightweight gateway that lets AI talk directly to AAP.
* **The Engine:** **Ansible Automation Platform 2.6** executing playbooks.
* **The Target:** **Red Hat OpenShift AI (RHOAI)**.



---

## 📚 Documentation & Setup Guide

We have broken the setup into a series of bite-sized steps. Please follow them in order:

| Step | Topic | Description |
| :--- | :--- | :--- |
| **01** | [**AAP Deployment**](./docs/1-aap-setup.md) | Installing the Operator and creating the platform. |
| **02** | [**Persistent Credentials**](./docs/2-credentials.md) | Setting up ServiceAccounts and 1-year tokens. |
| **03** | [**MCP Bridge**](./docs/3-mcp-server.md) | Deploying the server that connects AI to Ansible. |
| **04** | [**Cursor Setup**](./docs/4-cursor-setup.md) | Configuring the AI frontend with your MCP URL. |
| **05** | [**Ansible Workflow**](./docs/5-ansible-workflow.md) | Building the Job Templates and Approval Gates. |
| **06** | [**Running the Demo**](./docs/6-demo-instructions.md) | Scripted walkthrough and environment reset. |

---

## 📁 Key Files
* **`.cursorrules`**: The "Instruction Manual" for the AI. It prevents the AI from hallucinating and ensures it asks for the right metadata.
* **`catalog/mapping.yml`**: The source of truth for workbench images and hardware profiles.
* **`playbooks/deploy_workbench.yml`**: The heavy-lifting Ansible logic that talks to the OpenShift API.

---
**Ready to start?** Head over to [**Step 1: AAP Deployment**](./docs/1-aap-setup.md).