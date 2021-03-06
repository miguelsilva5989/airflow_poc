# Airflow PoC

## Docker Postresql

[Docker Postgresql](https://github.com/bitnami/bitnami-docker-postgresql)

    docker pull bitnami/postgresql:latest

As PostgreSQL image is non-root, you need to set the proper permissions to the mounted directory in your host:

    sudo chown 1001:1001 /home/mike/workspace/git/airflow_poc/airflow_poc/db

Check docker IP address:

    docker ps
    docker inspect <container-id>
    copy the ipaddress

Connect to DB

    172.18.0.2

## Airflow Installation

(optional) Airflow needs a home, ~/airflow is the default.

    export AIRFLOW_HOME=~/airflow

    pip install apache-airflow

Configure DB sqlalchemy connection string to postgresql in airflow.cfg

    sql_alchemy_conn = postgresql://ze:zeManel@172.18.0.2:5432/postgresql

Initialize the database

    airflow initdb

## Airflow Start

* start the web server, default port is 8080
  
    airflow webserver -p 8080

* start the scheduler

    airflow scheduler

* Web server

    visit http://localhost:8080 in the browser and enable the example dag in the home page

* print the list of active DAGs
  * airflow list_dags
* prints the list of tasks the "tutorial" dag_id
  * airflow list_tasks tutorial2
* prints the hierarchy of tasks in the tutorial DAG
  * airflow list_tasks tutorial2 --tree

### Testing

    airflow test tutorial2 print_date 2015-06-01
    airflow test tutorial2 sleep 2015-06-01
    airflow test tutorial2 templated 2015-06-01

hide example DAGs, change in config:
    load_examples = True
    airflow resetdb

### tutorial

[Tutorial](https://airflow.apache.org/docs/stable/tutorial.html)
[default_args of a DAG](https://airflow.apache.org/docs/stable/_api/airflow/models/index.html#airflow.models.BaseOperator)

`backfill` will respect your dependencies, emit logs into files and talk to the database to record status. If you do have a webserver up, you’ll be able to track the progress. airflow webserver will start a web server if you are interested in tracking the progress visually as your backfill progresses.
Note that if you use depends_on_past=True, individual task instances will depend on the success of the preceding task instance, except for the start_date specified itself, for which this dependency is disregarded.
The date range in this context is a start_date and optionally an end_date, which are used to populate the run schedule with task instances from this dag.

    # start your backfill on a date range
    airflow backfill tutorial -s 2015-06-01 -e 2015-06-07
