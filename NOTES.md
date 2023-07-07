Teradata

What is presto

+ Presto is an open source distributed SQL Query Engine
  + Designed from the ground up for interactive analytics against a variety of data sources for all size 
  + Not a database
+ Initially developed by facebook
  + Open sourced in November 2031
  + Distributed under the Apache license
  + Growing community of many contributors and users

Presto extensibility

+ Pluggable backends allow fro Presto users to query data where it lives
+ Presto's storage abstraction makes it simple to provide querying against disparate data sources


Presto performance

+ Presto has its own custom query execution engine designed for supporting SQL
+ Compute processing is performed in memory and in parallel
+ Data is pipelined between query stages and over the network reducing latency overhead of disk I/O where involved
+ Byte code generation
+ It's Not Map Reduce

SQL Support

+ Presto development is guided by the 2011 SQL Standard
+ Simply write SQL queries and Presto handles the difference of querying the various data sources

Presto connectivity

+ Command line interface (CLI)
+ ODBC drivers
  + Windows, Linux, OSX
+ JDBC drivers
  + 4.0 4.1 4.2


Business intelligence

+ Using either the JDBC or ODBC drivers, Presto works with many Business intelligence(BI) tools
+ With Presto's interactivity, it's a good alternative to Hive for Bi tools

Presto limitations

+ Not fault tolerant
  + Must restart query if part of the query fails
  + Aggregations, joins, sort, and window functions limited by memory
  + Still some SQL gaps


Presto distributed architecture
https://prestodb.io/docs/current/overview.html

Presto plugins

+ All data access in Preso is down via plugins
+ Plugins are written in Java
+ Implement interfaces and override methods defined by an SPI
+ Underlying data source does not have to appear relational


Query Presto

> docker run -p 8080:8080 --name presto ahanaio/prestodb-sandbox
> docker exec -it presto  presto-cli
> presto> show catalogs;
> docker exec -it presto  presto-cli  --catalog tpcds --schema sf10
> teradata vm

> show catalogs;
> show schemas in tpch;

TPCH

+ TPC-h is a decision support benchmark widely used in the industry primarity for benchmarking systems
+ The TPC-h connector in Presto generates the data on the fly so it needs not be stored anywhere
+ http://www.tpc.org/tpc_documents_current_versions/pdf/tpc-h_v2.17.1.pdf


Deployment considerations

+ Standalone Cluster
  + ensure network between Presto workers and HDFS nodes
  + No heavy MapReduce jobs can impact your Presto queries
+ Co-located with hadoop cluster
  + Minimize data access latency to hadoop
  + May want to configure with YARN


presto-cli --server localhost:8080

Presto admin

What is Presto admin?

+ Terminal base tool for managing a Presto cluster
+ Key Functionality
  + install, uninstall and upgrade Presto
  + Start, stop and restart Presto on cluster
  + Distribute Presto configuration across cluster

Presto admin architecture

+ Based on Fabric( http://www.fabfile.org/) for executing local and remote shell commands 

Presto admin command examples

> prsto-admin server install
> prsto-admin server restart
> prsto-admin server status
> prsto-admin connector add hive
> prsto-admin configuration deploy 


Presto admin requirements

+ Python 2.6 or 2.7
+ Must exists a user on all nodes with Sudo privileges
+ That user must be able to SSH into all nodes in the cluster

http://prestodb.io/presto-admin/
https://github.com/prestodb/presto-admin

> sudo pip install prestoadmin

sudo presto-admin package install path/to/jdk
sudo presto-admin -i  patho/to/ssh/key server install 0.150
sudo presto-admin -i  patho/to/ssh/key server start
sudo presto-admin -i  patho/to/ssh/key configuration deploy
sudo presto-admin -i  patho/to/ssh/key configuration restart

ambari-presto-service

Presto YARN integration

+ Accomplished by Apache Slider
  + Framework for long running applications to integrate with YARN in Hadoop
  + Manages life cycle
  + Start/Stop/Scale
+ Installed at Command line
+ Installed via Slider Views in Apache Ambari


https://github.com/prestodb/presto-yarn
https://prestodb.io/presto-yarn/index.html
https://slider.incubator.apache.org
http://hortonworks.com/apache/slider/

presto web ui

presto admin

> presto-admin help
> presto-admin server status -d
> presto-admin topology show
> presto-admin configuration show
> presto-admin collect logs -d
> presto-admin collect query_info -d
> presto-admin collect query_system_info -d


system catalog

show tables in system.metadata

select * from system.runtime.nodes
 call system.runtime.kill_query('query_id', "kill");

 Security in Presto

+  Authentication
  + "Who you ar"
+ Authorization
  + "What can you do"

Authentication types

+ Client to Presto
  + None
  + Kerberos
+ Presto to Connector
  + Depends on implementation
    + Username-Password for most
    + Hive Connector - Kerberos

Authorization

+ Presto- can do most things
+ Connectors - Depends on implementation
+ Username-password Authentication
  + Authorized to do whatever the service user for that system is able
  + Currently unable to connect as a different user than what is in the connector properties file

Future Work

+ Other Authentication methods
  + LDAP
  + Additional authentication for connections
+ HTTPS of worker / coordinator communication
+ Authentication to Presto web UI
+ Hadoop integrations - Knox, Ranger, Sentry

Hive connector security

+ Authentication
+ Authorization
+ Impersonation



Hive connector authentication

+ HDFS accessed as the user running the Presto process
  + Set -dHADOOP_USER_NAME in jvm.config to change
+ Connector can authenticate using Eerberos to
  + Hive Metastore
  + HDFS
+ Additional Hive Connector & Kerberos information will be convered in teh Kerberos lesson

Hive connector authorization

+ Hive Connector performs authorization checks
  + Hive properties files must have property hive.security set
    + legacy(default)
    + read-only
    + file
    + sql standard

Legacy Authorization

+ Original implementation in the Hive connector
+ Enforces the following properties if set:
  + hive.allow-drop-table
  + hive.allow-rename-table
  + hive.allow-add-column
  + hive.allow-rename-column

READ-Only authorization

+ reading data or metadata allowed
  + select
+ writing data or metadata not allowed
  + create
  + insert
  + delete

File Authorization

+ JSON-based file describing authorization rules for:
  + Schema(who owns the schema)
  + Tables(what privileges are granted to a user)
  + Session properties(who may set session properites)
+ Regex usage
+ File specified in security.config-file


https://prestodb.io/current/connector/hive-seucrity.html#file-based-authorization


SQL standard hive uathorization

+ Perform operations as long as privilege granted to them per the SQL standard
+ The hive connector performs the authorization checks based on privileges granted in Hive Metastor
+ Using grant/revoke in Presto will alter those privileges in the Hive Metastore

https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+Based+Hive+Authorization


Hive connector impersonation

+ HDFS accessed as the user running the Presto process
  + set -DHADOOP_USER_NAME in jvm.config to change
When configed, Presto can impersonate the end user running the query
  + e.g. using the --user from teh Presto cli


HDFS impersonation

+ Presto will impersonate end user running the query
  + instead of the PResto process owner
+ Does not require Kerberos to be configured

hive.hdfs.authertication.type=None
hive.hdfs.impersonation.enabled=true


https://prestodb.io/docs/current/connector/hive-security.html#end-user-impersonation


metastore impersonation

+ Not supported

hadoop impersonation

+ Hadoop MUST be configured to allow the specific user to impersonate
+ Configured in core-site.xml


https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/Superusers.html#Configurations



Kerberos in Presto


Two Forms

+ Client connecting to Kerberos enabled Presto
+ Presto connecting to Kerberos enabled hadoop
+ Not required to configure both


Kerberos enabled presto

+ Kerberos authentication from CLI, ODBC, or JDBC
+ Presto coordinator must be configured to enable Kerberos authentication
+ Configurations specified in config.properties

Coordinator prerequisites

+ Kerberos KDC must be accessible by Presto coordinator
+ krb5.conf configuration file for Presto
+ Presto coordinator needs a pricipal
+ Presto coordinator needs a keytab
+ Update java Cryptography Extension Policy files to allow for the stronger Kerberos keys
+ Java Keystore needed for HTTPS

Client Configuration

+ Enable authentication(true or false)
+ Presto coordinator service name
+ Presto coordinator address
+ Pricipal to authenticate with coordinator
+ Keytab location
+ krb5.conf path
+ Java Keystore path & password

Kerberos CLI

example

> #!/bin/bash

./presto \
    --server https://presto-coordinator.example.com:7778 \ 
    --enable-authentication \ 
    --krb5-config-path /etc/krb5.conf \
    --krb-principal someuser@example.com \
    --krb5-keytab-path /home/someuser/someuser.keytab \
    --krb-remote-service-name presto \
    --keystor-path /tmp/presto \
    --keystore-passworcd passwordc \
    --catalog <catalog>  \
    --schema <schema>


https://presto.io/docs/current/security/cli.html




Kerberos enabled hadoop

+ Presto hive connector must be configured to work with 
  + Hive Metastore Thrift Service
  + HDFS
+ Configurations specified in Hive connector properties file


HIve Metastore kerberos auth

+ Connector properties file must specify:
  + Authentication type(None or Kerberos)
  + Principal of the Hive Metastore service
  + Principal used by Presto to connect to Hive Metastore
  + Keytab location
+ Principal must have permission s to remove files and directories in the hive/warehouse directory in HDFS

HDFS Kerberos auth

+ Connector properties must specify:
  + Authentication type (None or Kerberos)
  + I mpersonation enabled (true or false)
  + Principal used by presto to connect to HDFS
  + Keytab location


KEYTAB NOTES

+ Must be stored securely
+ Must be on every node in the Presto cluster
+ Must be readable by user running the Presto process


Explan plan

use tpch.sf1;
select regionkey, count(*) from nation group by 1;
explain select regionkey, count(*) from nation group by 1;

read from bottom to up

explain (type distributed) select regionkey, count(*) from nation group by 1;
explain (type distributed) select nation.regionkey, count(*) from nation, region where nation.regionkey = region.regionkey group by 1;
j

Presto query execution


Presto concepts

A plan is created from a query
A plan is made up of 1 or more fragments, Fragments are logical
A fragment is represented by a physical stage
A stage has tasks where tasks are distributed over a network of Presto workers
A task has pipelines which is a template for the execution drivers
A pipeline has execution drivers that operate on splits of data
A driver is an instance of a pipeline and contains operators, The driver drives pages of data through the operators
Operators do the actual data processing in Presto
A split is a part of a table or intermediate data

PResto Memory Management


JVM Heap

+ Defined in jvm.confg
+ -Xmx


Presto Memory Pools

+ System Pool
  + e.g. Memory for buffers
+ User Pool
  + e.g. hash tables for joins and aggregations
+ Reserved Pool
  + Allocated once user pool is exhasusted
  + Used by 1 query at a time


Presto System Pool
  + default 50% of heap space
  + resources.reserved-system-memory
    + not recommended to change

PResto reserved Pool

+ Default (10% of heap space)
  + Equal to query.max-memory-per-node
+ Only used when user pool is exhasusted
+ Designed to avoid deadlocks

Presto User Pool

+ User Pool = Heap Size - System Pool - Reserved Pool
  + Default 50% of heap space

Query-max-memory-per-node

+ Most important configuration for Presto memory management
  + Default is 10% of heap size
  + Defined in config.properties
+ Queries that more than this amount result in an error
+ balance concurrency and queries that require more mrmory

Concurency

+ Recall User Pool = Heap Size - System - Reserved 
  + System pool = 40% of heap size
  + Max query.max-memory-per-node = 60% of heap space
+ If max is set for query.max-memory-per-node, then only the reserved pool is used.this mean only one query can execute at a time resulting in serial execution of queries in Presto
+ If query.max-memory-per-node is set such that the user pool memory > 0, then queries will start in the user pool
+ If at any point the total memory consumed exceeds the user pool, then any query requiring more memory will use the reserved pool
+ Once this query is finished, it frees up the memory for the other queries, thus avoiding deadlock

Presto TuNing

tuning

+ Cluster configuration
+ Query rewriting

Cluster configuration

+ Defaults should work for most workloads
+ Adjust config.properties for specific workload when faced with performance problems


Common Configuration options

+ node-scheduler.network-topology
  + "flat or legacy"
  + "flat" will try to schedule splits on same host as data
+ task.max-worker-threads
  + Numberof threads used by workers when processing splits
  + 1-2 per cores on machine
  + Increasing will increase heap space usage
+ query.max-memory-per-node
  + Total memory on a node to be used for a single query
+ query.max-memory
  + Total memory for entire query across cluster
  + ideally query.max-memory-per-node*#node
  + May be lower if query.max-memory-per-node was set higher to account for data skew
+ distributed-join-enabled
  + Use distributed hash joins instead of broadcast joins
  + Memory considerations


https://prestodb.io/docs/current/admin/tuning.html
https://teradata.github.io/presto/docs/current/admin/tuning.html
https://teradata.github.io/presto/docs/current/admin/properies.html


Query rewriting

+ Presto optmizer is a simple rule-based optimizer
+ Often can rewrite the query for better performance
  Join reordering
  + group-by pushdown
  + etc.

+ TODO 


PResto hadoop file formates

Supported file formats

+ ORC
+ Parquet
+ RCFile
+ SequenceFile
+ Text

Specifiy format

+ Specify in create table[as]
+ Or if already create in hive

create table orders_by_date
with (format= 'ORC')
as 
select orderdate, sum(totalprice) as price
from order
group by orderdate

https://prestodb.io/docs/current/sql/create-table-as.html
)

Presto Queues

Queues configuration

+ Use for basic load balancing
  + Control number of queries submitted
  + Control number of queries running in a queue
+ Defined in JSON file
  + Specify filename in config.properties using config query.queue-config.file
  + consists of queue templates and rules

Queue Templates

+ Queues defined in the JSON file
+ Set limits or number of concurrent and queued queries


"queues": {
    "global": {
        "maxConcurrent": 100,
        "maxQueued": 1000
    }
}


Rules

+ Rules defined in the json file
+ Determine which queries go into which queues

"rules": [
    {
        "queues": [
            "user.${user}",
            "global"
        [

    }
]


Multiple Queues

+ Able to define multiple queues in a Rule
  + Permits to the queues are acquired sequentially
  + A query cannot start execution untill all permits are acquired
  + All permits are held until the query completes execution


unixodbc.org


Web Ui url

localhost:8080




### presto hidden table and columns

select * from tables$partitions
select "$file_size" from tables
select "$last_modified_date" from tables
select "$path" from tables

# Hive Analyze

hive> analyze table hive.ontimeflights compute statistics
hive> analyze table hive.ontimeflights compute statistics for columns

>show stats for hive.ontime.flights;






