# Airflow PoC

## Docker MariaDB

* https://hub.docker.com/r/bitnami/mariadb/
* https://github.com/bitnami/bitnami-docker-mariadb

    docker ps
    docker inspect <container-id>
    copy the ipaddress

Connect to DB

    mysql -h 172.18.0.2 -uroot -pzeManel mysql

## Airflow Installation

    # airflow needs a home, ~/airflow is the default,
    # but you can lay foundation somewhere else if you prefer
    # (optional)
    export AIRFLOW_HOME=~/airflow

    # install from pypi using pip
    pip install apache-airflow

    # initialize the database
    airflow initdb

    # start the web server, default port is 8080
    airflow webserver -p 8080

    # start the scheduler
    airflow scheduler

Web

    visit localhost:8080 in the browser and enable the example dag in the home page


# print the list of active DAGs

    airflow list_dags

# prints the list of tasks the "tutorial" dag_id

    airflow list_tasks tutorial

# prints the hierarchy of tasks in the tutorial DAG

    airflow list_tasks tutorial --tree


## testing

    airflow test tutorial print_date 2015-06-01
    airflow test tutorial sleep 2015-06-01
    airflow test tutorial templated 2015-06-01


hide example DAGs, change in config:
    load_examples = True
    airflow resetdb


## tutorial
https://airflow.apache.org/docs/stable/tutorial.html

default_args of a DAG:
    https://airflow.apache.org/docs/stable/_api/airflow/models/index.html#airflow.models.BaseOperator


`backfill` will respect your dependencies, emit logs into files and talk to the database to record status. If you do have a webserver up, you’ll be able to track the progress. airflow webserver will start a web server if you are interested in tracking the progress visually as your backfill progresses.
Note that if you use depends_on_past=True, individual task instances will depend on the success of the preceding task instance, except for the start_date specified itself, for which this dependency is disregarded.
The date range in this context is a start_date and optionally an end_date, which are used to populate the run schedule with task instances from this dag.

    # start your backfill on a date range
    airflow backfill tutorial -s 2015-06-01 -e 2015-06-07
