# Exercise 1: Deploying an application

In this exercise, you'll deploy a simple Node.js Express application - "Example Health". Example Health features a modern UI for a patient health records system. We'll use this example to demonstrate key OpenShift features throughout this workshop. You can find the sample application GitHub repository here: [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)


1. Test the `Example Health` app locally

    * Clone the Example Health repo and `cd` to it:
    
        ```console
        git clone https://github.com/IBM/node-s2i-openshift
        cd node-s2i-openshift
        ```

    * The Node.js application is in the "site" directory. `cd` to it, install the `npm` dependencies, and run the `Health Example` app on the localhost:

        ```console
        cd site
        npm install
        npm start
        ```
    * To access the application, use the "Port" previewer and select `8080`:
    
        ![portpreview](../.gitbook/assets/port-preview.png)

    * You should see the Example Health application. Login with `admin:admin` and click around!

        ![examplehealthlocalhost](../.gitbook/assets/examplehealth-localhost.png)

    * Ctrl+C in the terminal to stop the application.

1. Before we push this application into OpenShift, you'll need to configure your `oc` CLI to connect to your cluster.

    * Run `ibmcloud ks clusters` to find the name of your cluster. Then, run the following command to get the URL to the dashboard:
    
        ```console
        ibmcloud ks cluster get <your_cluster_name> | grep "Public Service Endpoint URL"
        ```

        Output:
        ```console
        Public Service Endpoint URL:    https://your_cluster_dashboard_url:32545   
        ```

    * Navigate to that URL in the same browser that you used to log-in to IBM Cloud, and you should see your OpenShift dashboard! Take a second to look around, and then copy the login command for the CLI:

    ![copylogincommand](../.gitbook/assets/copylogincommand.png)

    * Your login command should look something like:

        ```console
        oc login --token=WmBWdgg1eObMBIYOlQAafeDKdUUUwNDaRCR1Rf7YkVc0 --server=https://c100-e.containers.cloud.ibm.com:32545
        ```
    
    * Navigate back to your terminal, and run that command.

        Output:
        ```console
        Logged into "https://c100-e.containers.cloud.ibm.com:32545" as "IAM#svennam@us.ibm.com" using the token provided.

        You have access to 55 projects, the list has been suppressed. You can list all projects with 'oc projects'

        Using project "default".
        ```

    * Your CLI is now connected to your Red Hat OpenShift cluster running in IBM Cloud.

## Deploy Example Health into OpenShift

There's many ways to create a new application in OpenShift. If you're already a Kubernetes expert, you can stick with what you know and use YAML deployment files. However, OpenShift has greatly simplified the process of deploying apps into a cluster. Today, we'll demonstrate the "s2i" or "source to image" builder. This builder allows you to go from source code on GitHub to a running deployment.

1. Create a new project `example-health`:

    ```console
    oc new-project example-health
    ```

    Output:
    ```console
    Now using project "example-health" on server "https://c100-e.containers.cloud.ibm.com:32545".

    You can add applications to this project with the 'new-app' command. For example, try:

        oc new-app django-psql-example

    to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

        kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
    ```

1. Next, deploy the application directly from GitHub into your cluster. This uses the OpenShift "s2i" or "source to image" strategy. Essentially, this build strategy combines a standard Node.js runtime (a base image) with your source code in GitHub. Our code needs a runtime to actually run -- the base image takes care of this. The base image exists on DockerHub, named `nodejs-10-centos7:latest`.

    * Run the following command to build the Docker image. Note the `~` separator between the base image and source repository. The `--context-dir` flag is used to define the folder where the app exists:
    
        ```console
        oc new-app centos/nodejs-10-centos7~https://github.com/IBM/node-s2i-openshift --context-dir='site'
        ```
    
        Output:
        ```console
        --> Found Docker image 4028fd4 (3 weeks old) from Docker Hub for "centos/nodejs-10-centos7"

            Node.js 10 
            ---------- 
            Node.js 10 available as container is a base platform for building and running various Node.js 10 applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

            Tags: builder, nodejs, nodejs10

            * An image stream tag will be created as "nodejs-10-centos7:latest" that will track the source image
            * A source build using source code from https://github.com/IBM/node-s2i-openshift will be created
            * The resulting image will be pushed to image stream tag "node-s2i-openshift:latest"
            * Every time "nodejs-10-centos7:latest" changes a new build will be triggered
            * This image will be deployed in deployment config "node-s2i-openshift"
            * Port 8080/tcp will be load balanced by service "node-s2i-openshift"
            * Other containers can access this service through the hostname "node-s2i-openshift"

        --> Creating resources ...
            imagestream.image.openshift.io "node-s2i-openshift" created
            buildconfig.build.openshift.io "node-s2i-openshift" created
            deploymentconfig.apps.openshift.io "node-s2i-openshift" created
            service "node-s2i-openshift" created
        --> Success
            Build scheduled, use 'oc logs -f bc/node-s2i-openshift' to track its progress.
            Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
            'oc expose svc/node-s2i-openshift' 
            Run 'oc status' to view your app.
        ```
    * This command created an ImageStream, Deployment, a Pod, and a Service resource for the `Example-Health` app,

1. Reading the output, you'll notice that although a deployment and service is created, your application is not yet "exposed" to the outside world. To do so, run the following command:

    ```console
    oc expose svc/node-s2i-openshift
    ```

    Output:
    ```console
    route.route.openshift.io/node-s2i-openshift exposed
    ```

1. Run the `oc status` command to make sure everything started up correctly. This might take a couple minutes -- look for the `deployed` status. You can also launch the dashboard and track it there.

    > To find the OpenShift dashboard URL again, run: `ic ks cluster get <cluster_name>`

    ```console
    oc status
    ```

    Output:
    ```console
    In project example-health on server https://c100-e.containers.cloud.ibm.com:32545

    http://node-s2i-openshift-example-health.<your_openshift_cluster>.us-south.stg.containers.appdomain.cloud to pod port 8080-tcp (svc/node-s2i-openshift)
    dc/node-s2i-openshift deploys istag/node-s2i-openshift:latest <-
        bc/node-s2i-openshift source builds https://github.com/ibm/node-s2i-openshift on istag/nodejs-10-centos7:latest 
        deployment #1 deployed 21 minutes ago - 1 pod

    ```

1. Finally, access your application with the URL from above -- it should look something like:

    `http://node-s2i-openshift-example-health.<your_openshift_cluster>.us-south.stg.containers.appdomain.cloud`

You've completed the first exercise! Let's recap -- in this exercise, you:
* Connected your local CLI to a running OpenShift cluster on IBM Cloud
* Deployed the "Example Health" Node.js application directly from GitHub into your cluster 
    * Used the "Source to Image" strategy provided by OpenShift
* Deployed an end-to-end development pipeline 
    * New commits that happen in GitHub can be pushed to your cluster with a simple (re)build

## What's Next?

Let's dive into some Day 1 OpenShift Operations tasks, starting with Monitoring and Logging