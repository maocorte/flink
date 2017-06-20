[![](https://images.microbadger.com/badges/image/maocorte/flink.svg)](http://microbadger.com/images/maocorte/flink "Get your own image badge on microbadger.com")

Flink in Docker
===============

This is a Docker image appropriate for running Flink. You can run it locally with docker-compose in which case you get three containers by default:
* `flink-jobmanager` - runs a Flink JobManager in cluster mode and exposes a port for Flink and a port for the WebUI.
* `flink-taskmanager` - runs a Flink TaskManager and connects to the Flink Job Manager via static DNS name `jobmanager`.
* `flink-history-server` - runs a Flink History Server

Usage
=====

Build
-----

You only need to build the Docker image if you have changed Dockerfile or the startup shell script, otherwise skip to the next step and start using directly.

To build, get the code from Github, change as desired and build an image by running `docker build .`

Run locally
-----------

Get the `docker-compose.yml` from Github and then use the following snippets

**Start JobManager and TaskManager**

`docker-compose up -d` will start in background a JobManager with a single TaskManager and the History Server.

**Scale TaskManagers**

`docker-compose scale taskmanager=5` will scale to 5 TaskManagers.

**Deploy and Run a Job**

1. Copy the Flink job JAR to the Job Manager

`docker cp /path/to/job.jar $(docker ps --filter name=jobmanager --format={{.ID}}):/job.jar` to

2. Copy the data to each Flink node if necessary

```bash
for i in $(docker ps --filter name=flink --format={{.ID}}); do
  docker cp /path/to/data.csv $i:/data.csv
done
```

3. Run the job

`docker exec -it $(docker ps --filter name=jobmanager --format={{.ID}}) flink run -c <your_job_class> /job.jar [optional params]`

where optional params could for example point to the dataset copied at the previous step.

**Accessing Flink Web Dashboard**

Navigate to [http://localhost:8081](http://localhost:8081)

**Stop Flink Cluster**

`docker-compose down` shuts down the cluster.

Disclaimer
==========

Apache®, Apache Flink™, Flink™, and the Apache feather logo are trademarks of [The Apache Software Foundation](http://apache.org).
