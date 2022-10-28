# Managed Server Deployments

A set of example deployments that work with the https://github.com/kabir/managed-wildfly-chart Helm chart.

For all examples, the Helm cart must have been built as outlined in the Helm chart 
[README](https://github.com/kabir/managed-wildfly-chart). 
This also contains instructions on how to update the application.

See each example's README for how to run it. To remove everything to prepare for the next example, run:
```shell
helm uninstall <name>
```

* [simple](simple) - deploys a simple servlet which outputs the values of system properties set by yaml and cli scripts used to configure the management model.
* [postgres](postgres) - connects to a Postgres database and configures a datasource to connect to it.
* [rhosak](rhosak) - connects a MicroProfile Reactive Messaging application to a Kafka instance provided by RHOSAK.



## Pulling the docker images

Each build will typically push the built images to an imagestream. To inspect the contents of the images, you can
do the following to be able to pull them:

```shell
export OPENSHIFT_NS=$(oc project -q)
oc registry login
# Copy the route in the env variable OPENSHIFT_IMAGE_REGISTRY
OPENSHIFT_IMAGE_REGISTRY=$(oc registry info)
docker login -u openshift -p $(oc whoami -t)  $OPENSHIFT_IMAGE_REGISTRY
```
Then we can pull our image
```shell
docker pull ${OPENSHIFT_IMAGE_REGISTRY}/${OPENSHIFT_NS}/<image-stream-name>:<tag>
```



