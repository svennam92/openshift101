
# Configure the Sysdig agent

To integrate your monitoring instance with your OpenShift cluster, you must run a script that creates a project and privileged service account for the Sysdig agent.

## Prereq


To deploy the Sysdig agent in a cluster, you must have a user ID that has the following IAM roles:
* **Viewer** platform role, and **Manager** service role to work with that cluster instance.
* **Viewer** permissions on the resource group where the cluster is available.

To configure the Sysdig agent in the cluster, you need the following CLIs:
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

## Step 2. Get your Sysdig instance access key

The Sysdig access key is used to open a secure web socket to the Sysdig ingestion server and to authenticate the monitoring agent with the monitoring service.

1.  Create a service key for your Sysdig instance.

    Get the name of your Sysdig instance. Run the following command:

    ```
    ibmcloud resource service-instances
    ```

    Then, run the following command:

    ```
    ibmcloud resource service-key-create <key_name> Administrator --instance-name <sysdig_instance_name>
    ```
        
2.  Note the **Sysdig Access Key** and **Sysdig Collector Endpoint** of your service key.

    ```
    ibmcloud resource service-key <key_name>
    ```
       
    Example output:
        
    ```
        Name:          <key_name>  
        ID:            crn:v1:bluemix:public:sysdig-monitor:<region>:<ID_string>::    
        Created At:    Thu Jun  6 21:31:25 UTC 2019   
        State:         active   
        Credentials:                                   
                       Sysdig Access Key:           11a1aa11-aa1a-1a1a-a111-11a11aa1aa11      
                       Sysdig Collector Endpoint:   ingest.<region>.monitoring.cloud.ibm.com      
                       Sysdig Customer Id:          11111      
                       Sysdig Endpoint:             https://<region>.monitoring.cloud.ibm.com  
                       apikey:                   <api_key_value>      
                       iam_apikey_description:   Auto-generated for key <ID>     
                       iam_apikey_name:          <key_name>       
                       iam_role_crn:             crn:v1:bluemix:public:iam::::role:Administrator      
                       iam_serviceid_crn:        crn:v1:bluemix:public:iam-identity::<ID_string>       
    ```


## Step 3. Deploy the Sysdig agent in the cluster

Run the script to set up an `ibm-observe` project with a privileged service account and a Kubernetes daemon set to deploy the Sysdig agent on every worker node of your Kubernetes cluster. 

The Sysdig agent collects metrics such as the worker node CPU usage, worker node memory usage, HTTP traffic to and from your containers, and data about several infrastructure components.

In the following command, replace <code>&lt;sysdig_access_key&gt;</code> and <code>&lt;sysdig_collector_endpoint&gt;</code> with the values from the service key that you created earlier. For <code>&lt;tag&gt;</code>, you can associate tags with your Sysdig agent, such as `role:service,location:us-south` to help you identify the environment that the metrics come from.

```
curl -sL https://ibm.biz/install-sysdig-k8s-agent | bash -s -- -a <sysdig_access_key> -c <sysdig_collector_endpoint> -t faststart,<Enter your name> -ac 'sysdig_capture_enabled: false' --openshift
```

For example:

```
curl -sL https://ibm.biz/install-sysdig-k8s-agent | bash -s -- -a <sysdig_access_key> -c <sysdig_collector_endpoint> -t faststart,marisa -ac 'sysdig_capture_enabled: false' --openshift
```

Example output:
    
```
    * Detecting operating system
    * Downloading Sysdig cluster role yaml
    * Downloading Sysdig config map yaml
    * Downloading Sysdig daemonset v2 yaml
    * Creating project: ibm-observe
    * Creating sysdig-agent serviceaccount in project: ibm-observe
    * Creating sysdig-agent access policies
    * Creating sysdig-agent secret using the ACCESS_KEY provided
    * Retreiving the IKS Cluster ID and Cluster Name
    * Setting cluster name as <cluster_name>
    * Setting ibm.containers-kubernetes.cluster.id 1fbd0c2ab7dd4c9bb1f2c2f7b36f5c47
    * Updating agent configmap and applying to cluster
    * Setting tags
    * Setting collector endpoint
    * Adding additional configuration to dragent.yaml
    * Enabling Prometheus
    configmap/sysdig-agent created
    * Deploying the sysdig agent
    daemonset.extensions/sysdig-agent created
```

## Step 4. Verify that the Sysdig agent is deployed successfully

Verify that the `sydig-agent` pods on each node show that **1/1** pods are ready and that each pod has a **Running** status.

Run the following command:

```
oc get pods -n ibm-observe
```


Example output:
    
```
    NAME                 READY     STATUS    RESTARTS   AGE
    sysdig-agent-qrbcq   1/1       Running   0          1m
    sysdig-agent-rhrgz   1/1       Running   0          1m
```

5.  From the [IBM Cloud **Observability > Monitoring** console](https://cloud.ibm.com/observe/logging), in the row for your monitoring instance, click **View Sysdig**. The Sysdig dashboard opens, and you can analyze your cluster metrics.

For more information about how to use monitoring, see the [Next steps docs](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-kubernetes_cluster#kubernetes_cluster_next_steps).

<br />
