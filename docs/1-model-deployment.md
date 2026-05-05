## 🛠️ Steps

- [ ] **🔐 Deploy the credential secrets needed by the database and model registry**

  ```bash
  oc apply -f configs/01/database-creds.yaml
  ```
  > **Expected output:**
  > ```text
  > secret/myregistry-registry created
  > secret/myregistry-mysql created
  > ```

---

- [ ] **🗄️ Deploy the MySQL database**
  
  *Create a MySQL deployment, service, and persistent storage for the data.*

  ```bash
  oc apply -f configs/01/mysql.yaml
  ```
  > **Expected output:**
  > ```text
  > persistentvolumeclaim/mysql created
  > service/mysql created
  > deployment.apps/mysql created
  > ```

---

- [ ] **👀 Verify the database is running and available**

  ```bash
  oc get pods -n rhoai-model-registries -w
  ```
  > **Expected output:**
  > ```text
  > NAME                     READY   STATUS    RESTARTS   AGE
  > ...
  > mysql-76cd95cc56-qsdfx   1/1     Running   0          7s
  > ```

---

- [ ] **📦 Deploy the model registry**

  ```bash
  oc apply -f configs/01/model-registry.yaml
  ```
  > **Expected output:**
  > ```text
  > secret/myregistry-registry created
  > ```

---

- [ ] **✅ Verify the model registry is running and available**

  ```bash
  oc get pods -n rhoai-model-registries -w
  ```
  > **Expected output:**
  > ```text
  > NAME                         READY   STATUS    RESTARTS   AGE
  > ...
  > myregistry-79699fbf6-rwlhx   2/2     Running   0          15s
  > ```

  💡 *You can further validate the registry is working by finding the registry page in OpenShift AI.*

  ![](/assets/empty-registry.png)

---

- [ ] **📥 Get the default model catalog file to use as a base for our changes**

  *Execute the following commands in a terminal:*

  ```bash
  POD_NAME=$(oc get pods -n rhoai-model-registries -l app.kubernetes.io/name=model-catalog -o name)
  oc exec -n rhoai-model-registries $POD_NAME -c catalog -- cat /shared-data/validated-models-catalog.yaml > scratch/validated-models-catalog.yaml
  ```
---

- [ ] **➕ Add the GPT-OSS-20b-essential variant model to the model catalog**

  *Run the following commands in a terminal:*

  ```bash
  yq '.models.[] | select(.name == "RedHatAI/gpt-oss-120b")' scratch/validated-models-catalog.yaml > scratch/new-model.yaml
  yq -i '.name = "gpt-oss-20b-essential"' scratch/new-model.yaml
  yq -i '.provider = "OpenAI by way of Red Hat"' scratch/new-model.yaml
  yq -i '.description = "This is a Red Hat packaged version of gpt-oss that streamlines the model architecture to just the essential files necessary to run the model in vLLM."' scratch/new-model.yaml
  yq -i '.artifacts[0].uri = "oci://registry.redhat.io/rhai/modelcar-gpt-oss-20b-essential:3.0"' scratch/new-model.yaml
  ```

---

- [ ] **📝 Prepare the model for insertion into the ConfigMap**

  *Run the following in a shell:*

  ```bash
  touch scratch/model-for-configmap.yaml
  yq -i '.source = "Organization AI"' scratch/model-for-configmap.yaml
  yq -i '.models = [load("scratch/new-model.yaml")]' scratch/model-for-configmap.yaml
  ```

---

- [ ] **🔄 Get the custom model catalog ConfigMap locally for updates**

  *Execute the following commands:*

  ```bash
  oc get configmap model-catalog-sources -n rhoai-model-registries -o yaml > scratch/model-catalog-sources.yaml
  cp scratch/model-catalog-sources.yaml scratch/new-model-catalog-sources.yaml
  ```

---

- [ ] **✏️ Modify the default model catalog for a new model**

  > ⚠️ **WARNING:**
  > The name of the key in the ConfigMap (e.g. `my-ai-catalog.yaml`) must match the `yamlCatalogPath` in the `sources.yaml` file, located at `.catalogs[].properties.yamlCatalogPath`. Furthermore, both of these attributes must be strings that contain the pertinent YAML.

  ```bash
  yq -i '.data."sources.yaml" = load_str("configs/02/sources.yaml")' scratch/new-model-catalog-sources.yaml
  yq -i '.data."my-ai-catalog.yaml" = load_str("scratch/model-for-configmap.yaml")' scratch/new-model-catalog-sources.yaml
  ```
  > 💡 *verify that `scratch/new-model-catalog-sources.yaml` has two keys in `.data`, `sources.yaml` and `my-ai-catalog`.*

---

- [ ] **🚀 Update the ConfigMap on the cluster with your new values**

  *Run the following to replace the existing ConfigMap with our new one:*

  ```bash
  oc replace configmap -n rhoai-model-registries -f scratch/new-model-catalog-sources.yaml
  ```
  > **Expected output:**
  > ```text
  > configmap/model-catalog-sources replaced
  > ```

---

- [ ] **♻️ Restart the model catalog deployment to get it to pick up the new catalog**

  *Run the following to restart the model catalog and watch it come back up:*

  ```bash
  oc rollout restart deployment/model-catalog -n rhoai-model-registries
  oc get pods -n rhoai-model-registries -l app.kubernetes.io/name=model-catalog -w
  ```
  > **Expected output:**
  > ```text
  > deployment.apps/model-catalog restarted
  > NAME                            READY   STATUS    RESTARTS   AGE
  > model-catalog-b59948797-s4fvj    2/2     Running   0          11s
  > ```

---

- [ ] **🔍 Navigate to the Model Catalog and validate that your model is present**

  *From the OpenShift AI web console, navigate to the "Catalog" section (`AI hub` > `Catalog`).*

  ![](/assets/console-catalog-registry.png)

  *When you click into the catalog, you should see that your new catalog `My models` has been added with the model we chose.*

  ![](/assets/my-model-catalog.png)

---

- [ ] **🚀 Register and Deploy your model (GPU required)**

  *From the OpenShift AI model catalog, click on your GPT model (`AI hub` > `Catalog` > `gpt-oss-20b-essential`).*

  First, click **"Register model"** and provide the model name, plus a description if needed. This will save the current item to the model registry, where versioning and multiple instances of the model can be tracked.

  Next, from the OpenShift AI model registry, click on your GPT model and select **"Version 1"** (`AI hub` > `Registry` > `gpt-oss-20b-essential` > `Version 1`).

  Click **"Deploy"** and configure the model using the following settings:

  | :--- | :--- |
  | **Name** | `gpt-oss-20b` *(can be changed)* |
  | **Hardware profile** | `NVIDIA GPU` |
  | **Serving Runtime** | `Auto-select` *(vLLM NVIDIA GPU ServingRuntime for KServe)* |
  | **External route** | ✅ Checked *(Make model Deployments available through an external route)* |
  | **Token Authentication** | ✅ Checked *(Require Token Authentication)* |
  | **Deployment strategy** | `Recreate` |

![](/assets/deployed-models.png)

<p align="center">
<a href="/docs/02-aap-setup.md">Next</a>
</p>