# Exercise 7: Configure the Sysdig Agent

To integrate your monitoring instance with your OpenShift cluster, you must run a script that creates a project and privileged service account for the Sysdig agent.

## Prereq

To deploy the Sysdig agent in a cluster, you must have a user ID that has the following Sysdig IAM roles:

* **Administrator** Platform role, and **Manager** Service role to work with the Sysdig instance.


## Step 2. Get your Sysdig instance access key

The Sysdig access key is used to open a secure web socket to the Sysdig ingestion server and to authenticate the monitoring agent with the monitoring service.

1. Create a service key for your Sysdig instance.

   Get the name of your Sysdig instance. Run the following command:

   ```text
   ibmcloud resource service-instances
   ```

   Then, run the following command:

   ```text
   ibmcloud resource service-key-create <key_name> Administrator --instance-name <sysdig_instance_name>
   ```

2. Note the **Sysdig Access Key** and **Sysdig Collector Endpoint** of your service key.

   ```text
   ibmcloud resource service-key <key_name>
   ```

   Example output:

   ```text
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

In the following command, replace `<sysdig_access_key>` and `<sysdig_collector_endpoint>` with the values from the service key that you created earlier. For `<tag>`, you can associate tags with your Sysdig agent, such as `role:service,location:us-south` to help you identify the environment that the metrics come from.

```text
curl -sL https://ibm.biz/install-sysdig-k8s-agent | bash -s -- -a <sysdig_access_key> -c <sysdig_collector_endpoint> -t faststart,<Enter your name> -ac 'sysdig_capture_enabled: false' --openshift
```

For example:

```text
curl -sL https://ibm.biz/install-sysdig-k8s-agent | bash -s -- -a <sysdig_access_key> -c <sysdig_collector_endpoint> -t faststart,marisa -ac 'sysdig_capture_enabled: false' --openshift
```

Example output:

```text
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

```text
oc get pods -n ibm-observe
```

Example output:

```text
    NAME                 READY     STATUS    RESTARTS   AGE
    sysdig-agent-qrbcq   1/1       Running   0          1m
    sysdig-agent-rhrgz   1/1       Running   0          1m
```

1. From the IBM Cloud [**Observability &gt; Monitoring** console](https://cloud.ibm.com/observe/logging), in the row for your monitoring instance, click **View Sysdig**. The Sysdig dashboard opens, and you can analyze your cluster metrics.

For more information about how to use monitoring, see the [Next steps docs](../.gitbook/assets/FIXME.PNG).

