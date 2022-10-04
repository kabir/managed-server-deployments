# Simple Deployment

The `META-INF/server-init.cli` and `META-INF/server-init.yml` files add system properties to the management model. These are then read and output by the servlet handling requests.

To run, build the Helm chart as outlined in the parent README, and run the following:
```
# Create all the OpenShift resources needed to deploy our app
helm install <name>/path/tp/managed-wildfly-chart/managed-wildfly-chart-0.1.0.tgz
# Deploy our war
oc start-build <name>-deployment-build  --from-file=target/ROOT.war 
```
