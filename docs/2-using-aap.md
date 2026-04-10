# Ansible Automation Platform (AAP) Configuration

Once your infrastructure is up, AAP needs to be configured to talk to OpenShift and accept variables from the AI.

### 1. OpenShift Credentials (CRITICAL)
AAP needs permission to build resources in OpenShift.
1. In the OpenShift terminal, run `oc whoami -t` to get your Bearer Token.
2. In AAP, navigate to **Credentials** -> **Add**.
3. Select **OpenShift or Kubernetes API Bearer Token** as the credential type.
4. Paste your cluster URL and the token.
   * ⚠️ **Demo Warning:** If you are using a personal user token (rather than a ServiceAccount), **it will expire daily**. You must update this credential with a fresh token before every demo!

### 2. Project & Job Template Configuration
The AI sends variables via the API, so AAP must be configured to accept them.
1. **Sync the Code:** Create a new **Project** in AAP pointing to this Git repository.
2. **Create the Job Template (Deploy_Workbench):**
   * Point it to `playbooks/deploy_workbench.yml`.
   * Attach the OpenShift Credential you created in Step 1.
   * 🛑 **MANDATORY:** You MUST check the **"Prompt on Launch"** box for **Variables**. If this is unchecked, AAP will ignore the data sent by Cursor, and the deployment will fail.

### 3. The Approval Workflow (Human-in-the-Loop)
To prevent the AI from spinning up costly GPUs without oversight:
1. Create a new **Workflow Job Template**.
2. Add an **Approval Node** as the first step.
3. Link the "Success" path of the Approval Node to your `Deploy_Workbench` template.