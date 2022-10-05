# Simple Deployment

The `META-INF/server-init.cli` and `META-INF/server-init.yml` files add system properties to the management model. These are then read and output by the servlet handling requests.

To run, build the Helm chart as outlined in the parent README.

Then run the following to create the application. `simple-example` is the name of the application, and is used as
the prefix for all objects created, and as the final name for the created deployment, route and service.

```shell
# Create all the OpenShift resources needed to deploy our app
helm install simple-example /path/tp/managed-wildfly-chart/managed-wildfly-chart-0.1.0.tgz
# Deploy our war
oc start-build simple-example-deployment-build  --from-file=target/ROOT.war 
```
Then view the output either by finding the created route in the console, and clicking on the link, or by running:

```shell
curl https://$(oc get route simple-example --template='{{ .spec.host }}')
```
In the output you should see the values coming from the system properties set by `META-INF/server-init.cli` and `META-INF/server-init.yml`.