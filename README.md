# Eigo-Mt-Fuji redash

[! [Redash 5.0.2] (https://img.shields.io/badge/redash-v5.0.2-ff7964.svg)] (https://github.com/getredash/redash)

## Diagram
* Red frame below

! [re: dash architecture] (./ docs / redash.png)

## Advance preparation
* Read [re: dash's dev-guide] (https://redash.io/help/open-source/dev-guide/setup)

* Create AWS CloudWatch Log Group
  * Assuming `/ aws / elasticbeanstalk / Test2-env-1 / redash`
  * Replace `logConfiguration` in Dockerrun.aws.json if you want to change
* Create ElasticBeanstalk application
  * Platform: Multi docker container
  * Single Instance
* Create RDS for Postgres 9.5
  * Alternative: add postgres to containerDefinitions in Dockerrun.aws.json
  * Apply the instance connection information created in the environment variable `REDASH_DATABASE_URL` of Dockerrun.aws.json
  * [Dash of Redash] (./ resources / redash-data.sql) is restored on postgres schema of created instance
* Create ElastiCache (Redis)
  * Alternative: Add redis to containerDefinitions in Dockerrun.aws.json
  * Apply the instance connection information created in the environment variable `REDASH_REDIS_URL` of Dockerrun.aws.json

* Set up the deployment tool [eb cli] (https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/eb-cli3-install-osx.html)

## Deploy (Deploy Dockerrun.aws.json to ElasticBeanstalk)

* init (first time)

`` `
$ eb init --profile {profile} test2
`` `

* deploy (deploy to Test2-env-1 on test2 application)

`` `
$ eb deploy --profile {profile} Test2-env-1
`` `

## Reference

* Redash docker-compose in local

`` `
# create base pg_dump (only github maintainer)
$ git clone github.com/getredash/redash.git redash-original
$ cd redash-original
$ docker-compose build # if no ports mapping for postgresql, add 15432: 5432 before build.
$ docker-compose up
`` `

* Get Redash DDL (local)

`` `
# drop schema (only if reset required.)

$ psql -h127.0.0.1 --port 15432 -Upostgres
drop schema public cascade;
create schema public;

# create table in docker container (only at first contact)
$ docker exec -it {redash server container_id} / bin / bash
bin / run ./manage.py database create_tables
$ pg_dump -h127.0.0.1 --port 15432 -Upostgres postgres> resources / redash-data.sql
`` `

* Redash table creation (AWS)

`` `
# create tables
redash-db-ssh # connect to postgresql on aws
redash-db <resources / redash-data.sql # restore
`` `

* Reference

`` `
https://redash.io/help/open-source/dev-guide/setup
https://stackoverflow.com/questions/32311366/alembic-util-command-error-cant-find-identifier
https://qiita.com/a-suenami/items/e231adc2e083ef9449f6
