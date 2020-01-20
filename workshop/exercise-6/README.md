# Exercise 6: Patient Database for Example Health

WIP -- Raw instructions below.

```
ibmcloud login --sso
```

Pick your own account, not IBM.

Interactively target your CF org, space and resource group where the Cloudant service will be created. Resource group is usually named `default` or `Default` -- case-sensitive.

```
ibmcloud target --cf -g Default
or
ibmcloud target --cf -g default
```

Install the operator framework, install the operator and set-up the API tokens that the Operator will use.

```
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.13.0/install.sh | bash -s 0.13.0
oc create -f https://operatorhub.io/install/ibmcloud-operator.yaml
curl -sL https://raw.githubusercontent.com/IBM/cloud-operators/master/hack/install-operator.sh | bash
```

```shell
oc get csv -n operators
```

Output:

```shell
NAME                       DISPLAY              VERSION   REPLACES                   PHASE
ibmcloud-operator.v0.1.3   IBM Cloud Operator   0.1.3     ibmcloud-operator.v0.1.2   Succeeded
```

Then, create the service YAML for the Cloudant database.

```
# cloudant.yaml

apiVersion: ibmcloud.ibm.com/v1alpha1
kind: Service
metadata:
  name: cloudant-service
spec:
  plan: lite
  serviceClass: cloudantnosqldb
```

```
oc create -f cloudant.yaml
```

```
oc get services.ibmcloud
```

Output:

```
NAME               STATUS   AGE
cloudant-service   Online   66m
```

If there's any issues, you can debug with the following command:

```
oc describe services.ibmcloud
```

First, verify that there's no bugs and the service is "online". Next, verify the Cloudant service is created by that command in your account: https://cloud.ibm.com/resources

If your service is online, then create the service binding resource. This will bind the credentials to a secret in your OpenShift cluster.

```
# cloudant-binding.yaml

apiVersion: ibmcloud.ibm.com/v1alpha1
kind: Binding
metadata:
  name: cloudant-binding
spec:
  serviceName: patientdata-cloudant
```

```
oc create -f cloudant-binding.yaml
```

```
oc get bindings.ibmcloud 
```

Output:
```
NAME        STATUS   AGE
mybinding   Online   64m
```

To get the secret with your credentials, run:
```
oc get secret cloudant-binding
```

Create the Node.js app that will populate your Cloudant DB with patient data. It will also serve data to the front-end application that we deployed in the first exercise. Run the following command to create this application:

```
oc new-app --name=db-backend centos/nodejs-10-centos7~https://github.com/svennam92/nodejs-patientdb-cloudant
```

App will crash and fail to start because the credentials to the Cloudant DB haven't been set yet. Do that now:

Navigate to the deployment config for this app, go to the `Environment` tab, create a new environment variable named `CLOUDANT_URL` from a secret. Choose the `cloudant-binding` secret, then choose `url` for the Key. Hit the `Reload` button.

```
oc expose svc/nodejs-patientdb-cloudant
```

```
oc get routes
```

Copy the route for the `db-backend` service. Open the route for the Example Health application `node-s2i-openshift` in a browser. Then, go to the `Settings` tab, input the route and hit the OpenShift icon.

Now login using `opall:opall`. Your application is backed by data in the cloudantdb.