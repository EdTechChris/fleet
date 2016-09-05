# Configs and Tools for Testing Kolide

The files in this directory are intended to assist with Kolide development.

* `docker-compose.yml`: This docker-compose file helps with starting `osqueryd` instances for testing Kolide. More on this [below](#testing-with-containerized-osqueryd).

* `example_config.json`: An example config file with insecure default passwords. Useful for testing in a local dev environment, but should /never/ be used in production.

* `example_osquery.conf`: An example osquery config file that sets up basic configuration for distributed queries.

* `example_osquery.flags`: An example osquery flagfile setting the config options that must be loaded before the full JSON config.

* `kolide.crt` & `kolide.key`: Self-signed SSL certificate & key useful for testing locally with `osqueryd`. Works with domains `localhost` and `dockerhost`. Should /never/ be used in production.


## Testing with containerized osqueryd

Using Docker enables us to rapidly spin up and down pre-configured `osqueryd` instances for testing Kolide. Currently we have container images for Ubuntu14 and Centos7 osquery installations.

### Setup

Docker and docker-compose are the only dependencies. The necessary container images will be pulled from Docker Cloud on first run.

Before using the following commands, set the environment variable `LOCALHOST` to the public IP (127.0.0.1 will not work) of the docker host machine. This will allow the containers to connect to the local Kolide server.

### Running osqueryd

The osqueryd instances are configured to use the TLS plugins at `$LOCALHOST:8080`. Using the `example_config.json` in this directory should configure Kolide with the appropriate settings for these `osqueryd` containers to connect.

To start one instance each of Centos and Ubuntu `osqueryd`, use:

```
docker-compose up
```

The logs will be displayed on the host shell. Note that `docker-compose up` will reuse containers (so the state of `osqueryd` will be maintained across calls). To remove the containers and start from a fresh state on the next call to `up`, use:

```
docker-compose rm
```

If you want to only start one instance of `osqueryd`, use:

```
docker-compose run ubuntu14-osqueryd
```
or
```
docker-compose run centos7-osqueryd
```

Note that `docker-compose run` does not save state between calls.

This system can also be used to start many instances of osqueryd running in containers on the same host:

```
docker-compose up -d && docker-compose scale ubuntu14-osquery=10 centos7-osquery=10
```

To stop the containers when running in detached mode like this, use:

```
docker-compose stop
```

And to delete the containers when wanting a fresh state, or when finished testing, use:

```
docker-compose rm
```

We have had no trouble running up to 100 containerized osqueryd instances on a single processor core and about 1GB of RAM.