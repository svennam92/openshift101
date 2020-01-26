# Exercise 3: Metrics and dashboards

In this exercise, we'll explore the third-party monitoring and metrics dashboards that are installed for free with OpenShift!

## Grafana

Red Hat OpenShift on IBM Cloud comes with [Grafana](https://grafana.com/) preinstalled.

1. Get started by switching to the **Administrator** perspective:

    ![Administrator Perspective](../assets/ocp43-adminview.png)

2. Navigate to **Monitoring > Dashboards** in the left-hand bar. You'll be asked to login with OpenShift and then click through some permissions.

    ![Monitoring Dashboards](../assets/ocp43-monitoring-dashboard.png)

3. You should then see your Grafana dashboard. Hit **Home** on the top left, and choose **Kubernetes / Compute Resources / Namespace (Pods)**.

    ![Grafana](../assets/ocp43-grafana.png)

4. Choose the name of the project you created in Step 1 - the same one that your application is running inside.

5. You should be able to see the CPU and Memory usage for your application. In production environments, this is helpful for identifying the average amount of CPU or Memory your application uses, especially as it can fluctuate through the day. We'll use this information in the next exercise to set up auto-scaling for our pods.

    ![Grafana also project](../assets/ocp43-grafana-cpu.png)

## Prometheus and Alert Manager

Navigating back to the cluster console, you can also launch:

* [**Prometheus**](https://prometheus.io/) - a monitoring system with an efficient time series database
* [**Alertmanager**](https://prometheus.io/docs/alerting/alertmanager/) - an extension of Prometheus focused on managing alerts

## Prometheus

OpenShift provides a web interface to Prometheus, which enables you to run Prometheus Query Language \(PromQL\) queries and examine the metrics visualized on a plot. This functionality provides an extensive overview of the cluster state and enables you to troubleshoot problems.

1. The Metrics page is accessible by clicking **Monitoring → Metrics**.

    ![Metrics, Alerts and Dashboards](../assets/ocp43-monitoring-prometheus.png)

    ![Prometheus](../assets/prometheus-time-series.png)

## Alertmanager

![Alert Manager](../assets/alert-manager.png)

