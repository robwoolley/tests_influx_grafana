# Zephyr Hardware Results Dashboard

## Set up InfluxDB and Grafana

### Option 1: Using Docker Compose

Pre-requisites
* [Docker-compose](https://docs.docker.com/compose/install/) must be installed on the docker host.


This option will start the multi-container application using docker-compose. Make sure to run the following commands from the top-level directory where docker-compose.yml is present.

1. Edit the file .env to set the environment variables used by docker-compose.yml.  This includes the default username and passwords for InfluxDB and Grafana.
   ```
   INFLUXDB_USERNAME=admin
   INFLUXDB_PASSWORD=admin

   GRAFANA_USERNAME=admin
   GRAFANA_PASSWORD=admin
   ```
1. Execute docker-compose to run the multi-container application in detached mode.
   ```
   docker-compose up -d
   ```
1. Execute docker-compose ps to list the currently running services.
   ```
   $ docker-compose ps
              Name                           Command            State                    Ports
   ----------------------------------------------------------------------------------------------------------------
   tests_influx_grafana_chronograf_1   /entrypoint.sh chronograf   Up      127.0.0.1:8888->8888/tcp
   tests_influx_grafana_grafana_1      /run.sh                     Up      0.0.0.0:3000->3000/tcp,:::3000->3000/tcp
   tests_influx_grafana_influxdb_1     /entrypoint.sh influxd      Up      0.0.0.0:8086->8086/tcp,:::8086->8086/tcp
   ```
1. Stop the application and its services using the down command for docker-compose
   ```
   docker-compose down
   ```

Grafana will be accessible on the localhost on port 3000.

Import the data using the ``import.sh`` script.

### Option 2: Manual

1. Install InfluxDB and Grafana and verify that both are running.

2. Create a database, named zephyr_test_results in InfluxDB that will host the test results.

   ```
   nashif@master:~$ influx
   Connected to http://localhost:8086 version 1.8.4
   InfluxDB shell version: 1.8.4
   > CREATE DATABASE zephyr_test_results
   ```

3. In Grafana, create a new data source and add the InfluxDB database (zephyr_test_results)

4. In Grafana, import the dashboards available in grafana-provisioning/dashboards/ directory.
   - overall.json
   - test_results.json

## Clone the Zephyr repositories

1. Change directories to the top-level of this repository
1. Clone the main zephyr repository:
   ```
   git clone https://github.com/zephyrproject-rtos/zephyr.git
   ```
1. Clone the zephyr test results repository:
   ```
   git clone https://github.com/zephyrproject-rtos/test_results.git
   ```

1. Change the configuration in the script import.sh and make sure the following variables reflect what you have created above:

   ```
   RESULTS_REPO_PATH=/path/to/test_results
   ZEPHYR_REPO_PATH=/path/to/zephyr/
   INFLUX_DB=influxdb://localhost:8086/zephyr_test_results
   ```

1. Create a virtuenv directory
   ```
   mkdir -p $HOME/virtualenvs
   ```

1. Create a virtualenv
   If virtualenv was installed as a package:
   ```
   virtualenv $HOME/virtualenvs/zephyr
   ```
   Otherwise, if virtualenv was installed via a Python 3.6+ module
   ```
   python -m venv $HOME/virtualenvs/popper
   ```
1. Activate the virtualenv
   ```
   source $HOME/virtualenvs/zephyr/bin/activate
   ```

1. Install junit2influx using pip:
   ```
   pip3 install junit2influx
   ```

There are 2 ways the import script can be called:

- Bulk import: This will import all results (v2.5.0 results) in one call. For
  this, call the script without any arguments
- Single Run: Specify the commit you want to import as the first argument to import.sh
- Commit import: Only import new files that are part of a single commit. The
  first argument should be a commit hash in the test_results repo



