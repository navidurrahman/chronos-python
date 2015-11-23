chronos-python
==============

[![Build Status](https://travis-ci.org/asher/chronos-python.svg?branch=master)](https://travis-ci.org/asher/chronos-python)

Simple python api for the chronos job scheduler

###Installation

    python setup.py install

###Usage Example

```
import chronos
c = chronos.connect("chronos.mesos.server.com:8080")
# or specify multilple servers that will be tried in order
c = chronos.connect(["chronos1.mesos.server.com:8080", "chronos2.mesos.server.com:8080"])

# get list of scheduled jobs and their status as
# [{ 'name': 'job1', ..}, { 'name': 'job2', ..}]
jobs = c.list()

# run job by name
c.run("job123")

# add || update
job = { 'async': False, 'command': 'echo 1', 'epsilon': 'PT15M', 'name': 'foo',
    'owner': 'me@foo.com', 'disabled': True, 'schedule': 'R/2014-01-01T00:00:00Z/PT60M'}
try:
    c.add(job) # fails if job of the same name already exists
except:
    c.update(job)

# delete job by name
c.delete("job123")

# kill all tasks for a running/stuck job
c.delete_tasks("job123")
```


###Included Scripts
* `chronos-sync-jobs.py` - Sync chronos jobs from a directory tree containing job.json files.
`chronos-sync-jobs.py --hostname chronos.server.com:4400 --sync /path/to/job.json/files`

* `chronos-nagios.py` - Nagios/Icinga style monitor of jobs
`chronos-nagios.py --hostname chronos.server.com:4400 --crit 3 --prefix etl. --prefix data.`
`chronos-nagios.py --hostname chronos.server.com:4400 --crit 3 --exclude etl.`

* `chronos` - command line to update chronos jobs in yaml format
`# export CHRONOS_URL as environment variable and use following commands to see the outputs`
`chronos list`
`chronos delete -j <job_name>`
`Added support to create or update cronjobs using YAML files, you can specify all arguments in the yaml file, otherwise the job will be scheduled with default settings. In addition, environment files can be passed through -e argument`
`chronos create_or_update -e </path/to/envirnoment_file> -d <libmesos/ubuntu> -y yaml file`

### YAML file format
```
# YAML
name: job_list_file
owner: test@test.com

jobs:
- name: test_job_1
  schedule: R1/2014-09-25T17:22:00Z/PT2M
  command: "while sleep 10; do date =u %T; done"
  mem: 512
  cpus: 0.5

- name: test_job_2
  schedule: R1/2014-09-25T17:22:00Z/PT2M
  command: "while sleep 10; do date =u %T; done"
```
### ENV file format
```
TEST_ENV1=test
TEST_ENV2=test
TEST_ENV3=test
TEST_ENV4=test
TEST_ENV5=test
```
