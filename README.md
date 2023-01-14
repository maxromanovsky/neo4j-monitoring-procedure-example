Monitoring procedure example
============================

# Updates to the original version

- https://graphaware.com/neo4j/2019/06/11/monitoring-neo4j-prometheus.html
- https://neo4j.com/developer/kb/how-to-monitor-neo4j-with-prometheus/
- https://neo4j.com/docs/operations-manual/current/installation/requirements/#deployment-requirements-java
- https://neo4j.com/docs/java-reference/4.4/extending-neo4j/project-setup/
- https://neo4j.com/docs/upgrade-migration-guide/current/version-4/migration/drivers/java-driver/
- https://github.com/neo4j-examples/neo4j-procedure-template/tree/4.4
- https://central.sonatype.dev/artifact/org.neo4j/neo4j/4.4.16/pom
- https://central.sonatype.dev/artifact/org.neo4j/parent/4.4.16/pom
- https://central.sonatype.dev/artifact/org.neo4j.driver/neo4j-java-driver/4.4.11/versions
- http://localhost:2004


It looks like Dropwizard Metrics lack labels: https://www.robustperception.io/exposing-dropwizard-metrics-to-prometheus/

## Server Configuration
```
# Enable the Prometheus endpoint. Default is 'false'.
metrics.prometheus.enabled=true
# The IP and port the endpoint will bind to in the format <hostname or IP address>:<port number>.
# The default is localhost:2004.
metrics.prometheus.endpoint=localhost:2004
```

## macOS config for building the plugin for Neo4j v4.4.16
```bash
brew install maven openjdk@11
```

## Plugin build
```bash
mvn clean package
```

- Copy plugin from `target/monitoring-procedure-example.jar` to Plugins dir
- Restart Neo4j
- Check http://localhost:2004 - there shouldn't be a custom metric
- Run cypher: `call example.monitoring`
- Check http://localhost:2004 - there should be custom metrics (probably at the very top), starting with `com_graphaware_neo4j_monitoring_MonitorProcedure`

# Original instructions

This is a repository accompanying a blogs post about how to create custom metrics in Neo4j and publish them to Prometheus, and visualise them in Grafana.

[Monitoring Neo4j and Procedures with Prometheus and Grafana - Part 1](https://graphaware.com/neo4j/2019/06/11/monitoring-neo4j-prometheus.html)

[Monitoring Neo4j and Procedures with Prometheus and Grafana - Part 2](https://graphaware.com/neo4j/2019/06/14/monitoring-neo4j-prometheus-part-2.html)

This repository contains a single module, where we have an example procedure with our own custom metrics published to Prometheus on the same URl as the Neo4j metrics.
The integration test starts a [Neo4j Testcontainer](https://www.testcontainers.org/modules/databases/neo4j/) where we have Neo4j Enterprise edition.
If you want to read more about Neo4j and Testcontainers then read [this blog post](https://graphaware.com/docker,/testing/2018/12/16/integration-testing-with-docker-neo4j-image-and-testcontainers.html).

We use a Neo4jContainer and modified it to expose the Prometheus port.
To be able to use Prometheus with Neo4j we need to call the withEnterpriseEdition method,
and you have to add a container-license-acceptance.txt file to the root directory of your test resources, containing the text
"neo4j:3.5.0-enterprise" in one line. With this you will accept the license terms and conditions.
You'll find more information about licensing Neo4j here: [About Neo4j Licenses](https://neo4j.com/licensing/).
If you are interested in a license, you can contact us directly at [GraphAware](mailto:info@graphaware.com).

Building
--------
This project uses maven, to build a jar-file with the example procedure. To produce the jar simply package the project with maven:

    mvn clean package

This will produce a jar-file,`target/monitoring-procedure-examle.jar`,
that can be deployed into the `plugin` directory of your Neo4j instance.

Invoking the procedure
----------------------
When you deployed the procedure, you can call it from the Neo4j browser:

    call example.monitoring
