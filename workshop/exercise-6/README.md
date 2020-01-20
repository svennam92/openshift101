# Exercise 6: Patient Database for Example Health

WIP -- Raw instructions below.

```
ibmcloud login --sso
```

Pick your own account, not IBM.

```
ibmcloud target -g Default
or
ibmcloud target -g default
```

```
ibmcloud target --cf
```

```
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.13.0/install.sh | bash -s 0.13.0
curl -sL https://raw.githubusercontent.com/IBM/cloud-operators/master/hack/install-operator.sh | bash
oc create -f https://operatorhub.io/install/ibmcloud-operator.yaml
```

```
kubectl get csv -n operators
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
oc describe services.ibmcloud
```

First verify that there's no bugs and the service in OpenShift is "online". Next, verify the Cloudant service is created by that command in your account: https://cloud.ibm.com/resources

If your service is online, then create the service binding YAML for the Cloudant database to create the credentials:

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

To get the secret with your credentials, run:
```
kubectl get secret cloudant-binding
```

Create a new app from: https://github.com/svennam92/nodejs-patientdb-cloudant

```
oc new-app centos/nodejs-10-centos7~https://github.com/svennam92/nodejs-patientdb-cloudant
```

Navigate to the deployment config for this app, go to the `Environment` tab, create a new environment variable named `CLOUDANT_URL` from a secret. Choose the `cloudant-binding` secret, then choose `url` for the Key. Hit the `Reload` button.

```
oc expose svc/nodejs-patientdb-cloudant
```

```
oc get routes
```

Copy the route. Open the Example Health application (the other deployment), go to the settings tab, input the route and hit the OpenShift icon.

Now login using `opall:opall`.