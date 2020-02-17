# Monitor your Cluster with SysDig

IBM Cloud Monitoring with Sysdig is a co-branded cloud-native, and container- intelligence management system that you can include as part of your IBM Cloud architecture. Use it to gain operational visibility into the performance and health of your applications, services, and platforms. It offers administrators, DevOps teams, and developers full stack telemetry with advanced features to monitor and troubleshoot performance issues, define alerts, and design custom dashboards. IBM Cloud Monitoring with Sysdig is operated by Sysdig in partnership with IBM. [Learn more](https://cloud.ibm.com/docs/Monitoring-with-Sysdig?topic=Sysdig-getting-started).



In the next steps, you will learn how to use dashboards and metrics to monitor the health of your application.


## View SysDig pre-defined views and dashboards

Use views and dashboards to monitor your infrastructure, applications, and services. You can use pre-defined dashboards. You can also create custom dashboards through the Web UI or programmatically. You can backup and restore dashboards by using Python scripts.

The following table lists the different types of pre-defined dashboards:

| Type | Description | 
| :--- | :--- |
| Applications | Dashboards that you can use to monitor your applications and infrastructure components. |
| Host and containers | Dashboards that you can use to monitor resource utilization and system activity on your hosts and in your containers. |
| Network | Dashboards that you can use to monitor your network connections and activity. | 
| Service | Dashboards that you can use to monitor the performance of your services, even if those services are deployed in orchestrated containers. | 
| Topology | Dashboards that you can use to monitor the logical dependencies of your application tiers and overlay metrics. | 

Complete the following steps to view a Sysdig dashboard:

1. Launch the Sysdig web UI.

    ![](../assets/icp-monitoring-launch.png).

1. In the Sysdig Welcome wizard, select **Kubernetes** as the installation method.

   After 30 seconds or so, it should show one or more agents connected.

   Select **GO TO NEXT STEP**.

   Then select **LET'S GET STARTED**.

2. Navigate the Sysdig console to get metrics on your Kubernetes cluster, nodes, deployments, pods, containers. Explore the following views:
3. View raw metrics for all workloads running on the cluster.

   Under the _Explore_ section,

   ![](../assets/explore.png)

   Select **Containerized Apps** to view raw metrics for all workloads running on the cluster.

4. Get a global view of the cluster HTTP load.

   Under Dashboard,

   ![](../assets/dashboards.png)

   Select **My Dashboards**. Then select **HTTP Overview** to get a global view of the cluster HTTP load.

5. Check how nodes are currently performing.

   Under Dashboard, select **My Dashboards**. Then select **Overview by Host** to understand how nodes are currently performing.

6. Check information about the sample app _patientui_.

   Under _Explore_, select **Cluster and Nodes**. Expand the section **Entire Infrastructure**. Look for the partientui pod entry.

   ![](../assets/explore-img-1.png)

### Explore the normal traffic flow of the application

You can use the **Connection Table** dashboard to monitor how data flows between your application components.

1. From the _Explore_ tab, select **Deployments and Pods.**
2. Select the namespace where you deployed your sample app.
3. Select the _patientui_ pod entry.
4. Select **Default Dashboards**.

   ![](../assets/explore-img-4.png)

5. Check out the two dashboards under **Hosts & Containers**:
   * **Overview by Host**
   * **Overview by Container**.

### Explore the cluster and the node capacity

1. From the _Explore_ tab, select **Deployments and Pods.**
2. Select the namespace where you deployed your sample app.
3. Select the _patientui_ pod entry.
4. Select **Default Dashboards**.
5. Select **Kubernetes > Resource Usage > Kuberentes Cluster and Node Capacity**. 

   ![](../assets/explore-img-9.png)

   Check the **Total CPU Capacity**. This is the CPU capacity that has been reserved for the node including system daemons.

   Check the **Total Allocatable CPU**. This is the CPU which is available for pods excluding system daemons.

   Check the **Total Pod CPU limit**. It should be less than the allocatable CPU of the node or cluster.

   Check the **Total Pod CPU Requested**. It is the amount of CPU that will be guaranteed for pods on the node or cluster.

   Check the **Total Pod CPU Usage**. It is the total amount of CPU that is used by all Pods on the node or cluster.

### Explore the Network

1. From the _DASHBOARDS_ tab, select **My Dashboards**. Then, select **Network Overview**.

   The following dashboard is displayed. It shows information about all resources that are monitored thorugh the instance.

   ![](../assets/dashboard-img-2.png)

2. Change the scope of the dashboard to display information about your openshift cluster. Select **Edit scope** on the right side and change it:

    ![](../assets/dashboard-img-4.png)

    The dashboard now shows information about the ibm-observe namespace.

    ![](../assets/dashboard-img-5.png)

## Congratulations!

That's it, you're done with the Red Hat OpenShift 4.3 on IBM Cloud Kubernetes Service workshop. Thanks for joining us!