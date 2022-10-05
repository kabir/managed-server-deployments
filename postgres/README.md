# Postgresql datasource Deployment


Fist we need to set up a Postgresql instance:
```shell
oc new-app --name database-server \
     --env POSTGRESQL_USER=admin \
     --env POSTGRESQL_PASSWORD=admin \
     --env POSTGRESQL_DATABASE=sampledb \
     postgresql:latest
```

[META-INF/server-config.xml](src/main/resources/META-INF/server-config.xml) configures the server to use the
`cloud-server` and `postgresql-datasource` layers which are needed.

The [helm.yml](helm.yml) sets environment variables understood by the configuration added by the `postgresql-datasource`
layer to connect to our database.


To run, package the Helm chart as outlined in the parent README. 

Then run the following to create the application. `postgres-example` is the name of the application, and is used as 
the prefix for all objects created, and as the final name for the created deployment, route and service.
```shell
# Create all the OpenShift resources needed to deploy our app
helm install postgres-example -f helm.yml /path/tp/managed-wildfly-chart/managed-wildfly-chart-0.1.0.tgz
# Deploy our war
oc start-build postgres-example-deployment-build  --from-file=target/ROOT.war 
```
Once the application is up and running save some values by running
```shell
curl -X POST https://$(oc get route postgres-example --template='{{ .spec.host }}')/one
curl -X POST https://$(oc get route postgres-example --template='{{ .spec.host }}')/two
```
And to read the values we stored
```shell
curl https://$(oc get route postgres-example --template='{{ .spec.host }}')
```