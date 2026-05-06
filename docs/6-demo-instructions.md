### 🎭 Step 6: Running the Demo

This guide walks you through the "showtime" phase. You will act as a Professor requesting infrastructure, observe the AI "thinking" via the rules we established, and finally approve the request as an IT Admin.

---

### ✅ Pre-Flight Confirmation
Before starting the narrative, confirm you have completed the following:
* [ ] **MCP Connection:** Cursor shows a green light for the `Ansible-MCP-Server`.
* [ ] **Namespace:** The `jupyter-labs` namespace exists (`oc new-project jupyter-labs`).
* [ ] **AAP Credential:** The 1-year ServiceAccount token is saved in AAP with **Verify SSL** unchecked.
* [ ] **AAP Template:** The "Deploy Workbench" template has **Prompt on Launch** checked for variables.

---

### 🧠 The "Brains": Understanding `.cursorrules`
The file `.cursorrules` is the logic engine of this demo. It forces the AI to follow a strict protocol so it doesn't just "guess" what you need.

**How it works during the demo:**
1.  **Zero Narration:** The AI won't chat your ear off. It gets straight to the point.
2.  **Discovery:** It looks at `catalog/mapping.yml` to see what "flavors" of labs are available.
3.  **Mapping:** If you say "I'm teaching a PyTorch class," it is programmed to find the `pytorch_gpu` ID and explain the packages included.
4.  **Guardrails:** It will **never** suggest a GPU unless you explicitly mention high-performance needs, preventing accidental cost spikes.

---

### 🚀 Execution Flow

#### **1. The Conversation (The Professor)**
Open a new Chat in Cursor (`Cmd + L` or `Ctrl + L`) and initiate the request. 

**Try this prompt:**
> *"I'm a Professor in the Biology department and I need a lab set up for my 5 students."*

* **The AI Response:** It should ask you for a **Workbench Name** and a **Justification** if you choose a GPU.
* **The Confirmation:** Once you provide the details, it will ask for a **Git Repository URL**.
* **The Submission:** After the Git question, it will present a summary. Click **"Run"** on the tool-call block to send the request to AAP.

#### **2. The Human-in-the-Loop (The IT Admin)**
The AI has submitted the request, but nothing has happened in OpenShift yet. It's waiting for *you*.

1.  Open your **Ansible Automation Platform** dashboard.
2.  Navigate to **Administration** > **Workflow Approvals**.
3.  Locate the pending request and click **Approve**.
    * *Note: This is the moment the automation actually begins the heavy lifting.*



#### **3. The Reveal (OpenShift AI)**
Now, watch the infrastructure materialize in real-time.

1.  Open the **Red Hat OpenShift AI (RHOAI)** dashboard.
2.  Navigate to **Data Science Projects** > **jupyter-labs**.
3.  Click the **Workbenches** tab.
4.  You will see 20 unique workbenches spinning up. Each one is pre-loaded with the GPU profile and your specified Git repository.

---

### 🧹 Resetting the Environment
To run the demo again (or if things get messy), use these commands to wipe the lab clean:

**To delete all workbenches and storage in the lab:**
```bash
oc delete notebooks,pvc,sa --all -n jupyter-labs
```

**To check the status of the cleanup:**
```bash
oc get notebooks -n jupyter-labs
```

---

### 🏆 Pro-Tips for a Great Demo
* **Highlight the "Slugification":** Point out how the AI took your human-friendly lab name (e.g., "Intro to Bio 101") and turned it into a valid Kubernetes string (e.g., `intro-to-bio-101`).
* **The GPU Logic:** Show the `catalog/mapping.yml` file to the audience to explain how the AI knew exactly which image and hardware profile to request.
* **Workflow History:** In AAP, show the **Job Output** to prove that the AI passed the exact variables you discussed in the chat directly into the playbook.

<p align="center">
<a href="../docs/5-ansible-workflow.md/">Prev</a>
&nbsp;&nbsp;&nbsp;
<span>🏁 End of Setup Guide</span>
</p>