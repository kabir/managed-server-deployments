# RHOSAK application Deployment

## Setting up RHOSAK
First we need to create and configure a RHOSAK instance, and grab the connection information which will be stored in a 
Kubernetes secret. 

Make sure you have the latest version of the [rhoas CLI](https://github.com/redhat-developer/app-services-cli/releases) i
nstalled.

Log in and create your application. Substitute `kabir-kafka` with your own name (names must be unique on the sandbox).
```shell
rhoas login
rhoas kafka create --name kabir-kafka
```
This will take a few minutes. Once the Status of `rhoas context status kafka` is `ready` we can proceed to the next step.

Switch the context to our Kafka instance, and create a topic called `testing`.
````shell
rhoas context set-kafka --name kabir-kafka 
rhoas kafka topic create --name testing
````
Generate the config to connect to Kafka
```shell
# Stores its data in rhoas.env
rhoas generate-config --type=env
```
Create a service account. 
```shell
rhoas service-account create
? Short Description: kabir-account
? Credentials file format: env
? Credentials file location /Users/kabir/.env

✔️  Service account created successfully with ID "xxxxxxxxx-1234-1234-1234-xxxxxxxxxxxx"
✔️  Credentials saved to /Users/kabir/.env

You can now set access rules for your current services.
To grant full access to produce and consume Kafka messages, enter this command:

 $ rhoas kafka acl grant-access --producer --consumer --service-account xxxxxxxxx-1234-1234-1234-xxxxxxxxxxxx --topic all --group all

To grant read and write access to the currently selected Service Registry instance, enter this command:

 $ rhoas service-registry role add --role=manager --service-account xxxxxxxxx-1234-1234-1234-xxxxxxxxxxxx
```
Combine the outputs of the two previous commands into rhoas.env
```shell
cat .env >> rhoas.env
```

Grant access as prompted in the output of the `rhoas service-acount create` command:
```shell
rhoas kafka acl grant-access --producer --consumer --service-account xxxxxxxxx-1234-1234-1234-xxxxxxxxxxxx --topic all --group all --yes
```

Finally we create a secret called `rhoas` with the contents of the rhoas.env file from above
```shell
oc create secret generic rhoas --from-env-file=./rhoas.env
```

## Application

The [helm.yml](helm.yml) mounts our secret under `/etc/config/rhoas` in the application pods.

[META-INF/server-init.cli](src/main/resources/META-INF/server-init.cli) configures the MicroProfile Config subsystem
to use the `/etc/config/rhoas` directory as a config source. It also adds properties to the MicroProfile Config subsystem
to provide the RHOSAK connection information.

[META-INF/server-config.xml](src/main/resources/META-INF/server-config.xml) configures the server to use the
`cloud-server` and `microprofile-reactive-messaging-kafka` layers.

To run, package the Helm chart as outlined in the parent README. 

Then run the following to create the application. `rhosak-example` is the name of the application, and is used as 
the prefix for all objects created, and as the final name for the created deployment, route and service.
```shell
# Create all the OpenShift resources needed to deploy our app
helm install rhosak-example -f helm.yml /path/tp/managed-wildfly-chart/managed-wildfly-chart-0.1.0.tgz
# Deploy our war
oc start-build rhosak-example-deployment-build  --from-file=target/ROOT.war 
```
Once the application is up and running save some values by running
```shell
curl -X POST https://$(oc get route rhosak-example --template='{{ .spec.host }}')/one
curl -X POST https://$(oc get route rhosak-example --template='{{ .spec.host }}')/two
```
And to read the values we stored
```shell
curl https://$(oc get route rhosak-example --template='{{ .spec.host }}')
```