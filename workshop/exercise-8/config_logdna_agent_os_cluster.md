# Exercise 7: Configuring a LogDNA agent for an OpenShift Kubernetes cluster

The LogDNA agent is responsible for collecting and forwarding logs to your IBM Log Analysis with LogDNA instance. After you provision an instance of IBM Log Analysis with LogDNA, you must configure a LogDNA agent for each log source that you want to monitor.

To configure your Kubernetes cluster to send logs to your IBM Log Analysis with LogDNA instance, you must install a *LogDNA-agent* pod on each node of your cluster. The LogDNA agent reads log files from the pod where it is installed, and forwards the log data to your LogDNA instance.

To forward logs to your LogDNA instance, complete the following steps from the command line:

## Prereq

To deploy the LogDNA agent in a cluster, you must have a user ID that has the following IAM roles:
* **Viewer** platform role, and **Manager** service role to work with that cluster instance.
* **Viewer** permissions on the resource group where the cluster is available.

To configure the LogDNA agent in the cluster, you need the following CLIs:
* The IBM Cloud CLI to log in to the IBM Cloud by using `ibmcloud` commands, and to manage the cluster by using `ibmcloud ks` commands. [Learn more](/docs/containers?topic=containers-cs_cli_install#cs_cli_install_steps).
* The Kubernetes CLI to manage the cluster by using `kubectl` commands. [Learn more](/docs/containers?topic=containers-cs_cli_install#kubectl).
* The Openshift CLI to login to the cluster from the command line and deploy the agent. [Learn more](/docs/openshift?topic=openshift-openshift-cli).



## Step 1. Set the cluster context and log in to the cluster

Complete the following steps:

1. Open a terminal to log in to IBM Cloud.

   ```
   ibmcloud login -a cloud.ibm.com
   ```

   Select the account where you provisioned the IBM Log Analysis with LogDNA instance.

2. List the clusters to find out in which region and resource group the cluster is available.

    ```
    ibmcloud oc clusters
    ```

3. Set the resource group and region.

    ```
    ibmcloud target -g RESOURCE_GROUP -r REGION
    ```

    Where 
    
    `RESOURCE_GROUP` is the name of the resource group where the cluster is available, for example, `default`.
    
    `REGION` is the region where the cluster is available, for example, `us-south`.

4. Set the cluster context in your session.

    Run the following command:

    ```
    export IKS_BETA_VERSION=1
    ```
    Then, run:

    ```
    ibmcloud oc cluster config --cluster <cluster_name_or_ID>
    ```

    The behavior of the command `ibmcloud oc cluster config` in your current CLI version is deprecated, and becomes unsupported when CLI version 1.0 is released in March 2020. To use the new behavior now, you have set the 'IKS_BETA_VERSION' environment variable by running `export IKS_BETA_VERSION=1`. Note: Changing the beta version can include other breaking changes. For more information, see [Boost Your Productivity with a New CLI ](http://ibm.biz/iks-cli-v1).

5. Log in to the cluster. Choose a method to login to an OpenShift cluster. [Learn more about the methods to login](/docs/openshift?topic=openshift-access_cluster#access_automation).

    For example, you can create an IBM Cloud IAM API key, and then use the API key to log in to an OpenShift cluster. 

    Create an IBM Cloud API key.<p class="important">Save your API key in a secure location. You cannot retrieve the API key again. If you want to export the output to a file on your local machine, include the `--file <path>/<file_name>` flag.</p>

    ```
    ibmcloud iam api-key-create os-lab-apikey
    ```

    Then, use the API key to login:

    ```
    oc login -u apikey -p <API_key>
    ```

## Step 2. Store your LogDNA ingestion key as a Kubernetes secret

You must create a Kubernetes secret to store your LogDNA ingestion key for your service instance. The LogDNA ingestion key is used to open a secure web socket to the LogDNA ingestion server and to authenticate the logging agent with the IBM Log Analysis with LogDNA service.

1. Create a project. A project is a namespace in a cluster.

    ```
    oc adm new-project --node-selector='' ibm-observe
    ```
    {: pre}

    Set `--node-selector=''` to disable the default project-wide node selector in your namespace and avoid pod recreates on the nodes that got unselected by the merged node selector.

2. Create the service account **logdna-agent** in the cluster namespace **ibm-observe**. A service account is in Openshift what a service ID is in IBM Cloud. Run the following command:

    ```
    oc create serviceaccount logdna-agent -n ibm-observe
    ```

4. Grant the serviceaccount access to the **Privileged SCC** so the service account has permissions to create priviledged LogDNA pods. Run the following command:

    ```
    oc adm policy add-scc-to-user privileged system:serviceaccount:ibm-observe:logdna-agent
    ```

5. Add a secret. The secret sets the ingestion key that the LogDNA agent uses to send logs.

    ```
    oc create secret generic logdna-agent-key --from-literal=logdna-agent-key=INGESTION_KEY -n ibm-observe 
    ```
    {: pre}

    Where `INGESTION_KEY` is the ingestion key for the LogDNA instance where you plan to forward and collect the cluster logs. To get the ingestion key, see [Get the ingestion key through the IBM Log Analysis with LogDNA web UI](/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-ingestion_key).


## Step 3. Deploy the LogDNA agent in the cluster

Create a Kubernetes daemon set to deploy the LogDNA agent on every worker node of your Kubernetes cluster. 

The LogDNA agent collects logs with the extension `*.log` and extensionsless files that are stored in the `/var/log` directory of your pod. By default, logs are collected from all namespaces, including `kube-system`, and automatically forwarded to the IBM Log Analysis with LogDNA service.

Run the following command:

```
oc create -f https://assets.us-south.logging.cloud.ibm.com/clients/logdna-agent-ds-os.yaml -n ibm-observe
```


## Step 4. Verify that the LogDNA agent is deployed successfully

To verify that the LogDNA agent is deployed successfully, run the following command:

1. Target the project where the LogDNA agent is deployed.

    ```
    oc project ibm-observe
    ```
    {: pre}

2. Verify that the `logdna-agent` pods on each node are in a **Running** status.

    ```
    oc get pods -n ibm-observe
    ```
    {: pre}


The deployment is successful when you see one or more LogDNA pods.
* **The number of LogDNA pods equals the number of worker nodes in your cluster.**
* All pods must be in a `Running` state.
* *Stdout* and *stderr* are automatically collected and forwarded from all containers. Log data includes application logs and worker logs.
* By default, the LogDNA agent pod that runs on a worker collects logs from all namespaces on that node.

After the agent is configured, you should start seeing logs from this cluster in the LogDNA web UI. If after a period of time you cannot see logs, check the agent logs.

To check the logs that are generated by a LogDNA agent, run the following command:

```
oc logs logdna-agent-<ID>
```
{: pre}

Where *ID* is the ID for a LogDNA agent pod. 

For example, 

```
oc logs logdna-agent-xxxkz
```
{: pre}


## Step 5. Launch the LogDNA webUI to verify that logs are being forwarded from the LogDNA agent

Next, launch the LogDNA web UI to verify that logs from the cluster are available through the UI. 

The following table lists the minimum policies that your IBMid must have to be able to launch the web UI from the IBM Cloud Observability page, and view data in the LogDNA web UI:

| Service                              | Role                      | Permission granted       |
|--------------------------------------|---------------------------|---------------------|
| `IBM Log Analysis with LogDNA` | Platform role: Viewer     | Allows the user to view the list of service instances in the Observability Logging dashboard. |
| `IBM Log Analysis with LogDNA` | Service role: Reader      | Allows the user to launch the Web UI and view logs in the Web UI.    |
{: caption="Table 1. IAM policies" caption-side="top"} 


You launch the web UI within the context of an IBM Log Analysis with LogDNA instance, from the IBM Cloud UI. 

Complete the following steps to launch the web UI:

1. Click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) &gt; **Observability**. 

2. Select **Logging**. 

    The list of instances that are available on IBM Cloud is displayed.

3. Select your instance. Then, click **View LogDNA**.

The Web UI opens.






