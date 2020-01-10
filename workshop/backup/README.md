# Exercise 1: Deploying an application

In this exercise, you'll deploy a simple Node.js Express application - "Example Health". Example Health is a simple UI for a patient health records system. We'll use this example to demonstrate key OpenShift features throughout this workshop. You can find the sample application GitHub repository here: [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)


1. Get the source code for the `Example Health` app

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

        ![portpreview](../.gitbook/assets/examplehealth-localhost.png)

1. Run the Example Health app with Docker,

    * In the directory `./site` create a new file `Dockerfile`,

        ```console
        touch Dockerfile
        ```

    * Edit the `Dockerfile` and add the following commands,

        ```console
        cat <<EOF >> Dockerfile
        FROM node:10-slim

        USER node

        RUN mkdir -p /home/node/app
        WORKDIR /home/node/app

        COPY --chown=node package*.json ./
        RUN npm install
        COPY --chown=node . .

        ENV HOST=0.0.0.0 PORT=3000

        EXPOSE 3000
        CMD [ "node", "app.js" ]
        EOF
        ```

    * Run the app with Docker,

        ```console
        docker stop example-health
        docker rm example-health
        docker build --no-cache -t example-health .
        docker run -d --restart always --name example-health -p 3000:3000 example-health

        da106f3b5a06a00ea8bf56c54e29f6e38405a77c6dec3e461e3062aa823d8a4f
        ```

1. Build and Push the Image to your public Docker Hub Registry.

    * Make sure to change the `<username>` by the username of your Docker Hub account,

    ```bash
    docker build --no-cache -t example-health .

    Sending build context to Docker daemon  8.229MB
    Step 1/10 : FROM node:10-slim
     ---> 8d33f30db9b5
    ... and more
    Successfully built aaf90ce81dd7
    Successfully tagged example-health:latest
    ```

    ```bash
    docker tag example-health:latest <username>/example-health:1.0.0
    ```

    ```bash
    docker login -u <username>

    Password:
    Login Succeeded
    ```

    ```bash
    docker push <username>/example-health:1.0.0

    The push refers to repository [docker.io/<username>/example-health]
    b33f2248b6f9: Pushed
    195f723f9ebb: Pushed
    0912774a40f4: Pushed
    3558c6f90d27: Pushed
    4d1d690b5181: Mounted from <username>/example-health
    bc272904b2c4: Mounted from <username>/example-health
    784c13bc7926: Mounted from <username>/example-health
    0e0d79e2c080: Mounted from <username>/example-health
    e9dc98463cd6: Mounted from <username>/example-health
    1.0.0: digest: sha256:a329778ce422e3d25ac9ff70b5131a9de26184a1e94b6d08844ea4f361519fd7 size: 2205
    ```

1. Login to the Remote OpenShift Cluster

    * Login to the OpenShift cluster web console,
    * From the logged in user drop down in the top right of the web console, select `Copy Login Command`,
    * The login command will be copied to the clipboard,
    * In your terminal, paste the login command, e.g.

        ```console
        oc login https://c100-e.us-south.containers.cloud.ibm.com:30403 --token=jWX7a04tRgpdhW_iofWuHqb_Ygp8fFsUkRjOK7_QyFQ
        ```

1. Create a new Project

    * Create a new project `example-health-ns`,

        ```console
        $ oc new-project example-health-ns
        Now using project "example-health-ns" on server "https://c100-e.us-south.containers.cloud.ibm.com:30403".

        You can add applications to this project with the 'new-app' command. For example, try:

            oc new-app centos/ruby-25-centos7~https://github.com/sclorg/ruby-ex.git

        to build a new example application in Ruby.
        ```

    * Use the `example-health-ns` project,

        ```console
        $ oc project example-health-ns
        Already on project "example-health-ns" on server "https://c100-e.us-south.containers.cloud.ibm.com:30403".
        $ oc project
        Using project "example-health-ns" on server "https://c100-e.us-south.containers.cloud.ibm.com:30403".
        ```

1. Deploy the `Example Health` app using the Docker image,

    * Create the application, and replace `<username>` by the username of your Docker Hub account,

        ```console
        $ oc new-app <username>/example-health:1.0.0
        --> Found Docker image aaf90ce (8 minutes old) from Docker Hub for "<username>/example-health:1.0.0"

            * An image stream tag will be created as "example-health:1.0.0" that will track this image
            * This image will be deployed in deployment config "example-health"
            * Port 3000/tcp will be load balanced by service "example-health"
            * Other containers can access this service through the hostname "example-health"

        --> Creating resources ...
            imagestream.image.openshift.io "example-health" created
            deploymentconfig.apps.openshift.io "example-health" created
            service "example-health" created
        --> Success
            Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
            'oc expose svc/example-health'
            Run 'oc status' to view your app.
        ```

    * This will create an ImageStream, Deployment, a Pod, and a Service resource for the `Example-Health` app,

1. Expose the `Example-Health` service,

    * The last thing to do is to create a route. By default, services on OpenShift are not publically available. A route will expose the service publically to external traffic.

        ```console
        $ oc expose svc/example-health
        route.route.openshift.io/example-health exposed
        ```

    * View the status,

        ```console
        $ oc status
        In project example-health-ns on server https://c100-e.us-south.containers.cloud.ibm.com:30403

        http://example-health-example-health-ns.cda-openshift-cluster-1c0e8bfb1c68214cf875a9ca7dd1e060-0001.us-south.containers.appdomain.cloud to pod port 3000-tcp (svc/example-health)
        dc/example-health deploys istag/example-health:1.0.0
            deployment #1 deployed 5 minutes ago - 1 pod

        2 infos identified, use 'oc status --suggest' to see details.
        ```

1. Review the `Example-Health` app in the web console,

    * Go to `My Projects` via URI `/console/projects`,

        ![My Projects](../.gitbook/assets/oc-my-projects.png)

    * Select the project `example-health-ns`, unfold the `DEPLOYMENT CONFIG` for `example-health` application details,

        ![Example Health details](../.gitbook/assets/oc-example-health-details.png)

    * In the `NETWORKING` section, click the `Routes - External Traffic` link, e.g. `http://example-health-example-health-ns.cda-openshift-cluster-1c0e8bfb1c68214cf875a9ca7dd1e060-0001.us-south.containers.appdomain.cloud`

    * This opens the Example Health app in a new tab of your browser,
    * Login with `admin:test`,

        ![Example Health details](../.gitbook/assets/example-health-app.png)

## You're done

Congratulations on completing the lab!






## Deploy Example Health

Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters). Click the `OpenShift web console` button on the top-right. (This is a pop-up so you'll need to white list this site.)

Once you're in the OpenShift dashboard, switch to the "Developer" view:

![Choose Developer](../.gitbook/assets/choose-developer.png)


oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/image-streams/image-streams-rhel7.json -n openshift






Create a project, you can title it whatever you like, we suggest "example-health."

![Create Project](../.gitbook/assets/createproject.png)

Click on your new project. You should see a view that looks like this:

![Project View](../.gitbook/assets/projectview.png)

Click on the browse catalog button and scroll down to the `Node.js` image. Click on that catalog button.

![Node](../.gitbook/assets/node.png)

Click through to the second step for configuration, and choose `advanced options`. \( a blue hyperlink on the bottom line \)

![Advanced](../.gitbook/assets/advanced.png)

You'll see an advanced form like this:

![Node Advanced Form](../.gitbook/assets/node-advanced-form.png)

Enter the repository: `https://github.com/IBM/node-s2i-openshift` and `/site` for the 'Context Dir'. Click 'Create' at the bottom of the window to build and deploy the application.

Scroll through to watch the build deploying:

![Build](../.gitbook/assets/build.png)

When the build has deployed, click the 'External Traffic Route', and you should see the login screen like the following:

![Login](../.gitbook/assets/login.png)

You can enter any strings for username and password, for instance `test:test` because the app is running in demo mode.

Congrats! You've deployed a `Node.js` app to Kubernetes using OpenShift Source-to-Image (S2I).

## Understanding What Happened

[S2I](https://docs.openshift.com/container-platform/3.6/architecture/core_concepts/builds_and_image_streams.html#source-build) is a framework that creates container images from source code, then runs the assembled images as containers. It allows developers to build reproducible images easily, letting them spend time on what matters most, developing their code!

## Git Webhooks

So far we have been doing alot of manual deployment. In cloud-native world we want to move away from manual work and move toward automation. Wouldn't it be nice if our application rebuilt on git push events? Git webhooks are the way its done and openshift comes bundled in with git webhooks. Let's set it up for our project.

To be able to setup git webhooks, we need to have elevated permission to the project. We don't own the repo we have been using so far. But since its opensource we can easily fork it and make it our own.

Fork the repo at [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)

![Fork](../.gitbook/assets/fork.png)

Now that I have forked the repo under my repo I have full admin priviledges. As you can see I now have a settings button that I can change the repo settings with.

![Forked Repo](../.gitbook/assets/forked-repo.png)

We will come back to this page in a moment. Lets change our git source to our repo.

From our openshift dashboard for our project. Select `Builds > Builds`

![Goto Build](../.gitbook/assets/goto-build.png)

Select the patientui build. As of now this should be the only build on screen.

![Select Build](../.gitbook/assets/select-build.png)

Click on `Action` on the right and then select `Edit`

![Edit Build](../.gitbook/assets/edit-build.png)

Change the `Git Repository URL` to our forked repository.

Click Save in the bottom.

![Update Build](../.gitbook/assets/update-build-src.png)

You will see this will not result in a new build. If you want to start a manual build you can do so by clicking `Start Build`. We will skip this for now and move on to the webhook part.

Click on `Configuration` tab.

Copy the GitHub Webook URL.

The webhook is in the structure

```text
https://c100-e.us-east.containers.cloud.ibm.com:31305/apis/build.openshift.io/v1/namespaces/example-health/buildconfigs/patientui/webhooks/<secret>/github
```

![Copy github webhook](../.gitbook/assets/copy-github-webhook.png)

> There is also the generic webhook url. This also works for github. But the github webhook captures some additional data from github and is more specific. But if we were using some other git repo like bitbucket or gitlab we would use the generic one.

In our github repo go to `Setting > Webhooks`. Then click `Add Webhook`

![webhook page](../.gitbook/assets/webhook-page.png)

In the Add Webhook page fill in the `Payload URL` with the url copied earlier from the build configuration. Change the `Content type` to `application/json`.

> **NOTE**: The *Secret* field can remain empty.

Right now just the push event is being sent which is fine for our use.

Click on `Add webhook`

![add webhook](../.gitbook/assets/add-webhook.png)

If the webhook is reachable by github you will see a green check mark.

Back in our openshift console we still would only see one build however. Because we added a webhook that sends us push events and we have no push event happening. Lets make one. The easiest way to do it is probably from the Github UI. Lets change some text in the login page.

Path to this file is `site/public/login.html` from the root of the directory. On Github you can edit any file by clicking the Pencil icon on the top right corner.

![edit page](../.gitbook/assets/edit-page.png)

Let's change the name our application to `Demo Health` (Line 21, Line 22). Feel free to make any other UI changes you feel like.

![changes](../.gitbook/assets/changes.png)

Once done go to the bottom and click `commit changes`.

Go to the openshift build page again. This happens quite fast so you might not see the running state. But the moment we made that commit a build was kicked off.

![running build](../.gitbook/assets/running-build.png)

In a moment it will show completed. Navigate to the overview page to find the route.

![route](../.gitbook/assets/route.png)

> You could also go to `Applications > Routes` to find the route for the application.

If you go to your new route you will see your change.

![UI](../.gitbook/assets/updated-ui.png)
