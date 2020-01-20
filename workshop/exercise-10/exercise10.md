# Exercise 10: Monitor your cluster through the Sysdig web UI

The IBM Cloud™ Monitoring with Sysdig service is a fully managed enterprise-grade monitoring service. You get deep container visibility, service-oriented views and comprehensive metrics. You can use this to gain operational visibility for your applications, services, and platform. Sysdig offers administrators, DevOps teams and developers advanced features to monitor and troubleshoot, define alerts, and design custom views.

When you monitor an application, you should consider:
* Monitoring the health of the application

    The health signals coming from hardware and software components

    Utilization, Saturations and Errors (USE): resource usage and capacity limits, CPU, memory, disk I/O, network against host or container limits

* Monitoring the golden signals that focus on perceived service quality

    Latency of HTTP calls, both average and worst case ones

    Traffic, that is, rate of requests per second

    Errors and their frequency

In the next steps, you will learn how to use dashboards and metrics to monitor the health of your application.

## Launch the web UI

You launch the Web UI within the context of an Sysdig instance, from the IBM Cloud UI. 

Complete the following steps to launch the web UI:

1. Click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) &gt; **Observability**. 

2. Select **Monitoring**. 

    The list of instances that are available is displayed.

3. Select the instance that is allocated for your lab. Then, click **View Sysdig**.

The Web UI opens.



# View SysDig pre-defined views and dashboards

**Use views and dashboards to monitor your infrastructure, applications, and services. You can use pre-defined dashboards. You can also create custom dashboards through the Web UI or programmatically. You can backup and restore dashboards by using Python scripts.**

The following table lists the different types of pre-defined dashboards:

| Type | Description | More information | 
|------|-------------|------------------|
| Applications | Dashboards that you can use to monitor your applications and infrastructure components.  | [Application dashboards](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-default_dashboards#default_dashboards_applications) |
| Host and containers | Dashboards that you can use to monitor resource utilization and system activity on your hosts and in your containers. | [Host and container dashboards](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-default_dashboards#default_dashboards_host_container) |
| Network | Dashboards that you can use to monitor your network connections and activity. | [Network dashboards](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-default_dashboards#default_dashboards_network) |
| Service | Dashboards that you can use to monitor the performance of your services, even if those services are deployed in orchestrated containers. | [Service dashboards](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-default_dashboards#default_dashboards_service) |
| Topology | Dashboards that you can use to monitor the logical dependencies of your application tiers and overlay metrics. | [Topology dashboards](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-default_dashboards#default_dashboards_topology) |
{: caption="Table 1. List of pre-defined dashboards" caption-side="top"} 



Complete the following steps to view a Sysdig dashboard:

1. After you launch the Sysdig web UI, in the Sysdig Welcome wizard, select **Kubernetes** as the installation method.

    After 30 seconds or so, it should show one or more agents connected.

    Select **GO TO NEXT STEP**.

    Then select **LET'S GET STARTED**.

2. Navigate the Sysdig console to get metrics on your Kubernetes cluster, nodes, deployments, pods, containers. Explore the following views:

3. View raw metrics for all workloads running on the cluster.

    Under the  _Explore_ section,

    ![explore icon](images/explore.png)
    
    Select **Containerized Apps** to view raw metrics for all workloads running on the cluster.

4. Get a global view of the cluster HTTP load.

    Under Dashboard, 
    
    ![dashboard icon](images/dashboards.png)
    
    Select **My Dashboards**. Then select **HTTP Overview** to get a global view of the cluster HTTP load.

5. Check how nodes are currently performing.

    Under Dashboard, select **My Dashboards**. Then select **Overview by Host** to understand how nodes are currently performing.

6. Check information about the sample app *patientui*.

    Under  _Explore_, select **Cluster and Nodes**. Expand the section **Entire Infrastructure**. Look for the partientui pod entry.

    ![Explore image 1](images/explore-img-1.png)



## Explore the normal traffic flow of the application

You can use the **Connection Table** dashboard to monitor how data flows between your application components.

1. From the _Explore_ tab, select **Deployments and Pods.**

2. Select the namespace where you deployed your sample app.

    ![Explore image 2](images/explore-img-2.png)

3. Select the *patientui* pod entry.

    ![Explore image 3](images/explore-img-3.png)

4. Select **Default Dashboards**.

    ![Explore image 4](images/explore-img-4.png)

5. Select **Hosts & Containers**. Then, select **Overview by Host**.

    ![Explore image 7](images/explore-img-7.png)

6. Select **Hosts & Containers**. Then, select **Overview by Container**.

    ![Explore image 5](images/explore-img-5.png)

    The **Overview by Container** view opens. Look at the different columns and the data.

    ![Explore image 6](images/explore-img-6.png)


## Explore the cluster and the node capacity

1. From the _Explore_ tab, select **Deployments and Pods.**

2. Select the namespace where you deployed your sample app.

    ![Explore image 2](images/explore-img-2.png)

3. Select the *patientui* pod entry.

    ![Explore image 3](images/explore-img-3.png)

4. Select **Default Dashboards**.

    ![Explore image 4](images/explore-img-4.png)

5. Select **Kubernetes Cluster**. Then, select **Node capacity**.

    ![Explore image 8](images/explore-img-8.png)

    The view **Kubernetes Cluster and Node Capacity** opens.

    ![Explore image 9](images/explore-img-9.png)

    Check the **Total CPU Capacity**. This is the CPU capacity that has been reserved for the node including system daemons.

    Check the **Total Allocatable CPU**. This is the CPU which is available for pods excluding system daemons.

    Check the **Total Pod CPU limit**. It should be less than the allocatable CPU of the node or cluster.

    Check the **Total Pod CPU Requested**. It is the amount of CPU that will be guaranteed for pods on the node or cluster.

    Check the **Total Pod CPU Usage**. It is the total amount of CPU that is used by all Pods on the node or cluster.


## Explore the Network

1. From the _DASHBOARDS_ tab, select **My Dashboards**. Then, select **Network Overview**.

    ![Dashboard image 1](images/dashboard-img-1.png)

    The following dashboard is displayed. It shows information about all resources that are monitored thorugh the instance.

    ![Dashboard image 2](images/dashboard-img-2.png)

 2. Change the scope of the dashboard to display information about your openshift cluster. Select **Edit scope**

    ![Dashboard image 3](images/dashboard-img-3.png)

    Change the scope.

    ![Dashboard image 4](images/dashboard-img-4.png)

    The dashboard shows information about the ibm-observe namespace.

    ![Dashboard image 5](images/dashboard-img-5.png)

    
