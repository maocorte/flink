# Flink in Docker

This is a Docker image appropriate for running Flink in Kuberenetes. You can also run it locally with docker-compose in which case you get two containers by default:
* `flink-jobmanager` - runs a Flink JobManager in cluster mode and exposes a port for Flink and a port for the WebUI.
* `flink-taskmanager` - runs a Flink TaskManager and connects to the Flink Job Manager via static DNS name `flink-jobmanager`.

The Docker setup is heavily influenced by [docker-flink](https://github.com/apache/flink/tree/master/flink-contrib/docker-flink)

# Usage

## Build

You only need to build the Docker image if you have changed Dockerfile or the startup shell script, otherwise skip to the next step and start using directly.

To build, get the code from Github, change as desired and build an image by running `make`

## Run locally

Get the `docker-compose.yml` from Github and then use the following snippets

##### Start JobManager and TaskManager
`docker-compose up -d` will start a JobManager with a single TaskManager in background with the History Server.

##### Scale TaskManagers
`docker-compose scale flink-taskmanager=5` will scale to 5 TaskManagers.

##### Deploy and Run a Job

1. Copy the Flink job JAR to the Job Manager

`docker cp /path/to/job.jar $(docker ps --filter name=flink-jobmanager --format={{.ID}}):/job.jar` to

2. Copy the data to each Flink node if necessary

```bash
for i in $(docker ps --filter name=flink --format={{.ID}}); do
  docker cp /path/to/data.csv $i:/data.csv
done
```

3. Run the job

`docker exec -it $(docker ps --filter name=flink-jobmanager --format={{.ID}}) flink run -c <your_job_class> /job.jar [optional params]`

where optional params could for example point to the dataset copied at the previous step.

##### Accessing Flink Web Dashboard

Navigate to [http://localhost:8081](http://localhost:8081)

##### Stop Flink Cluster
`docker-compose down` shuts down the cluster.

# Contribution

You are very welcome to open an issue or a PR on Github.
