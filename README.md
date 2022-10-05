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



