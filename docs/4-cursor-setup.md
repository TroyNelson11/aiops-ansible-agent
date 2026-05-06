### 1. Launch Cursor
Instead of saving the token in a configuration file, use this command in a local terminal or a terminal in cursor to fetch the secret from OpenShift and inject it directly into Cursor's memory for the current session.

```bash
MY_SERVICE_TOKEN=$(oc get secret aap-token -n aap -o jsonpath="{.data.token}" | base64 -d) cursor .
```
- Run `echo $MY_SERVICE_TOKEN` to ensure the variable is present.

- Make sure to open this git repository folder inside of Cursor (`File` > `Open Folder` > `aiops-ansible-agent`).
---

### 2. Configure the MCP Server in Cursor
1. Open **Cursor Settings** (gear icon in the top right or `Cmd + Shift + J` / `Ctrl + Shift + J`).
2. Navigate to **Tools & MCPs**.
3. Click the **+ New MCP Server** button.
4. Fill in the following details:

| Field | Value |
| :--- | :--- |
| **Name** | `Ansible-MCP-Server` |
| **Type** | `command` (or `http` if prompted) |

5. **Configuration (JSON):**
   *Paste the following block. Replace the URL with your specific **AAP MCP Server** route found in OpenShift.* **Don't forget the job_management/mcp endpoint!**

```json
{
  "mcpServers": {
    "Ansible-MCP-Server": {
      "type": "http",
      "url": "https://aap-mcp-server-aap.apps.cluster-n4n8n.n4n8n.sandbox3774.opentlc.com/job_management/mcp",
      "headers": {
        "Authorization": "Bearer ${env:MY_SERVICE_TOKEN}"
      }
    }
  }
}
```

---

### 3. Save and Refresh
1. Click **Save**.
2. Cursor will attempt to "ping" the server. If successful, you will see a green light or a list of available tools (like `launch_job_template`).

---

### 4. Configure your gpt model on Cursor
1. Open **Cursor Settings** (gear icon in the top right or `Cmd + Shift + J` / `Ctrl + Shift + J`).
2. Navigate to **Models**.
3. Fill in the OpenAI API Key and Base URL with the model's default-token secret and external gpt model endpoint.
4. The **External URL** is found under **Openshift AI** Model Deployments (`Openshift AI` > `AI hub` > `Deployments` > `Internal and external endpoint`)
5. The **default-token** for the model is in the Project's **Openshift** Secrets (`Openshift` > `Workloads` > `Secrets` > `default-token-gpt-oss-20b-sa`)
   > **Scroll down and copy the token field**
6. Click **View All Models** and scroll all the way down to your custom model and toggle that on. *Make sure to turn off the other models as well.*
---


<p align="center">
<a href="../docs/3-ansible-mcp-server.md/">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="../docs/5-ansible-workflow.md/">Next</a>
</p>