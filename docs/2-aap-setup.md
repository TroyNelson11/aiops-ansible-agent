- [ ] **📦 Install the AAP Operator from OperatorHub**

  *We will use the OpenShift UI to install the Operator, as it automatically handles dependencies and catalog sources.*

  1. Log into your OpenShift web console with Cluster Administrator privileges.
  2. Navigate to **Ecosystem** > **Software Catalog**.
  3. Search for **Ansible Automation Platform**.
  4. Click **Install**, twice.
---

- [ ] **⚙️ Deploy the Ansible Platform Instance**

  *Once the Operator is installed and ready, we need to spin up the actual AAP Controller interface.*

  1. Navigate to **Operators** > **Installed Operators** and click on the **Ansible Automation Platform** operator.
  2. Find the **Ansible Automation Platform** tab or square, and select **Create instance**. You can also go to *All instances* tab > *Create new* > Ansible Automation Platform.
  3. Change the name to something other than example, switch to the **YAML View** to verify the configuration.
  4. Click **Create**.

---

- [ ] **🌐 Access the AAP Web Interface**

  *It will take a few minutes for the database and web interface pods to spin up.*

  1. Navigate to **Networking** > **Routes** in the `aap` namespace.
  2. Look for the route associated with your new AAP instance (it usually contains `aap` or `controller` in the name).
  3. Click the Location URL to open the AAP web interface.
> [!NOTE]
> Retrieve the `admin` user's password by navigating to **Workloads** > **Secrets**, finding the `aap-instance-admin-password` secret, and copying the value.*
  4. Once logged in, you will be requested to provide an aap subscription. Red Hat employees, select **Username and Password**. Provide your email and password and find the subscription: **Employee SKU** (List is alphabetical).
  5. Skip analytics page (Next) > **Agree** > **Finish**.

---


<p align="center">
<a href="../docs/1-model-deployment.md/">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="../docs/3-ansible-mcp-server.md/">Next</a>
</p>
```
