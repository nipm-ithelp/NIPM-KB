[![Build Status](https://travis-ci.org/hakyimlab/ukbrest.svg?branch=master)](https://travis-ci.org/hakyimlab/ukbrest)
[![Coverage Status](https://coveralls.io/repos/github/hakyimlab/ukbrest/badge.svg?branch=master)](https://coveralls.io/github/hakyimlab/ukbrest?branch=master)
[![Docker Automated build](https://img.shields.io/badge/Docker%20build-automated-blue.svg)](https://hub.docker.com/r/hakyimlab/ukbrest/)


# ukbREST



# Installation

If you just want to give ukbREST a try, and you are not a UK Biobank user, you
can follow the [guide in the wiki](https://github.com/hakyimlab/ukbrest/wiki)
and use our simulated data.



## Step 2: Setup
Here we are going to start PostgreSQL and load the phenotype data into it.
Start Docker in your server/computer and pull the PostgreSQL and ukbREST images:

```
$ singularity pull docker://postgres:12
```

```
$ singularity pull docker://hakyimlab/ukbrest
```

First set up some directories and environmental variables 

```
mkdir postgres
export COMMON=/scratch/han_lab
POSTGRES_HOME=$COMMON/postgres
mkdir -p $POSTGRES_HOME/{config,db/data,run}
uuidgen > $POSTGRES_HOME/config/postgres-password
chmod 600 $POSTGRES_HOME/config/postgres-password
export POSTGRES_HOME=$COMMON/postgres
export POSTGRES_PASSWORD_FILE=$POSTGRES_HOME/config/postgres-password
export POSTGRES_USER=$USER
export POSTGRES_DB=ukb
export PGDATA=$POSTGRES_HOME/db/data
POSTGRES_HOST_AUTH_METHOD=md5
POSTGRES_INITDB_ARGS="--data-checksums"
export POSTGRES_PORT=$(shuf -i 2000-65000 -n 1)
```


Start the PostgreSQL container :

```
singularity run -B $POSTGRES_HOME/db:/var/lib/postgresql -B $POSTGRES_HOME/run:/var/run/postgresql -B /scratch postgres_12.sif -c "port=$POSTGRES_PORT"
```
```
mhan@node01:/scratch/han_lab$ singularity run -B $POSTGRES_HOME/db:/var/lib/postgresql -B $POSTGRES_HOME/run:/var/run/postgresql -B /scratch postgres_12.sif -c "port=$POSTGRES_PORT"
The files belonging to this database system will be owned by user "mhan".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are enabled.

fixing permissions on existing directory /var/lib/postgresql/data ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

initdb: warning: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /var/lib/postgresql/data -l logfile start

waiting for server to start....2022-03-30 11:20:18.182 PDT [19805] LOG:  starting PostgreSQL 12.10 (Debian 12.10-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
2022-03-30 11:20:18.184 PDT [19805] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2022-03-30 11:20:18.198 PDT [19806] LOG:  database system was shut down at 2022-03-30 11:20:17 PDT
2022-03-30 11:20:18.202 PDT [19805] LOG:  database system is ready to accept connections
 done
server started
CREATE DATABASE


/usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*

2022-03-30 11:20:18.470 PDT [19805] LOG:  received fast shutdown request
waiting for server to shut down....2022-03-30 11:20:18.472 PDT [19805] LOG:  aborting any active transactions
2022-03-30 11:20:18.473 PDT [19805] LOG:  background worker "logical replication launcher" (PID 19812) exited with exit code 1
2022-03-30 11:20:18.473 PDT [19807] LOG:  shutting down
2022-03-30 11:20:18.485 PDT [19805] LOG:  database system is shut down
 done
server stopped

PostgreSQL init process complete; ready for start up.

2022-03-30 11:20:18.588 PDT [19755] LOG:  starting PostgreSQL 12.10 (Debian 12.10-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
2022-03-30 11:20:18.590 PDT [19755] LOG:  listening on IPv4 address "0.0.0.0", port 44245
2022-03-30 11:20:18.590 PDT [19755] LOG:  listening on IPv6 address "::", port 44245
2022-03-30 11:20:18.592 PDT [19755] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.44245"
2022-03-30 11:20:18.605 PDT [19833] LOG:  database system was shut down at 2022-03-30 11:20:18 PDT
2022-03-30 11:20:18.609 PDT [19755] LOG:  database system is ready to accept connections
```

Remember that you will have to keep this server running while you query with ukbREST.

Keep in mind that the above command runs PostgreSQL with the default settings. That could make it work **really slow** when
you send a query to ukbREST. See the installation instructions in the
[wiki](https://github.com/hakyimlab/ukbrest/wiki) for more details.

Then use the ukbREST Docker image to load your phenotype data into the PostgreSQL database.
Here we are only loading your CSV/HTML main datasets, but keep in mind that you can also load **Sample-QC**
or **relatedness data**, which is provided separately in UK Biobank. This is covered in
[the wiki](https://github.com/hakyimlab/ukbrest/wiki/Load-real-UK-Biobank-data).

In the command below, replace the bold text with the full path of both your phenotype and genotype folder,
as well as the right name of your `.sample` file.
Here we are going to use the simulated data provided in the ukbREST repository under tests/data/. I've copied over the test/data into my scratch location at /scratch/han_lab/ukbrest_tests/data. You will have to change it to where your file is. 


For the postgres connection, the username is your username for val, and the password is randomly genrated and saved in the file $POSTGRES_HOME/config/postgres-password, the port is also the randomly generated port that is assigned to $POSTGRES_PORT. You will have to check the output of the postgres to find port number. look for the lines such as 
```
2022-03-30 11:20:18.590 PDT [19755] LOG:  listening on IPv4 address "0.0.0.0", port 44245
2022-03-30 11:20:18.590 PDT [19755] LOG:  listening on IPv6 address "::", port 44245
```
Replace the bold with your own username password and port.

<pre>
singularity run -B /scratch -B <b>/scratch/han_lab/ukbrest_tests/data/pheno2sql/example14</b>:/var/lib/genotype -B <b>/scratch/han_lab/ukbrest_tests/data/pheno2sql/example14</b>:/var/lib/phenotype --env UKBREST_GENOTYPE_BGEN_SAMPLE_FILE="impv2.sample" --env UKBREST_DB_URI="postgresql://<b>mha</b>n:<b>b1400ec2-15cd-4b71-8e52-3a0ca006ad31</b>@node01:<b>44245</b>/ukb" ukbrest_latest.sif --load
</pre>

<pre>
singularity run -B /scratch -B //scratch/han_lab/ukbrest_tests/data/pheno2sql/example14:/var/lib/genotype -B /scratch/han_lab/ukbrest_tests/data/pheno2sql/example14:/var/lib/phenotype --env UKBREST_GENOTYPE_BGEN_SAMPLE_FILE="impv2.sample" --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif --load
2022-03-30 11:21:00,292 - ukbrest - WARNING - UKBREST_SQL_CHUNKSIZE was not set, no chunksize for SQL queries, what can lead to memory problems.
2022-03-30 11:21:00,292 - ukbrest - INFO - Loading phenotype data into database
2022-03-30 11:21:00,293 - ukbrest - INFO - Working on /var/lib/phenotype/example14_00.csv
2022-03-30 11:21:00,293 - ukbrest - INFO - Creating database tables
2022-03-30 11:21:00,299 - ukbrest - INFO - Getting columns types
2022-03-30 11:21:00,299 - ukbrest - INFO - Reading data types from /var/lib/phenotype/example14_00.html
2022-03-30 11:21:00,479 - ukbrest - INFO - Table ukb_pheno_0_00 (8 columns)
2022-03-30 11:21:00,521 - ukbrest - INFO - Writing temporary CSV files
2022-03-30 11:21:00,705 - ukbrest - WARNING - No encodings.txt found, assuming utf-8
2022-03-30 11:21:00,833 - ukbrest - INFO - Loading CSV files into database
2022-03-30 11:21:01,029 - ukbrest - INFO - /scratch/tmp/ukb_pheno_0_00.csv -> ukb_pheno_0_00
2022-03-30 11:21:01,353 - ukbrest - INFO - Working on /var/lib/phenotype/example14_01.csv
2022-03-30 11:21:01,353 - ukbrest - INFO - Creating database tables
2022-03-30 11:21:01,358 - ukbrest - INFO - Getting columns types
2022-03-30 11:21:01,358 - ukbrest - INFO - Reading data types from /var/lib/phenotype/example14_01.html
2022-03-30 11:21:01,394 - ukbrest - INFO - Table ukb_pheno_1_00 (8 columns)
2022-03-30 11:21:01,438 - ukbrest - INFO - Writing temporary CSV files
2022-03-30 11:21:01,623 - ukbrest - WARNING - No encodings.txt found, assuming utf-8
2022-03-30 11:21:01,750 - ukbrest - INFO - Loading CSV files into database
2022-03-30 11:21:01,946 - ukbrest - INFO - /scratch/tmp/ukb_pheno_1_00.csv -> ukb_pheno_1_00
2022-03-30 11:21:02,069 - ukbrest - INFO - Loading all eids into table all_eids
2022-03-30 11:21:02,089 - ukbrest - INFO - Loading BGEN sample file: /var/lib/genotype/impv2.sample
2022-03-30 11:21:02,104 - ukbrest - INFO - Loading events table
2022-03-30 11:21:02,119 - ukbrest - INFO - Creating table constraints (indexes, primary keys, etc)
2022-03-30 11:21:02,162 - ukbrest - INFO - Vacuuming
2022-03-30 11:21:02,328 - ukbrest - INFO - Loading finished!


[...]
2018-07-20 22:50:34,962 - ukbrest - INFO - Loading finished!
</pre>


Once your main datasets are loaded, you only need to complete two more steps: 1) load the data-field codings and
2) some useful SQL functions. You do this by just running two commands.

To load the data-field codings, run this:
```
$ singularity run --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif --load-codings

```
```
mhan@node01:/scratch/han_lab$ singularity run --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif --load-codings
2022-03-30 12:06:00,962 - ukbrest - INFO - Loading codings from /var/lib/codings
2022-03-30 12:06:01,028 - ukbrest - INFO - Processing coding file: coding_1.tsv
2022-03-30 12:06:01,054 - ukbrest - INFO - Processing coding file: coding_10.tsv
2022-03-30 12:06:01,065 - ukbrest - INFO - Processing coding file: coding_100.tsv
2022-03-30 12:06:01,072 - ukbrest - INFO - Processing coding file: coding_100001.tsv
2022-03-30 12:06:01,079 - ukbrest - INFO - Processing coding file: coding_100002.tsv
2022-03-30 12:06:01,087 - ukbrest - INFO - Processing coding file: coding_100003.tsv
```

This will load most of the data-field codings from the UK Biobank Data Showcase (they are in `.tsv` format in
the [codings folder](https://github.com/hakyimlab/ukbrest/tree/master/misc/codings)). This includes, for instance,
[data coding 19](http://biobank.ctsu.ox.ac.uk/showcase/coding.cgi?id=19), which is used for
[data-field 41202](http://biobank.ctsu.ox.ac.uk/showcase/field.cgi?id=41202)
(Diagnoses - main ICD10).
For your application, however, you could need to download a few more if you have specific data-fields.
This is covered in [the documentation](https://github.com/hakyimlab/ukbrest/wiki/Load-real-UK-Biobank-data).

Finally, run this command to create some useful SQL functions you will likely use in your queries:
```
$ singularity run --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif --load-sql
```
```
mhan@node01:/scratch/han_lab$ singularity run --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif --load-sql
2022-03-30 12:06:35,720 - ukbrest - WARNING - UKBREST_SQL_CHUNKSIZE was not set, no chunksize for SQL queries, what can lead to memory problems.
2022-03-30 12:06:35,878 - ukbrest - INFO - SQL file loaded successfully: /opt/utils/sql/functions.sql
```

## Step 3: Start
Now you only need to start the ukbREST server:

<pre>
$ singularity run  -B /scratch/han_lab/ukbrest_tests/data/example01:/var/lib/genotype --env UKBREST_SQL_CHUNKSIZE="10000" --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif 
</pre>

```
mhan@node01:/scratch/han_lab$ singularity run  -B /scratch/han_lab/ukbrest_tests/data/example01:/var/lib/genotype --env UKBREST_SQL_CHUNKSIZE="10000" --env UKBREST_DB_URI="postgresql://mhan:b1400ec2-15cd-4b71-8e52-3a0ca006ad31@node01:44245/ukb" ukbrest_latest.sif 
[2022-03-30 12:07:36 -0700] [36904] [INFO] Starting gunicorn 19.7.1
[2022-03-30 12:07:36 -0700] [36904] [INFO] Listening at: http://0.0.0.0:5000 (36904)
[2022-03-30 12:07:36 -0700] [36904] [INFO] Using worker: eventlet
[2022-03-30 12:07:36 -0700] [36928] [INFO] Booting worker with pid: 36928
[2022-03-30 12:07:36 -0700] [36929] [INFO] Booting worker with pid: 36929
[2022-03-30 12:07:36 -0700] [36930] [INFO] Booting worker with pid: 36930
[2022-03-30 12:07:36 -0700] [36931] [INFO] Booting worker with pid: 36931
2022-03-30 12:07:37,242 - ukbrest - WARNING - No users file was specified, so HTTP Basic authentication is disabled.
2022-03-30 12:07:37,242 - ukbrest - WARNING - No users file was specified, so HTTP Basic authentication is disabled.
2022-03-30 12:07:37,251 - ukbrest - WARNING - No users file was specified, so HTTP Basic authentication is disabled.
2022-03-30 12:07:37,253 - ukbrest - WARNING - No users file was specified, so HTTP Basic authentication is disabled.
```


Again, remember that you have to keep this server running as well, while you query with ukbREST.

For **security reasons**, note that with these commands both the ukbREST server
and the PostgreSQL are only reachable from your own computer/server. No one from the
network will be able to make any queries other than you from the computer where
ukbREST is running.

Check out [the documentation](https://github.com/hakyimlab/ukbrest/wiki)
to setup ukbREST in a private and secure network and how to add **user authentication**
and **SSL encryption**.

## Step 4: Query
Once the ukbREST is up and running, you can request any data-field using
[different query methods](https://github.com/hakyimlab/ukbrest/wiki/Phenotype-queries).
Column names for data-fields have this format: `c{DATA_FIELD_ID}_{INSTANCE}_{ARRAY}`.


#### Using the command line
You can request a single or multiple data-fields using standard tools like `curl`:

```
$ curl -G -HAccept:text/csv "http://node01:5000/ukbrest/api/v1.0/phenotype" --data-urlencode "columns=c101_0_0 as variable_name"
```
```
mhan@node01:~$ curl -G -HAccept:text/csv "http://node01:5000/ukbrest/api/v1.0/phenotype" --data-urlencode "columns=c101_0_0 as variable_name"
eid,variable_name
1000010,NA
1000021,0.0401
1000030,NA
1000041,0.5632
1000050,0.4852
1000061,0.1192
```
In this case you asked for data field 101 (instance 0 and array 0) and CSV format. You also asked to rename that column in the CSV file by variable_name.


### Genotype queries

In this example you can query for all variants between positions 0 and 1000 in chromosome 1 (all these parameters are highlighted in bold):

<pre>
$ curl -G -HAccept:application/octel-stream "http://node01:5000/ukbrest/api/v1.0/genotype/1/positions/0/1000" > test.bgen
</pre>


Note that in this example you get a `bgen` (binary) file. If you want to read it from your scripts in Python,
for instance, you can use a package like this one: https://github.com/limix/bgen-reader-py


# Documentation

Check out the [wiki pages](https://github.com/hakyimlab/ukbrest/wiki) for more information.
