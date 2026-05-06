
### 1. ⚙️ Create the Ansible MCP Server

  1. Navigate to **Operators** > **Installed Operators** and click on the **Ansible Automation Platform** operator.
  2. Go to **All instances** tab > **Create new** > Ansible MCP Server.
  3. Change the name to something other than example
  4. Provide your cluster route for the Ansible Platform you created,     
  *https://aap-instance-aap.apps.cluster-n4n8n.n4n8n.sandbox3774.opentlc.com/*
  5. Check **allow_write_operations**
  4. Click **Create**.

---

### 2. Generate an AAP API Token
1. Log into your **Ansible Automation Platform** web console.
2. In the sidebar, navigate to **Access Management** > **Users**.
3. Click on your **Username** (e.g., `admin`).
4. Select the **Tokens** tab and click **Create API Token**.
5. Ensure scope is set to **Write** and no Oauth application is selected.
6. Click **Create** and **Copy the Token immediately**. 
   > ⚠️ **Warning:** The token will only be displayed once. If you lose it, you must create a new one.

---

### 3. Store the Token in OpenShift
To keep the token secure and off your local hard drive, store it as a secret in your OpenShift cluster. Replace `<PASTE_TOKEN_HERE>` with the token you just copied.

```bash
oc create secret generic aap-token --from-literal=token="<PASTE_TOKEN_HERE>" -n aap
```

---


<p align="center">
<a href="../docs/2-aap-setup.md/">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="../docs/4-cursor-setup.md/">Next</a>
</p>
```
