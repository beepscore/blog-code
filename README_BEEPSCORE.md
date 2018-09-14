# Purpose
Do Kafka tutorial.

# References

## Kafka 2.0 Documentation
https://kafka.apache.org/documentation/#gettingStarted

## cloudboxlabs git repo
I forked from cloudboxlabs, added branch beepscore
https://github.com/beepscore/blog-code.git

## Building a real time NYC subway tracker with Apache Kafka
https://hackernoon.com/building-a-real-time-nyc-subway-tracker-with-apache-kafka-40d4e09bfe98

### New York City MTA Feed documentation
http://datamine.mta.info/feed-documentation

## Tracking NYC Citi Bike real time utilization using Kafka Streams
https://towardsdatascience.com/tracking-nyc-citi-bike-real-time-utilization-using-kafka-streams-1c0ea9e24e79

## Building a Real-Time Bike-Share Data Pipeline with StreamSets, Kafka and MapD
https://www.jowanza.com/blog/2018/9/8/real-time-station-tracking-ford-gobike-and-mapd

## gbfs General Bikeshare Feed Specification
Documentation for the General Bikeshare Feed Specification, a standardized data feed for bike share system availability.
https://github.com/NABSA/gbfs

## StreamSets
https://streamsets.com/

# Results

## Building a real time NYC subway tracker with Apache Kafka
"MTA’s trip update API uses protobuf in gtfs-realtime feed specification."

### python protobuf classes
These are used to parse mta API protobuf response.
Apparently author already generated pyton protobuf classes like nyct_subway_pb2.py by running:

    python -m grpc_tools.protoc -Iprotos/ --python_out=. --grpc_python_out=. protos/gtfs-realtime.proto

### Kafka
In tutorial, both the Kafka cluster and the application components run on separate EC2 on AWS.
Try running locally instead.

    conda activate beepscore

#### producer

    python producer.py
ModuleNotFoundError: No module named 'confluent_kafka'

In anaconda installed package python-confluent-kafka 0.11.4 (I think this contains module confluent_kafka)

    python producer.py
Traceback (most recent call last):
  File "producer.py", line 41, in <module>
    MTARealTime().run()
  File "producer.py", line 13, in __init__
    with open('.mta_api_key', 'r') as key_in:
FileNotFoundError: [Errno 2] No such file or directory: '.mta_api_key'

##### mta key
https://datamine.mta.info/user/register
I requested a key. Had to provide email, street address, phone number.
Keep api key secret, don't commit it to version control which could accidentally expose it.
Added file .mta_api_key containing api key.
.gitignore ignores filename .mta_api_key

###### 2018-09-14
producer.py produce_trip_updates is requesting to mta, getting response, parsing protobuf
and converting to json.

update_json = MessageToJson(entity.trip_update)
e.g.
{
  "trip": {
    "tripId": "075750_1..S03R",
    "startDate": "20180914",
    "routeId": "1"
  },
  "stopTimeUpdate": [
    {
      "arrival": {
        "time": "1536946180"
      },
      "departure": {
        "time": "1536946180"
      },
      "stopId": "134S"
    },
    {
      "arrival": {
        "time": "1536946270"
      },
      "departure": {
        "time": "1536946270"
      },
      "stopId": "135S"
    },
    {
      "arrival": {
        "time": "1536946330"
      },
      "departure": {
        "time": "1536946330"
      },
      "stopId": "136S"
    },
    {
      "arrival": {
        "time": "1536946420"
      },
      "departure": {
        "time": "1536946420"
      },
      "stopId": "137S"
    },
    {
      "arrival": {
        "time": "1536946510"
      },
      "departure": {
        "time": "1536946510"
      },
      "stopId": "138S"
    },
    {
      "arrival": {
        "time": "1536946600"
      },
      "departure": {
        "time": "1536946600"
      },
      "stopId": "139S"
    },
    {
      "arrival": {
        "time": "1536946750"
      },
      "stopId": "142S"
    }
  ]
}
                
#### consumer

    python consumer.py
  File "consumer.py", line 5, in <module>
    import arrow
ModuleNotFoundError: No module named 'arrow'

In anaconda installed package arrow, not pyarrow.
https://arrow.readthedocs.io/en/latest/

    python consumer.py
%3|1536891241.394|FAIL|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused
%3|1536891241.394|ERROR|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused
%3|1536891241.394|ERROR|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: 1/1 brokers are down

TODO: get key and start producer before starting consumer.

# Appendix bike sample data
https://gbfs.fordgobike.com/gbfs/en/station_status.json
returns json with ~ 300 stations

last_updated	1536882694
ttl	10
data	
stations	
0	
station_id	"74"
num_bikes_available	2
num_ebikes_available	0
num_bikes_disabled	2
num_docks_available	23
num_docks_disabled	0
is_installed	1
is_renting	1
is_returning	1
last_reported	1536882678
eightd_has_available_keys	false
eightd_active_station_services	
0	
id	"6d9eb7b8-a412-407a-b424-5fa8c4ae96ba"
1	
station_id	"3"
num_bikes_available	11
num_ebikes_available	0
num_bikes_disabled	2
num_docks_available	22
num_docks_disabled	0
is_installed	1
is_renting	1
is_returning	1
last_reported	1536882674
eightd_has_available_keys	false

# Appendix install kafka
brew install kafka
==> Installing dependencies for kafka: zookeeper
==> Installing kafka dependency: zookeeper
==> Downloading https://homebrew.bintray.com/bottles/zookeeper-3.4.12.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring zookeeper-3.4.12.high_sierra.bottle.tar.gz
==> Caveats
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> Summary
🍺  /usr/local/Cellar/zookeeper/3.4.12: 242 files, 32.9MB
==> Installing kafka
==> Downloading https://homebrew.bintray.com/bottles/kafka-2.0.0.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kafka-2.0.0.high_sierra.bottle.tar.gz
==> Caveats
To have launchd start kafka now and restart at login:
  brew services start kafka
Or, if you don't want/need a background service you can just run:
  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties & kafka-server-start /usr/local/etc/kafka/server.properties
==> Summary
🍺  /usr/local/Cellar/kafka/2.0.0: 160 files, 46.8MB
==> Caveats
==> zookeeper
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> kafka
To have launchd start kafka now and restart at login:
  brew services start kafka
Or, if you don't want/need a background service you can just run:
  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties & kafka-server-start /usr/local/etc/kafka/server.properties
 
 ## zoo.cfg
 homebrew puts configuration file at
 /usr/local/etc/zookeeper/zoo.cfg
 
 I think kafka connects to zookeeper on port 2181.
 producer and consumer connect to kafka on port 9092.
 https://stackoverflow.com/questions/51338769/kafka-node-only-works-on-zookeeper-port-2181
 
 then run:
 
     zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties
     
 open a second terminal window, run:
 
     kafka-server-start /usr/local/etc/kafka/server.properties
  
 then in pycharm ran producer.py
 
 iterm kafka server partial log:
[2018-09-14 16:11:27,402] INFO [Log partition=test-0, dir=/usr/local/var/lib/kafka-logs] Loading producer state till offset 0 with message format version 2 (kafka.log.Log)
[2018-09-14 16:11:27,408] INFO [Log partition=test-0, dir=/usr/local/var/lib/kafka-logs] Completed load of log with 1 segments, log start offset 0 and log end offset 0 in 35 ms (kafka.log.Log)
[2018-09-14 16:11:27,409] INFO Created log for partition test-0 in /usr/local/var/lib/kafka-logs with properties {compression.type -> producer, message.format.version -> 2.0-IV1, file.delete.delay.ms -> 60000, max.message.bytes -> 1000012, min.compaction.lag.ms -> 0, message.timestamp.type -> CreateTime, message.downconversion.enable -> true, min.insync.replicas -> 1, segment.jitter.ms -> 0, preallocate -> false, min.cleanable.dirty.ratio -> 0.5, index.interval.bytes -> 4096, unclean.leader.election.enable -> false, retention.bytes -> -1, delete.retention.ms -> 86400000, cleanup.policy -> [delete], flush.ms -> 9223372036854775807, segment.ms -> 604800000, segment.bytes -> 1073741824, retention.ms -> 604800000, message.timestamp.difference.max.ms -> 9223372036854775807, segment.index.bytes -> 10485760, flush.messages -> 9223372036854775807}. (kafka.log.LogManager)
[2018-09-14 16:11:27,409] INFO [Partition test-0 broker=0] No checkpointed highwatermark is found for partition test-0 (kafka.cluster.Partition)
[2018-09-14 16:11:27,411] INFO Replica loaded for partition test-0 with initial high watermark 0 (kafka.cluster.Replica)
[2018-09-14 16:11:27,413] INFO [Partition test-0 broker=0] test-0 starts at Leader Epoch 0 from offset 0. Previous Leader Epoch was: -1 (kafka.cluster.Partition)
[2018-09-14 16:11:27,429] INFO [ReplicaAlterLogDirsManager on broker 0] Added fetcher for partitions List() (kafka.server.ReplicaAlterLogDirsManager)
[2018-09-14 16:11:29,317] INFO Updated PartitionLeaderEpoch. New: {epoch:0, offset:0}, Current: {epoch:-1, offset:-1} for Partition: test-0. Cache now contains 0 entries. (kafka.server.epoch.LeaderEpochFileCache)

 then in pycharm ran consumer.py
pycharm log:
/Users/stevebaker/anaconda/envs/beepscore/bin/python /Users/stevebaker/Documents/projects/kafkaProjects/cloudboxlabs/blog-code/streaming/consumer.py
Next S bound 1 train will arrive at station South Ferry in 1 minutes
Next N bound 1 train will arrive at station Van Cortlandt Park - 242 St in 0 minutes
Next S bound 1 train will arrive at station Chambers St in 1 minutes
Next S bound 1 train will arrive at station Cortlandt St in 2 minutes
Next S bound 1 train will arrive at station Rector St in 4 minutes
Next N bound 1 train will arrive at station Marble Hill - 225 St in 0 minutes
Next N bound 1 train will arrive at station 231 St in 2 minutes
Next N bound 1 train will arrive at station 238 St in 3 minutes
Next S bound 1 train will arrive at station Houston St in 0 minutes
Next S bound 1 train will arrive at station Canal St in 1 minutes
Next S bound 1 train will arrive at station Franklin St in 2 minutes
Next N bound 1 train will arrive at station 207 St in 0 minutes
Next N bound 1 train will arrive at station 215 St in 2 minutes
Next N bound 1 train will arrive at station 181 St in 0 minutes
Next N bound 1 train will arrive at station 191 St in 1 minutes
Next N bound 1 train will arrive at station Dyckman St in 3 minutes
Next S bound 1 train will arrive at station 28 St in 1 minutes
Next S bound 1 train will arrive at station 23 St in 2 minutes
Next S bound 1 train will arrive at station 18 St in 3 minutes
Next S bound 1 train will arrive at station 14 St in 4 minutes
Next S bound 1 train will arrive at station Christopher St - Sheridan Sq in 5 minutes
Next N bound 1 train will arrive at station 145 St in 0 minutes
Next N bound 1 train will arrive at station 157 St in 2 minutes
Next N bound 1 train will arrive at station 168 St - Washington Hts in 3 minutes
Next S bound 1 train will arrive at station Times Sq - 42 St in 0 minutes
Next S bound 1 train will arrive at station 34 St - Penn Station in 1 minutes
Next N bound 1 train will arrive at station 125 St in 0 minutes
Next N bound 1 train will arrive at station 137 St - City College in 2 minutes
Next N bound 1 train will arrive at station 116 St - Columbia University in 0 minutes
Next S bound 1 train will arrive at station 59 St - Columbus Circle in 0 minutes
Next S bound 1 train will arrive at station 50 St in 2 minutes
Next N bound 1 train will arrive at station 96 St in 1 minutes
Next N bound 1 train will arrive at station 103 St in 2 minutes
Next N bound 1 train will arrive at station Cathedral Pkwy in 4 minutes
Next S bound 1 train will arrive at station 86 St in 0 minutes
Next S bound 1 train will arrive at station 79 St in 1 minutes
Next S bound 1 train will arrive at station 72 St in 3 minutes
Next S bound 1 train will arrive at station 66 St - Lincoln Center in 4 minutes
Next S bound 1 train will arrive at station 96 St in 0 minutes
Next N bound 1 train will arrive at station 72 St in 0 minutes
Next N bound 1 train will arrive at station 79 St in 2 minutes
Next N bound 1 train will arrive at station 86 St in 3 minutes
Next S bound 1 train will arrive at station 116 St - Columbia University in 2 minutes
Next S bound 1 train will arrive at station Cathedral Pkwy in 3 minutes
Next S bound 1 train will arrive at station 103 St in 4 minutes
Next N bound 1 train will arrive at station 59 St - Columbus Circle in 1 minutes
Next N bound 1 train will arrive at station 66 St - Lincoln Center in 3 minutes
Next S bound 1 train will arrive at station 125 St in 1 minutes
Next N bound 1 train will arrive at station 18 St in 0 minutes
Next N bound 1 train will arrive at station 23 St in 1 minutes
Next N bound 1 train will arrive at station 28 St in 2 minutes
Next N bound 1 train will arrive at station 34 St - Penn Station in 4 minutes
Next N bound 1 train will arrive at station Times Sq - 42 St in 5 minutes
Next N bound 1 train will arrive at station 50 St in 7 minutes
Next S bound 1 train will arrive at station 157 St in 0 minutes
Next S bound 1 train will arrive at station 145 St in 2 minutes
Next S bound 1 train will arrive at station 137 St - City College in 3 minutes
Next S bound 1 train will arrive at station 181 St in 0 minutes
Next S bound 1 train will arrive at station 168 St - Washington Hts in 2 minutes
Next N bound 1 train will arrive at station Franklin St in 0 minutes
Next N bound 1 train will arrive at station Canal St in 1 minutes
Next N bound 1 train will arrive at station Houston St in 2 minutes
Next N bound 1 train will arrive at station Christopher St - Sheridan Sq in 4 minutes
Next N bound 1 train will arrive at station 14 St in 6 minutes
Next S bound 1 train will arrive at station 207 St in 0 minutes
Next S bound 1 train will arrive at station Dyckman St in 1 minutes
Next S bound 1 train will arrive at station 191 St in 3 minutes
Next S bound 1 train will arrive at station 238 St in 1 minutes
Next S bound 1 train will arrive at station 231 St in 2 minutes
Next S bound 1 train will arrive at station Marble Hill - 225 St in 4 minutes
Next S bound 1 train will arrive at station 215 St in 5 minutes
Next N bound 1 train will arrive at station Rector St in 2 minutes
Next N bound 1 train will arrive at station Cortlandt St in 3 minutes
Next N bound 1 train will arrive at station Chambers St in 4 minutes
Next N bound 5X train will arrive at station 149 St - Grand Concourse in 4 minutes
Next N bound 5X train will arrive at station 3 Av - 149 St in 6 minutes
Next N bound 5X train will arrive at station E 180 St in 18 minutes
Next N bound 5X train will arrive at station Morris Park in 22 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 23 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 26 minutes
Next N bound 5X train will arrive at station Baychester Av in 28 minutes
Next N bound 5X train will arrive at station Eastchester - Dyre Av in 31 minutes
Next N bound 2 train will arrive at station Wakefield - 241 St in 1 minutes
Next N bound 2 train will arrive at station 233 St in 1 minutes
Next N bound 2 train will arrive at station Nereid Av in 2 minutes
Next N bound 2 train will arrive at station Bronx Park East in 1 minutes
Next N bound 2 train will arrive at station Pelham Pkwy in 4 minutes
Next N bound 2 train will arrive at station Allerton Av in 6 minutes
Next N bound 2 train will arrive at station Burke Av in 7 minutes
Next N bound 2 train will arrive at station Gun Hill Rd in 9 minutes
Next N bound 2 train will arrive at station 219 St in 11 minutes
Next N bound 2 train will arrive at station 225 St in 12 minutes
Next N bound 2 train will arrive at station West Farms Sq - E Tremont Av in 0 minutes
Next N bound 2 train will arrive at station E 180 St in 2 minutes
Next S bound 2 train will arrive at station Beverly Rd in 1 minutes
Next S bound 2 train will arrive at station Newkirk Av in 3 minutes
Next S bound 2 train will arrive at station Flatbush Av - Brooklyn College in 5 minutes
Next N bound 2 train will arrive at station 174 St in 1 minutes
Next S bound 2 train will arrive at station President St in 2 minutes
Next S bound 2 train will arrive at station Sterling St in 3 minutes
Next S bound 2 train will arrive at station Winthrop St in 5 minutes
Next S bound 2 train will arrive at station Church Av in 6 minutes
Next N bound 2 train will arrive at station Freeman St in 1 minutes
Next S bound 2 train will arrive at station Franklin Av in 1 minutes
Next N bound 2 train will arrive at station Prospect Av in 0 minutes
Next N bound 2 train will arrive at station Intervale Av in 2 minutes
Next N bound 2 train will arrive at station Simpson St in 3 minutes
Next S bound 2 train will arrive at station Atlantic Av - Barclays Ctr in 0 minutes
Next S bound 2 train will arrive at station Bergen St in 1 minutes
Next S bound 2 train will arrive at station Grand Army Plaza in 3 minutes
Next S bound 2 train will arrive at station Eastern Pkwy - Brooklyn Museum in 5 minutes
Next N bound 2 train will arrive at station 3 Av - 149 St in 2 minutes
Next N bound 2 train will arrive at station Jackson Av in 4 minutes
Next S bound 2 train will arrive at station Wall St in 0 minutes
Next S bound 2 train will arrive at station Clark St in 4 minutes
Next S bound 2 train will arrive at station Borough Hall in 6 minutes
Next S bound 2 train will arrive at station Hoyt St in 7 minutes
Next S bound 2 train will arrive at station Nevins St in 8 minutes
Next N bound 2 train will arrive at station 149 St - Grand Concourse in 2 minutes
Next S bound 2 train will arrive at station Park Pl in 0 minutes
Next S bound 2 train will arrive at station Fulton St in 3 minutes
Next N bound 2 train will arrive at station Central Park North (110 St) in 0 minutes
Next N bound 2 train will arrive at station 116 St in 1 minutes
Next N bound 2 train will arrive at station 125 St in 3 minutes
Next N bound 2 train will arrive at station 135 St in 5 minutes
Next N bound 2 train will arrive at station 96 St in 1 minutes
Next S bound 2 train will arrive at station 34 St - Penn Station in 0 minutes
Next S bound 2 train will arrive at station 14 St in 3 minutes
Next S bound 2 train will arrive at station Chambers St in 7 minutes
Next N bound 2 train will arrive at station 72 St in 0 minutes
Next S bound 2 train will arrive at station 96 St in 0 minutes
Next S bound 2 train will arrive at station 72 St in 3 minutes
Next S bound 2 train will arrive at station Times Sq - 42 St in 8 minutes
Next N bound 2 train will arrive at station 34 St - Penn Station in 2 minutes
Next N bound 2 train will arrive at station Times Sq - 42 St in 3 minutes
Next S bound 2 train will arrive at station 125 St in 1 minutes
Next S bound 2 train will arrive at station 116 St in 2 minutes
Next S bound 2 train will arrive at station Central Park North (110 St) in 3 minutes
Next S bound 2 train will arrive at station 135 St in 2 minutes
Next S bound 2 train will arrive at station Freeman St in 1 minutes
Next S bound 2 train will arrive at station Simpson St in 3 minutes
Next S bound 2 train will arrive at station Intervale Av in 4 minutes
Next S bound 2 train will arrive at station Prospect Av in 5 minutes
Next S bound 2 train will arrive at station Jackson Av in 7 minutes
Next S bound 2 train will arrive at station 3 Av - 149 St in 9 minutes
Next S bound 2 train will arrive at station 149 St - Grand Concourse in 12 minutes
Next N bound 2 train will arrive at station Hoyt St in 0 minutes
Next N bound 2 train will arrive at station Borough Hall in 1 minutes
Next N bound 2 train will arrive at station Clark St in 3 minutes
Next N bound 2 train will arrive at station Wall St in 8 minutes
Next N bound 2 train will arrive at station Fulton St in 9 minutes
Next N bound 2 train will arrive at station Park Pl in 11 minutes
Next N bound 2 train will arrive at station Chambers St in 12 minutes
Next N bound 2 train will arrive at station 14 St in 17 minutes
Next S bound 2 train will arrive at station 174 St in 0 minutes
Next N bound 2 train will arrive at station Bergen St in 0 minutes
Next N bound 2 train will arrive at station Atlantic Av - Barclays Ctr in 1 minutes
Next N bound 2 train will arrive at station Nevins St in 3 minutes
Next S bound 2 train will arrive at station Bronx Park East in 2 minutes
Next S bound 2 train will arrive at station E 180 St in 4 minutes
Next S bound 2 train will arrive at station West Farms Sq - E Tremont Av in 6 minutes
Next N bound 2 train will arrive at station Winthrop St in 0 minutes
Next N bound 2 train will arrive at station Sterling St in 1 minutes
Next N bound 2 train will arrive at station President St in 3 minutes
Next N bound 2 train will arrive at station Franklin Av in 5 minutes
Next N bound 2 train will arrive at station Eastern Pkwy - Brooklyn Museum in 7 minutes
Next N bound 2 train will arrive at station Grand Army Plaza in 9 minutes
Next S bound 2 train will arrive at station Gun Hill Rd in 1 minutes
Next S bound 2 train will arrive at station Burke Av in 2 minutes
Next S bound 2 train will arrive at station Allerton Av in 4 minutes
Next S bound 2 train will arrive at station Pelham Pkwy in 6 minutes
Next N bound 2 train will arrive at station Newkirk Av in 0 minutes
Next N bound 2 train will arrive at station Beverly Rd in 2 minutes
Next N bound 2 train will arrive at station Church Av in 3 minutes
Next S bound 2 train will arrive at station Nereid Av in 4 minutes
Next S bound 2 train will arrive at station 233 St in 5 minutes
Next S bound 2 train will arrive at station 225 St in 7 minutes
Next S bound 2 train will arrive at station 219 St in 8 minutes
Next N bound 2 train will arrive at station 14 St in 4 minutes
Next N bound 2 train will arrive at station Eastern Pkwy - Brooklyn Museum in 1 minutes
Next N bound 2 train will arrive at station Grand Army Plaza in 3 minutes
Next N bound 3 train will arrive at station Harlem - 148 St in 1 minutes
Next S bound 3 train will arrive at station Van Siclen Av in 0 minutes
Next S bound 3 train will arrive at station New Lots Av in 1 minutes
Next S bound 3 train will arrive at station Pennsylvania Av in 1 minutes
Next N bound 3 train will arrive at station 125 St in 1 minutes
Next N bound 3 train will arrive at station 135 St in 3 minutes
Next N bound 3 train will arrive at station 145 St in 5 minutes
Next S bound 3 train will arrive at station Kingston Av in 1 minutes
Next S bound 3 train will arrive at station Crown Hts - Utica Av in 3 minutes
Next S bound 3 train will arrive at station Sutter Av - Rutland Rd in 6 minutes
Next S bound 3 train will arrive at station Saratoga Av in 8 minutes
Next S bound 3 train will arrive at station Rockaway Av in 10 minutes
Next S bound 3 train will arrive at station Junius St in 11 minutes
Next N bound 3 train will arrive at station Central Park North (110 St) in 2 minutes
Next N bound 3 train will arrive at station 116 St in 3 minutes
Next S bound 3 train will arrive at station Grand Army Plaza in 0 minutes
Next S bound 3 train will arrive at station Eastern Pkwy - Brooklyn Museum in 2 minutes
Next S bound 3 train will arrive at station Franklin Av in 3 minutes
Next S bound 3 train will arrive at station Nostrand Av in 5 minutes
Next N bound 3 train will arrive at station Times Sq - 42 St in 0 minutes
Next N bound 3 train will arrive at station 72 St in 4 minutes
Next N bound 3 train will arrive at station 96 St in 7 minutes
Next S bound 3 train will arrive at station Nevins St in 0 minutes
Next S bound 3 train will arrive at station Atlantic Av - Barclays Ctr in 1 minutes
Next S bound 3 train will arrive at station Bergen St in 2 minutes
Next N bound 3 train will arrive at station Fulton St in 0 minutes
Next N bound 3 train will arrive at station Park Pl in 2 minutes
Next N bound 3 train will arrive at station Chambers St in 4 minutes
Next N bound 3 train will arrive at station 14 St in 9 minutes
Next N bound 3 train will arrive at station 34 St - Penn Station in 12 minutes
Next S bound 3 train will arrive at station Clark St in 0 minutes
Next S bound 3 train will arrive at station Borough Hall in 2 minutes
Next S bound 3 train will arrive at station Hoyt St in 3 minutes
Next N bound 3 train will arrive at station Clark St in 1 minutes
Next N bound 3 train will arrive at station Wall St in 6 minutes
Next S bound 3 train will arrive at station Chambers St in 3 minutes
Next S bound 3 train will arrive at station Park Pl in 5 minutes
Next S bound 3 train will arrive at station Fulton St in 8 minutes
Next S bound 3 train will arrive at station Wall St in 10 minutes
Next N bound 3 train will arrive at station Bergen St in 1 minutes
Next N bound 3 train will arrive at station Atlantic Av - Barclays Ctr in 3 minutes
Next N bound 3 train will arrive at station Nevins St in 4 minutes
Next N bound 3 train will arrive at station Hoyt St in 6 minutes
Next N bound 3 train will arrive at station Borough Hall in 7 minutes
Next S bound 3 train will arrive at station Times Sq - 42 St in 2 minutes
Next S bound 3 train will arrive at station 34 St - Penn Station in 3 minutes
Next S bound 3 train will arrive at station 14 St in 6 minutes
Next N bound 3 train will arrive at station Franklin Av in 1 minutes
Next N bound 3 train will arrive at station Eastern Pkwy - Brooklyn Museum in 3 minutes
Next N bound 3 train will arrive at station Grand Army Plaza in 5 minutes
Next S bound 3 train will arrive at station 96 St in 2 minutes
Next S bound 3 train will arrive at station 72 St in 5 minutes
Next N bound 3 train will arrive at station Sutter Av - Rutland Rd in 0 minutes
Next N bound 3 train will arrive at station Crown Hts - Utica Av in 3 minutes
Next N bound 3 train will arrive at station Kingston Av in 5 minutes
Next N bound 3 train will arrive at station Nostrand Av in 7 minutes
Next S bound 3 train will arrive at station 135 St in 1 minutes
Next S bound 3 train will arrive at station 125 St in 3 minutes
Next S bound 3 train will arrive at station 116 St in 5 minutes
Next S bound 3 train will arrive at station Central Park North (110 St) in 6 minutes
Next N bound 3 train will arrive at station Van Siclen Av in 2 minutes
Next N bound 3 train will arrive at station Pennsylvania Av in 3 minutes
Next N bound 3 train will arrive at station Junius St in 4 minutes
Next N bound 3 train will arrive at station Rockaway Av in 6 minutes
Next N bound 3 train will arrive at station Saratoga Av in 7 minutes
Next S bound 3 train will arrive at station 145 St in 7 minutes
Next N bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next N bound 4 train will arrive at station Bedford Park Blvd - Lehman College in 3 minutes
Next N bound 4 train will arrive at station Mosholu Pkwy in 5 minutes
Next N bound 4 train will arrive at station Woodlawn in 7 minutes
Next N bound 4 train will arrive at station Fordham Rd in 0 minutes
Next N bound 4 train will arrive at station 176 St in 0 minutes
Next N bound 4 train will arrive at station Burnside Av in 1 minutes
Next N bound 4 train will arrive at station 183 St in 3 minutes
Next N bound 4 train will arrive at station 149 St - Grand Concourse in 2 minutes
Next N bound 4 train will arrive at station 161 St - Yankee Stadium in 5 minutes
Next N bound 4 train will arrive at station 167 St in 7 minutes
Next N bound 4 train will arrive at station 170 St in 8 minutes
Next N bound 4 train will arrive at station Mt Eden Av in 9 minutes
Next N bound 4 train will arrive at station 138 St - Grand Concourse in 1 minutes
Next S bound 4 train will arrive at station Franklin Av in 1 minutes
Next S bound 4 train will arrive at station Crown Hts - Utica Av in 5 minutes
Next N bound 4 train will arrive at station 125 St in 5 minutes
Next S bound 4 train will arrive at station Borough Hall in 2 minutes
Next S bound 4 train will arrive at station Nevins St in 4 minutes
Next S bound 4 train will arrive at station Atlantic Av - Barclays Ctr in 6 minutes
Next N bound 4 train will arrive at station 86 St in 0 minutes
Next N bound 4 train will arrive at station 161 St - Yankee Stadium in 1 minutes
Next N bound 4 train will arrive at station 167 St in 2 minutes
Next N bound 4 train will arrive at station 170 St in 4 minutes
Next N bound 4 train will arrive at station Mt Eden Av in 5 minutes
Next S bound 4 train will arrive at station Brooklyn Bridge - City Hall in 1 minutes
Next S bound 4 train will arrive at station Fulton St in 3 minutes
Next S bound 4 train will arrive at station Wall St in 4 minutes
Next S bound 4 train will arrive at station Bowling Green in 6 minutes
Next S bound 4 train will arrive at station 14 St - Union Sq in 1 minutes
Next N bound 4 train will arrive at station Grand Central - 42 St in 2 minutes
Next N bound 4 train will arrive at station 59 St in 4 minutes
Next S bound 4 train will arrive at station Grand Central - 42 St in 1 minutes
Next S bound 4 train will arrive at station 86 St in 1 minutes
Next S bound 4 train will arrive at station 59 St in 5 minutes
Next N bound 4 train will arrive at station 14 St - Union Sq in 2 minutes
Next S bound 4 train will arrive at station 138 St - Grand Concourse in 0 minutes
Next S bound 4 train will arrive at station 125 St in 2 minutes
Next N bound 4 train will arrive at station Bowling Green in 1 minutes
Next N bound 4 train will arrive at station Wall St in 3 minutes
Next N bound 4 train will arrive at station Fulton St in 4 minutes
Next N bound 4 train will arrive at station Brooklyn Bridge - City Hall in 6 minutes
Next S bound 4 train will arrive at station 161 St - Yankee Stadium in 0 minutes
Next S bound 4 train will arrive at station 149 St - Grand Concourse in 3 minutes
Next S bound 4 train will arrive at station Burnside Av in 1 minutes
Next S bound 4 train will arrive at station 176 St in 2 minutes
Next S bound 4 train will arrive at station Mt Eden Av in 4 minutes
Next S bound 4 train will arrive at station 170 St in 5 minutes
Next S bound 4 train will arrive at station 167 St in 6 minutes
Next N bound 4 train will arrive at station Nevins St in 1 minutes
Next N bound 4 train will arrive at station Borough Hall in 3 minutes
Next S bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next S bound 4 train will arrive at station Fordham Rd in 2 minutes
Next S bound 4 train will arrive at station 183 St in 3 minutes
Next S bound 4 train will arrive at station Mosholu Pkwy in 2 minutes
Next S bound 4 train will arrive at station Bedford Park Blvd - Lehman College in 4 minutes
Next N bound 4 train will arrive at station Franklin Av in 6 minutes
Next N bound 4 train will arrive at station Atlantic Av - Barclays Ctr in 11 minutes
Next N bound 5X train will arrive at station Eastchester - Dyre Av in 1 minutes
Next N bound 5 train will arrive at station Allerton Av in 1 minutes
Next N bound 5 train will arrive at station Burke Av in 2 minutes
Next N bound 5 train will arrive at station Gun Hill Rd in 4 minutes
Next N bound 5 train will arrive at station 219 St in 6 minutes
Next N bound 5 train will arrive at station 225 St in 7 minutes
Next N bound 5 train will arrive at station 233 St in 9 minutes
Next N bound 5 train will arrive at station Nereid Av in 10 minutes
Next N bound 5X train will arrive at station E 180 St in 7 minutes
Next N bound 5X train will arrive at station Bronx Park East in 9 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 11 minutes
Next N bound 5X train will arrive at station Allerton Av in 13 minutes
Next N bound 5X train will arrive at station Burke Av in 15 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 17 minutes
Next N bound 5X train will arrive at station 219 St in 18 minutes
Next N bound 5X train will arrive at station 225 St in 20 minutes
Next N bound 5X train will arrive at station 233 St in 21 minutes
Next N bound 5X train will arrive at station Nereid Av in 23 minutes
Next S bound 5 train will arrive at station Church Av in 0 minutes
Next S bound 5 train will arrive at station Beverly Rd in 4 minutes
Next S bound 5 train will arrive at station Newkirk Av in 5 minutes
Next S bound 5 train will arrive at station Flatbush Av - Brooklyn College in 8 minutes
Next N bound 5X train will arrive at station 3 Av - 149 St in 0 minutes
Next N bound 5X train will arrive at station Morris Park in 15 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 17 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 19 minutes
Next N bound 5X train will arrive at station Baychester Av in 22 minutes
Next N bound 5X train will arrive at station 125 St in 1 minutes
Next N bound 5X train will arrive at station 138 St - Grand Concourse in 4 minutes
Next S bound 5 train will arrive at station President St in 2 minutes
Next S bound 5 train will arrive at station Sterling St in 4 minutes
Next S bound 5 train will arrive at station Winthrop St in 5 minutes
Next S bound 5 train will arrive at station Fulton St in 0 minutes
Next S bound 5 train will arrive at station Wall St in 1 minutes
Next S bound 5 train will arrive at station Bowling Green in 3 minutes
Next S bound 5 train will arrive at station Borough Hall in 9 minutes
Next S bound 5 train will arrive at station Nevins St in 11 minutes
Next S bound 5 train will arrive at station Atlantic Av - Barclays Ctr in 13 minutes
Next S bound 5 train will arrive at station Franklin Av in 17 minutes
Next N bound 5 train will arrive at station E 180 St in 1 minutes
Next N bound 5 train will arrive at station Morris Park in 5 minutes
Next N bound 5 train will arrive at station Pelham Pkwy in 6 minutes
Next N bound 5 train will arrive at station Gun Hill Rd in 9 minutes
Next N bound 5 train will arrive at station Baychester Av in 11 minutes
Next N bound 5 train will arrive at station Eastchester - Dyre Av in 14 minutes
Next S bound 5 train will arrive at station 14 St - Union Sq in 0 minutes
Next S bound 5 train will arrive at station Brooklyn Bridge - City Hall in 5 minutes
Next N bound 5X train will arrive at station 86 St in 2 minutes
Next N bound 5X train will arrive at station 59 St in 1 minutes
Next S bound 5 train will arrive at station 59 St in 0 minutes
Next S bound 5 train will arrive at station Grand Central - 42 St in 3 minutes
Next N bound 5X train will arrive at station 14 St - Union Sq in 0 minutes
Next N bound 5X train will arrive at station Grand Central - 42 St in 4 minutes
Next S bound 5 train will arrive at station 86 St in 3 minutes
Next N bound 5X train will arrive at station Bowling Green in 0 minutes
Next N bound 5X train will arrive at station Wall St in 1 minutes
Next N bound 5X train will arrive at station Fulton St in 2 minutes
Next N bound 5X train will arrive at station Brooklyn Bridge - City Hall in 4 minutes
Next S bound 5 train will arrive at station Simpson St in 0 minutes
Next S bound 5 train will arrive at station Intervale Av in 1 minutes
Next S bound 5 train will arrive at station Prospect Av in 2 minutes
Next S bound 5 train will arrive at station Jackson Av in 4 minutes
Next S bound 5 train will arrive at station 3 Av - 149 St in 6 minutes
Next S bound 5 train will arrive at station 149 St - Grand Concourse in 9 minutes
Next S bound 5 train will arrive at station 138 St - Grand Concourse in 12 minutes
Next S bound 5 train will arrive at station 125 St in 15 minutes
Next N bound 5 train will arrive at station Atlantic Av - Barclays Ctr in 2 minutes
Next N bound 5 train will arrive at station Nevins St in 4 minutes
Next N bound 5 train will arrive at station Borough Hall in 6 minutes
Next N bound 5 train will arrive at station Bowling Green in 11 minutes
Next N bound 5 train will arrive at station Wall St in 13 minutes
Next N bound 5 train will arrive at station Fulton St in 14 minutes
Next N bound 5 train will arrive at station Brooklyn Bridge - City Hall in 16 minutes
Next N bound 5 train will arrive at station 14 St - Union Sq in 20 minutes
Next N bound 5 train will arrive at station Grand Central - 42 St in 24 minutes
Next N bound 5 train will arrive at station 59 St in 27 minutes
Next N bound 5 train will arrive at station 86 St in 30 minutes
Next N bound 5 train will arrive at station 125 St in 35 minutes
Next N bound 5 train will arrive at station 138 St - Grand Concourse in 38 minutes
Next N bound 5 train will arrive at station 149 St - Grand Concourse in 42 minutes
Next N bound 5 train will arrive at station 3 Av - 149 St in 44 minutes
Next N bound 5 train will arrive at station Jackson Av in 47 minutes
Next N bound 5 train will arrive at station Prospect Av in 48 minutes
Next N bound 5 train will arrive at station Intervale Av in 50 minutes
Next N bound 5 train will arrive at station Simpson St in 51 minutes
Next N bound 5 train will arrive at station Freeman St in 53 minutes
Next N bound 5 train will arrive at station 174 St in 55 minutes
Next N bound 5 train will arrive at station West Farms Sq - E Tremont Av in 57 minutes
Next N bound 5 train will arrive at station President St in 0 minutes
Next N bound 5 train will arrive at station Franklin Av in 3 minutes
Next S bound 5 train will arrive at station Pelham Pkwy in 0 minutes
Next S bound 5 train will arrive at station Morris Park in 1 minutes
Next S bound 5 train will arrive at station E 180 St in 5 minutes
Next S bound 5 train will arrive at station West Farms Sq - E Tremont Av in 7 minutes
Next S bound 5 train will arrive at station 174 St in 9 minutes
Next S bound 5 train will arrive at station Freeman St in 11 minutes
Next S bound 5 train will arrive at station Morris Park in 0 minutes
Next S bound 5 train will arrive at station E 180 St in 4 minutes
Next S bound 5 train will arrive at station West Farms Sq - E Tremont Av in 6 minutes
Next S bound 5 train will arrive at station 174 St in 8 minutes
Next S bound 5 train will arrive at station Freeman St in 10 minutes
Next S bound 5 train will arrive at station Baychester Av in 3 minutes
Next S bound 5 train will arrive at station Gun Hill Rd in 5 minutes
Next S bound 5 train will arrive at station Crown Hts - Utica Av in 17 minutes
Next N bound 5 train will arrive at station Newkirk Av in 4 minutes
Next N bound 5 train will arrive at station Beverly Rd in 6 minutes
Next N bound 5 train will arrive at station Church Av in 7 minutes
Next N bound 5 train will arrive at station Winthrop St in 9 minutes
Next N bound 5 train will arrive at station Sterling St in 10 minutes
Next N bound 5 train will arrive at station Gun Hill Rd in 1 minutes
Next N bound 5 train will arrive at station Baychester Av in 4 minutes
Next N bound 5 train will arrive at station Eastchester - Dyre Av in 6 minutes
Next N bound 5 train will arrive at station 125 St in 5 minutes
Next N bound 5 train will arrive at station 138 St - Grand Concourse in 8 minutes
Next N bound 5 train will arrive at station 149 St - Grand Concourse in 11 minutes
Next N bound 5 train will arrive at station 3 Av - 149 St in 13 minutes
Next S bound 6 train will arrive at station Canal St in 0 minutes
Next S bound 6 train will arrive at station Brooklyn Bridge - City Hall in 2 minutes
Next N bound 6 train will arrive at station St Lawrence Av in 0 minutes
Next N bound 6 train will arrive at station Parkchester in 1 minutes
Next N bound 6X train will arrive at station Castle Hill Av in 5 minutes
Next N bound 6X train will arrive at station Zerega Av in 6 minutes
Next N bound 6X train will arrive at station Westchester Sq - E Tremont Av in 7 minutes
Next N bound 6X train will arrive at station Middletown Rd in 8 minutes
Next N bound 6X train will arrive at station Buhre Av in 10 minutes
Next N bound 6X train will arrive at station Pelham Bay Park in 12 minutes
Next N bound 6 train will arrive at station Whitlock Av in 0 minutes
Next N bound 6 train will arrive at station Elder Av in 2 minutes
Next N bound 6 train will arrive at station Morrison Av- Sound View in 3 minutes
Next N bound 6X train will arrive at station Parkchester in 4 minutes
Next N bound 6 train will arrive at station Hunts Point Av in 0 minutes
Next S bound 6 train will arrive at station Grand Central - 42 St in 1 minutes
Next S bound 6 train will arrive at station 33 St in 3 minutes
Next S bound 6 train will arrive at station 28 St in 4 minutes
Next S bound 6 train will arrive at station 23 St in 5 minutes
Next S bound 6 train will arrive at station 14 St - Union Sq in 7 minutes
Next S bound 6 train will arrive at station Astor Pl in 8 minutes
Next S bound 6 train will arrive at station Bleecker St in 10 minutes
Next S bound 6 train will arrive at station Spring St in 11 minutes
Next N bound 6X train will arrive at station Hunts Point Av in 0 minutes
Next S bound 6 train will arrive at station 59 St in 0 minutes
Next S bound 6 train will arrive at station 51 St in 1 minutes
Next S bound 6 train will arrive at station 23 St in 0 minutes
Next S bound 6 train will arrive at station 14 St - Union Sq in 2 minutes
Next S bound 6 train will arrive at station Astor Pl in 4 minutes
Next S bound 6 train will arrive at station Bleecker St in 5 minutes
Next S bound 6 train will arrive at station Spring St in 6 minutes
Next S bound 6 train will arrive at station 86 St in 0 minutes
Next S bound 6 train will arrive at station 77 St in 2 minutes
Next S bound 6 train will arrive at station 68 St - Hunter College in 4 minutes
Next N bound 6X train will arrive at station 116 St in 0 minutes
Next N bound 6X train will arrive at station 125 St in 2 minutes
Next N bound 6X train will arrive at station 3 Av - 138 St in 5 minutes
Next S bound 6 train will arrive at station 77 St in 0 minutes
Next S bound 6 train will arrive at station 68 St - Hunter College in 2 minutes
Next S bound 6 train will arrive at station 125 St in 1 minutes
Next S bound 6 train will arrive at station 116 St in 3 minutes
Next S bound 6 train will arrive at station 110 St in 4 minutes
Next S bound 6 train will arrive at station 103 St in 5 minutes
Next S bound 6 train will arrive at station 96 St in 7 minutes
Next N bound 6X train will arrive at station 77 St in 0 minutes
Next N bound 6X train will arrive at station 86 St in 2 minutes
Next N bound 6X train will arrive at station 96 St in 5 minutes
Next N bound 6X train will arrive at station 103 St in 7 minutes
Next N bound 6X train will arrive at station 110 St in 8 minutes
Next S bound 6 train will arrive at station 116 St in 0 minutes
Next S bound 6 train will arrive at station 110 St in 2 minutes
Next S bound 6 train will arrive at station 103 St in 3 minutes
Next S bound 6 train will arrive at station 96 St in 4 minutes
Next N bound 6 train will arrive at station 68 St - Hunter College in 1 minutes
Next N bound 6 train will arrive at station 77 St in 2 minutes
Next N bound 6 train will arrive at station 86 St in 4 minutes
Next N bound 6 train will arrive at station 96 St in 7 minutes
Next N bound 6 train will arrive at station 103 St in 8 minutes
Next N bound 6 train will arrive at station 110 St in 10 minutes
Next N bound 6 train will arrive at station 116 St in 11 minutes
Next N bound 6 train will arrive at station 125 St in 13 minutes
Next N bound 6 train will arrive at station 3 Av - 138 St in 16 minutes
Next N bound 6 train will arrive at station Brook Av in 17 minutes
Next N bound 6 train will arrive at station Cypress Av in 18 minutes
Next N bound 6 train will arrive at station E 143 St - St Mary's St in 20 minutes
Next N bound 6 train will arrive at station E 149 St in 21 minutes
Next N bound 6 train will arrive at station Longwood Av in 23 minutes
Next N bound 6 train will arrive at station Castle Hill Av in 35 minutes
Next N bound 6 train will arrive at station Zerega Av in 36 minutes
Next N bound 6 train will arrive at station Westchester Sq - E Tremont Av in 37 minutes
Next N bound 6 train will arrive at station Middletown Rd in 38 minutes
Next N bound 6 train will arrive at station Buhre Av in 40 minutes
Next N bound 6 train will arrive at station Pelham Bay Park in 42 minutes
Next S bound 6 train will arrive at station Longwood Av in 1 minutes
Next S bound 6 train will arrive at station E 149 St in 3 minutes
Next S bound 6 train will arrive at station E 143 St - St Mary's St in 4 minutes
Next S bound 6 train will arrive at station Cypress Av in 6 minutes
Next S bound 6 train will arrive at station Brook Av in 7 minutes
Next S bound 6 train will arrive at station 3 Av - 138 St in 8 minutes
Next N bound 6X train will arrive at station 51 St in 0 minutes
Next N bound 6X train will arrive at station 59 St in 2 minutes
Next N bound 6X train will arrive at station 68 St - Hunter College in 4 minutes
Next S bound 6 train will arrive at station E 143 St - St Mary's St in 0 minutes
Next S bound 6 train will arrive at station Cypress Av in 2 minutes
Next S bound 6 train will arrive at station Brook Av in 3 minutes
Next S bound 6 train will arrive at station 3 Av - 138 St in 5 minutes
Next N bound 6 train will arrive at station 28 St in 0 minutes
Next N bound 6 train will arrive at station 33 St in 1 minutes
Next N bound 6 train will arrive at station Grand Central - 42 St in 3 minutes
Next N bound 6 train will arrive at station 51 St in 5 minutes
Next N bound 6 train will arrive at station 59 St in 6 minutes
Next N bound 6 train will arrive at station 33 St in 0 minutes
Next N bound 6 train will arrive at station Grand Central - 42 St in 2 minutes
Next N bound 6 train will arrive at station 51 St in 4 minutes
Next N bound 6 train will arrive at station 59 St in 5 minutes
Next S bound 6 train will arrive at station Parkchester in 0 minutes
Next S bound 6 train will arrive at station St Lawrence Av in 3 minutes
Next S bound 6 train will arrive at station Morrison Av- Sound View in 5 minutes
Next S bound 6 train will arrive at station Elder Av in 6 minutes
Next S bound 6 train will arrive at station Whitlock Av in 7 minutes
Next S bound 6 train will arrive at station Hunts Point Av in 9 minutes
Next N bound 6X train will arrive at station 14 St - Union Sq in 1 minutes
Next N bound 6X train will arrive at station 23 St in 2 minutes
Next N bound 6X train will arrive at station 28 St in 4 minutes
Next N bound 6X train will arrive at station 33 St in 5 minutes
Next N bound 6X train will arrive at station Grand Central - 42 St in 7 minutes
Next N bound 6 train will arrive at station 23 St in 1 minutes
Next S bound 6 train will arrive at station Middletown Rd in 0 minutes
Next S bound 6 train will arrive at station Westchester Sq - E Tremont Av in 1 minutes
Next S bound 6 train will arrive at station Zerega Av in 2 minutes
Next S bound 6 train will arrive at station Castle Hill Av in 3 minutes
Next N bound 6 train will arrive at station Spring St in 0 minutes
Next N bound 6 train will arrive at station Bleecker St in 2 minutes
Next N bound 6 train will arrive at station Astor Pl in 3 minutes
Next N bound 6 train will arrive at station 14 St - Union Sq in 5 minutes
Next S bound 6 train will arrive at station Morrison Av- Sound View in 0 minutes
Next S bound 6 train will arrive at station Elder Av in 1 minutes
Next S bound 6 train will arrive at station Whitlock Av in 3 minutes
Next S bound 6 train will arrive at station Hunts Point Av in 5 minutes
Next N bound 6X train will arrive at station Spring St in 1 minutes
Next N bound 6X train will arrive at station Bleecker St in 2 minutes
Next N bound 6X train will arrive at station Astor Pl in 4 minutes
Next N bound 6 train will arrive at station Canal St in 3 minutes
Next N bound 6X train will arrive at station Canal St in 7 minutes
Next S bound 6 train will arrive at station Buhre Av in 8 minutes
Next N bound GS train will arrive at station Times Sq - 42 St in 2 minutes
Next S bound GS train will arrive at station Grand Central - 42 St in 2 minutes
Next S bound 1 train will arrive at station Franklin St in 0 minutes
Next S bound 1 train will arrive at station Houston St in 0 minutes
Next S bound 1 train will arrive at station Canal St in 1 minutes
Next N bound 1 train will arrive at station 181 St in 0 minutes
Next N bound 1 train will arrive at station 191 St in 1 minutes
Next N bound 1 train will arrive at station Dyckman St in 3 minutes
Next S bound 1 train will arrive at station 34 St - Penn Station in 0 minutes
Next N bound 1 train will arrive at station 145 St in 0 minutes
Next N bound 1 train will arrive at station 157 St in 2 minutes
Next N bound 1 train will arrive at station 168 St - Washington Hts in 3 minutes
Next N bound 1 train will arrive at station 72 St in 0 minutes
Next N bound 1 train will arrive at station 79 St in 1 minutes
Next N bound 1 train will arrive at station 86 St in 3 minutes
Next S bound 1 train will arrive at station 125 St in 0 minutes
Next N bound 1 train will arrive at station 50 St in 0 minutes
Next N bound 1 train will arrive at station 23 St in 0 minutes
Next N bound 1 train will arrive at station 28 St in 1 minutes
Next N bound 1 train will arrive at station 34 St - Penn Station in 3 minutes
Next N bound 1 train will arrive at station Times Sq - 42 St in 4 minutes
Next S bound 1 train will arrive at station 157 St in 0 minutes
Next S bound 1 train will arrive at station 145 St in 2 minutes
Next S bound 1 train will arrive at station 137 St - City College in 3 minutes
Next N bound 2 train will arrive at station Wakefield - 241 St in 0 minutes
Next N bound 2 train will arrive at station E 180 St in 0 minutes
Next S bound 2 train will arrive at station Beverly Rd in 1 minutes
Next S bound 2 train will arrive at station Newkirk Av in 3 minutes
Next S bound 2 train will arrive at station Flatbush Av - Brooklyn College in 5 minutes
Next N bound 2 train will arrive at station 174 St in 1 minutes
Next N bound 2 train will arrive at station Prospect Av in 0 minutes
Next N bound 2 train will arrive at station Intervale Av in 2 minutes
Next N bound 2 train will arrive at station Simpson St in 3 minutes
Next S bound 2 train will arrive at station Clark St in 3 minutes
Next S bound 2 train will arrive at station Borough Hall in 5 minutes
Next S bound 2 train will arrive at station Hoyt St in 6 minutes
Next S bound 2 train will arrive at station Nevins St in 7 minutes
Next S bound 2 train will arrive at station Gun Hill Rd in 1 minutes
Next S bound 2 train will arrive at station Burke Av in 2 minutes
Next S bound 2 train will arrive at station Allerton Av in 4 minutes
Next S bound 2 train will arrive at station Pelham Pkwy in 6 minutes
Next N bound 2 train will arrive at station Eastern Pkwy - Brooklyn Museum in 1 minutes
Next N bound 2 train will arrive at station Grand Army Plaza in 3 minutes
Next N bound 3 train will arrive at station Grand Army Plaza in 0 minutes
Next S bound 3 train will arrive at station Times Sq - 42 St in 2 minutes
Next S bound 3 train will arrive at station 34 St - Penn Station in 3 minutes
Next S bound 3 train will arrive at station 14 St in 6 minutes
Next N bound 3 train will arrive at station Nostrand Av in 0 minutes
Next S bound 3 train will arrive at station 96 St in 2 minutes
Next S bound 3 train will arrive at station 72 St in 4 minutes
Next N bound 4 train will arrive at station Mt Eden Av in 0 minutes
Next N bound 4 train will arrive at station 149 St - Grand Concourse in 2 minutes
Next S bound 4 train will arrive at station Franklin Av in 1 minutes
Next S bound 4 train will arrive at station Crown Hts - Utica Av in 4 minutes
Next N bound 4 train will arrive at station 125 St in 3 minutes
Next S bound 4 train will arrive at station Grand Central - 42 St in 0 minutes
Next N bound 4 train will arrive at station 14 St - Union Sq in 2 minutes
Next N bound 5X train will arrive at station Morris Park in 14 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 16 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 18 minutes
Next N bound 5X train will arrive at station Baychester Av in 21 minutes
Next N bound 5 train will arrive at station President St in 0 minutes
Next N bound 5 train will arrive at station Franklin Av in 2 minutes
Next N bound 5 train will arrive at station Gun Hill Rd in 1 minutes
Next N bound 5 train will arrive at station Baychester Av in 3 minutes
Next N bound 5 train will arrive at station Eastchester - Dyre Av in 6 minutes
Next S bound 6 train will arrive at station 77 St in 0 minutes
Next S bound 6 train will arrive at station 68 St - Hunter College in 2 minutes
Next N bound 6X train will arrive at station 77 St in 0 minutes
Next N bound 6X train will arrive at station 86 St in 2 minutes
Next N bound 6X train will arrive at station 96 St in 5 minutes
Next N bound 6X train will arrive at station 103 St in 6 minutes
Next N bound 6X train will arrive at station 110 St in 8 minutes
Next N bound 6 train will arrive at station 59 St in 0 minutes
Next S bound 6 train will arrive at station Hunts Point Av in 0 minutes
Next N bound 6X train will arrive at station 51 St in 0 minutes
Next N bound 6X train will arrive at station 59 St in 2 minutes
Next N bound 6X train will arrive at station 68 St - Hunter College in 4 minutes
Next S bound 1 train will arrive at station South Ferry in 1 minutes
Next N bound 1 train will arrive at station 125 St in 0 minutes
Next N bound 1 train will arrive at station 137 St - City College in 2 minutes
Next N bound 1 train will arrive at station 96 St in 1 minutes
Next N bound 1 train will arrive at station 103 St in 2 minutes
Next N bound 1 train will arrive at station Cathedral Pkwy in 4 minutes
Next N bound 2 train will arrive at station 233 St in 0 minutes
Next N bound 2 train will arrive at station Nereid Av in 2 minutes
Next S bound 2 train will arrive at station President St in 1 minutes
Next S bound 2 train will arrive at station Sterling St in 3 minutes
Next S bound 2 train will arrive at station Winthrop St in 4 minutes
Next S bound 2 train will arrive at station Church Av in 6 minutes
Next N bound 2 train will arrive at station 96 St in 0 minutes
Next S bound 2 train will arrive at station 14 St in 1 minutes
Next S bound 2 train will arrive at station Chambers St in 5 minutes
Next N bound 2 train will arrive at station Hoyt St in 0 minutes
Next N bound 2 train will arrive at station Borough Hall in 1 minutes
Next N bound 2 train will arrive at station Clark St in 3 minutes
Next N bound 2 train will arrive at station Wall St in 8 minutes
Next N bound 2 train will arrive at station Fulton St in 9 minutes
Next N bound 2 train will arrive at station Park Pl in 11 minutes
Next N bound 2 train will arrive at station Chambers St in 12 minutes
Next N bound 2 train will arrive at station Atlantic Av - Barclays Ctr in 1 minutes
Next N bound 2 train will arrive at station Nevins St in 2 minutes
Next N bound 2 train will arrive at station Sterling St in 0 minutes
Next N bound 2 train will arrive at station President St in 2 minutes
Next N bound 2 train will arrive at station Franklin Av in 4 minutes
Next N bound 2 train will arrive at station Chambers St in 0 minutes
Next N bound 3 train will arrive at station 125 St in 0 minutes
Next N bound 3 train will arrive at station 135 St in 2 minutes
Next N bound 3 train will arrive at station 145 St in 4 minutes
Next N bound 3 train will arrive at station Park Pl in 1 minutes
Next N bound 3 train will arrive at station Chambers St in 2 minutes
Next N bound 3 train will arrive at station 14 St in 7 minutes
Next N bound 3 train will arrive at station 34 St - Penn Station in 11 minutes
Next S bound 3 train will arrive at station Times Sq - 42 St in 2 minutes
Next S bound 3 train will arrive at station 34 St - Penn Station in 3 minutes
Next S bound 3 train will arrive at station 14 St in 6 minutes
Next N bound 3 train will arrive at station Sutter Av - Rutland Rd in 0 minutes
Next N bound 3 train will arrive at station Crown Hts - Utica Av in 3 minutes
Next N bound 3 train will arrive at station Kingston Av in 5 minutes
Next N bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next N bound 4 train will arrive at station Bedford Park Blvd - Lehman College in 2 minutes
Next N bound 4 train will arrive at station Mosholu Pkwy in 4 minutes
Next N bound 4 train will arrive at station Woodlawn in 6 minutes
Next S bound 4 train will arrive at station Borough Hall in 2 minutes
Next S bound 4 train will arrive at station Nevins St in 4 minutes
Next S bound 4 train will arrive at station Atlantic Av - Barclays Ctr in 5 minutes
Next S bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next S bound 4 train will arrive at station Fordham Rd in 2 minutes
Next S bound 4 train will arrive at station 183 St in 3 minutes
Next N bound 5X train will arrive at station E 180 St in 7 minutes
Next N bound 5X train will arrive at station Bronx Park East in 9 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 11 minutes
Next N bound 5X train will arrive at station Allerton Av in 13 minutes
Next N bound 5X train will arrive at station Burke Av in 15 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 17 minutes
Next N bound 5X train will arrive at station 219 St in 18 minutes
Next N bound 5X train will arrive at station 225 St in 20 minutes
Next N bound 5X train will arrive at station 233 St in 21 minutes
Next N bound 5X train will arrive at station Nereid Av in 23 minutes
Next S bound 5 train will arrive at station Wall St in 0 minutes
Next S bound 5 train will arrive at station Bowling Green in 2 minutes
Next S bound 5 train will arrive at station Borough Hall in 8 minutes
Next S bound 5 train will arrive at station Nevins St in 10 minutes
Next S bound 5 train will arrive at station Atlantic Av - Barclays Ctr in 12 minutes
Next S bound 5 train will arrive at station Franklin Av in 16 minutes
Next N bound 5X train will arrive at station Grand Central - 42 St in 0 minutes
Next N bound 5 train will arrive at station President St in 0 minutes
Next N bound 5 train will arrive at station Franklin Av in 2 minutes
Next N bound 5 train will arrive at station 86 St in 1 minutes
Next S bound 6 train will arrive at station Brooklyn Bridge - City Hall in 1 minutes
Next N bound 6 train will arrive at station 14 St - Union Sq in 0 minutes
Next S bound 6 train will arrive at station Westchester Sq - E Tremont Av in 0 minutes
Next S bound 6 train will arrive at station Zerega Av in 1 minutes
Next S bound 6 train will arrive at station Castle Hill Av in 2 minutes
Next N bound 6X train will arrive at station Spring St in 0 minutes
Next N bound 6X train will arrive at station Bleecker St in 1 minutes
Next N bound 6X train will arrive at station Astor Pl in 3 minutes
Next S bound 1 train will arrive at station Times Sq - 42 St in 59 minutes
Next S bound 1 train will arrive at station 86 St in 0 minutes
Next S bound 1 train will arrive at station 79 St in 1 minutes
Next S bound 1 train will arrive at station 72 St in 2 minutes
Next S bound 1 train will arrive at station 66 St - Lincoln Center in 4 minutes
Next N bound 1 train will arrive at station 18 St in 0 minutes
Next N bound 2 train will arrive at station E 180 St in 0 minutes
Next N bound 2 train will arrive at station Freeman St in 0 minutes
Next S bound 2 train will arrive at station Fulton St in 2 minutes
Next N bound 2 train will arrive at station 96 St in 0 minutes
Next N bound 2 train will arrive at station 34 St - Penn Station in 1 minutes
Next N bound 2 train will arrive at station Times Sq - 42 St in 2 minutes
Next S bound 2 train will arrive at station 135 St in 1 minutes
Next S bound 2 train will arrive at station Gun Hill Rd in 1 minutes
Next S bound 2 train will arrive at station Burke Av in 2 minutes
Next S bound 2 train will arrive at station Allerton Av in 4 minutes
Next S bound 2 train will arrive at station Pelham Pkwy in 6 minutes
Next S bound 3 train will arrive at station Pennsylvania Av in 1 minutes
Next N bound 3 train will arrive at station 72 St in 3 minutes
Next N bound 3 train will arrive at station 96 St in 6 minutes
Next N bound 3 train will arrive at station Borough Hall in 0 minutes
Next S bound 3 train will arrive at station Chambers St in 2 minutes
Next S bound 3 train will arrive at station Park Pl in 4 minutes
Next S bound 3 train will arrive at station Fulton St in 7 minutes
Next S bound 3 train will arrive at station Wall St in 9 minutes
Next N bound 3 train will arrive at station Grand Army Plaza in 0 minutes
Next S bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next S bound 4 train will arrive at station Fordham Rd in 1 minutes
Next S bound 4 train will arrive at station 183 St in 3 minutes
Next N bound 5X train will arrive at station Eastchester - Dyre Av in 0 minutes
Next S bound 5 train will arrive at station Intervale Av in 0 minutes
Next S bound 5 train will arrive at station Prospect Av in 2 minutes
Next S bound 5 train will arrive at station Jackson Av in 4 minutes
Next S bound 5 train will arrive at station 3 Av - 149 St in 6 minutes
Next S bound 5 train will arrive at station 149 St - Grand Concourse in 8 minutes
Next S bound 5 train will arrive at station 138 St - Grand Concourse in 12 minutes
Next S bound 5 train will arrive at station 125 St in 14 minutes
Next N bound 5 train will arrive at station Atlantic Av - Barclays Ctr in 2 minutes
Next N bound 5 train will arrive at station Nevins St in 4 minutes
Next N bound 5 train will arrive at station Borough Hall in 6 minutes
Next N bound 5 train will arrive at station Bowling Green in 11 minutes
Next N bound 5 train will arrive at station Wall St in 13 minutes
Next N bound 5 train will arrive at station Fulton St in 14 minutes
Next N bound 5 train will arrive at station Brooklyn Bridge - City Hall in 16 minutes
Next N bound 5 train will arrive at station 14 St - Union Sq in 20 minutes
Next N bound 5 train will arrive at station Grand Central - 42 St in 24 minutes
Next N bound 5 train will arrive at station 59 St in 27 minutes
Next N bound 5 train will arrive at station Jackson Av in 47 minutes
Next N bound 5 train will arrive at station Prospect Av in 48 minutes
Next N bound 5 train will arrive at station Intervale Av in 50 minutes
Next N bound 5 train will arrive at station Simpson St in 51 minutes
Next N bound 5 train will arrive at station Freeman St in 53 minutes
Next N bound 5 train will arrive at station 174 St in 55 minutes
Next N bound 5 train will arrive at station West Farms Sq - E Tremont Av in 57 minutes
Next S bound 5 train will arrive at station Pelham Pkwy in 0 minutes
Next N bound 6 train will arrive at station Whitlock Av in 0 minutes
Next N bound 6 train will arrive at station Elder Av in 2 minutes
Next N bound 6 train will arrive at station Morrison Av- Sound View in 3 minutes
Next S bound 6 train will arrive at station 51 St in 1 minutes
Next N bound 6X train will arrive at station 110 St in 0 minutes
Next N bound 6X train will arrive at station Bleecker St in 1 minutes
Next N bound 6X train will arrive at station Astor Pl in 2 minutes
Next S bound 1 train will arrive at station Houston St in 0 minutes
Next S bound 1 train will arrive at station Canal St in 1 minutes
Next S bound 1 train will arrive at station Christopher St - Sheridan Sq in 0 minutes
Next N bound 1 train will arrive at station 145 St in 0 minutes
Next N bound 1 train will arrive at station 157 St in 2 minutes
Next N bound 1 train will arrive at station 168 St - Washington Hts in 3 minutes
Next N bound 1 train will arrive at station 96 St in 0 minutes
Next N bound 1 train will arrive at station 103 St in 2 minutes
Next N bound 1 train will arrive at station Cathedral Pkwy in 3 minutes
Next S bound 1 train will arrive at station 86 St in 0 minutes
Next S bound 1 train will arrive at station 79 St in 1 minutes
Next S bound 1 train will arrive at station 72 St in 2 minutes
Next S bound 1 train will arrive at station 66 St - Lincoln Center in 4 minutes
Next S bound 1 train will arrive at station 137 St - City College in 0 minutes
Next N bound 1 train will arrive at station 23 St in 0 minutes
Next N bound 1 train will arrive at station 28 St in 1 minutes
Next N bound 1 train will arrive at station 34 St - Penn Station in 3 minutes
Next N bound 1 train will arrive at station Times Sq - 42 St in 4 minutes
Next S bound 1 train will arrive at station 231 St in 1 minutes
Next S bound 1 train will arrive at station Marble Hill - 225 St in 2 minutes
Next S bound 1 train will arrive at station 215 St in 4 minutes
Next N bound 2 train will arrive at station 233 St in 0 minutes
Next N bound 2 train will arrive at station Nereid Av in 1 minutes
Next N bound 2 train will arrive at station West Farms Sq - E Tremont Av in 0 minutes
Next S bound 2 train will arrive at station Beverly Rd in 1 minutes
Next S bound 2 train will arrive at station Newkirk Av in 3 minutes
Next S bound 2 train will arrive at station Flatbush Av - Brooklyn College in 5 minutes
Next S bound 2 train will arrive at station Grand Army Plaza in 0 minutes
Next S bound 2 train will arrive at station Eastern Pkwy - Brooklyn Museum in 2 minutes
Next N bound 2 train will arrive at station Prospect Av in 0 minutes
Next N bound 2 train will arrive at station Intervale Av in 1 minutes
Next N bound 2 train will arrive at station Simpson St in 3 minutes
Next S bound 2 train will arrive at station Clark St in 2 minutes
Next S bound 2 train will arrive at station Borough Hall in 4 minutes
Next S bound 2 train will arrive at station Hoyt St in 5 minutes
Next S bound 2 train will arrive at station Nevins St in 6 minutes
Next S bound 2 train will arrive at station 125 St in 0 minutes
Next S bound 2 train will arrive at station 116 St in 1 minutes
Next S bound 2 train will arrive at station Central Park North (110 St) in 2 minutes
Next S bound 2 train will arrive at station 174 St in 0 minutes
Next N bound 2 train will arrive at station Bergen St in 0 minutes
Next N bound 2 train will arrive at station Sterling St in 0 minutes
Next N bound 2 train will arrive at station President St in 1 minutes
Next N bound 2 train will arrive at station Franklin Av in 4 minutes
Next N bound 2 train will arrive at station Newkirk Av in 0 minutes
Next N bound 2 train will arrive at station Beverly Rd in 1 minutes
Next N bound 2 train will arrive at station Church Av in 3 minutes
Next N bound 2 train will arrive at station Franklin Av in 0 minutes
Next N bound 3 train will arrive at station 145 St in 59 minutes
Next S bound 3 train will arrive at station Pennsylvania Av in 0 minutes
Next N bound 3 train will arrive at station Park Pl in 1 minutes
Next N bound 3 train will arrive at station Chambers St in 2 minutes
Next N bound 3 train will arrive at station 14 St in 7 minutes
Next N bound 3 train will arrive at station 34 St - Penn Station in 11 minutes
Next N bound 3 train will arrive at station Nostrand Av in 0 minutes
Next N bound 4 train will arrive at station Kingsbridge Rd in 0 minutes
Next N bound 4 train will arrive at station Bedford Park Blvd - Lehman College in 2 minutes
Next N bound 4 train will arrive at station Mosholu Pkwy in 4 minutes
Next N bound 4 train will arrive at station Woodlawn in 6 minutes
Next N bound 4 train will arrive at station 138 St - Grand Concourse in 0 minutes
Next N bound 5 train will arrive at station Allerton Av in 0 minutes
Next N bound 5 train will arrive at station Burke Av in 2 minutes
Next N bound 5 train will arrive at station Gun Hill Rd in 4 minutes
Next N bound 5 train will arrive at station 219 St in 5 minutes
Next N bound 5 train will arrive at station 225 St in 7 minutes
Next N bound 5 train will arrive at station 233 St in 8 minutes
Next N bound 5 train will arrive at station Nereid Av in 10 minutes
Next N bound 5X train will arrive at station Morris Park in 14 minutes
Next N bound 5X train will arrive at station Pelham Pkwy in 15 minutes
Next N bound 5X train will arrive at station Gun Hill Rd in 18 minutes
Next N bound 5X train will arrive at station Baychester Av in 20 minutes
Next S bound 5 train will arrive at station President St in 1 minutes
Next S bound 5 train will arrive at station Sterling St in 2 minutes
Next S bound 5 train will arrive at station Winthrop St in 4 minutes
Next N bound 5X train will arrive at station 59 St in 0 minutes
Next S bound 5 train will arrive at station 86 St in 3 minutes
Next S bound 5 train will arrive at station Morris Park in 0 minutes
Next S bound 5 train will arrive at station E 180 St in 3 minutes
Next S bound 5 train will arrive at station West Farms Sq - E Tremont Av in 5 minutes
Next S bound 5 train will arrive at station 174 St in 7 minutes
Next S bound 5 train will arrive at station Freeman St in 10 minutes
Next N bound 6X train will arrive at station Castle Hill Av in 3 minutes
Next N bound 6X train will arrive at station Zerega Av in 4 minutes
Next N bound 6X train will arrive at station Westchester Sq - E Tremont Av in 5 minutes
Next N bound 6X train will arrive at station Middletown Rd in 7 minutes
Next N bound 6X train will arrive at station Buhre Av in 8 minutes
Next N bound 6X train will arrive at station Pelham Bay Park in 11 minutes
Next N bound 6 train will arrive at station Hunts Point Av in 0 minutes
Next S bound 6 train will arrive at station 51 St in 1 minutes
Next S bound 6 train will arrive at station 28 St in 0 minutes
Next N bound 6X train will arrive at station 110 St in 0 minutes
Next S bound 6 train will arrive at station 68 St - Hunter College in 1 minutes
Next S bound 6 train will arrive at station 125 St in 1 minutes
Next N bound 6X train will arrive at station 77 St in 0 minutes
Next N bound 6X train will arrive at station 86 St in 2 minutes
Next N bound 6X train will arrive at station 96 St in 5 minutes
Next N bound 6X train will arrive at station 103 St in 6 minutes
Next N bound 6X train will arrive at station 51 St in 0 minutes
Next N bound 6X train will arrive at station 59 St in 2 minutes
Next N bound 6X train will arrive at station 68 St - Hunter College in 4 minutes
Next S bound 6 train will arrive at station E 149 St in 59 minutes
Next S bound 6 train will arrive at station Westchester Sq - E Tremont Av in 0 minutes
Next S bound 6 train will arrive at station Zerega Av in 1 minutes
Next S bound 6 train will arrive at station Castle Hill Av in 2 minutes
Next S bound 6 train will arrive at station Morrison Av- Sound View in 0 minutes
Next S bound 6 train will arrive at station Elder Av in 1 minutes
Next S bound 6 train will arrive at station Whitlock Av in 2 minutes
Next N bound 6X train will arrive at station Bleecker St in 1 minutes
Next N bound 6X train will arrive at station Astor Pl in 2 minutes
Next S bound 1 train will arrive at station Franklin St in 0 minutes
Next N bound 1 train will arrive at station Cathedral Pkwy in 0 minutes
Next S bound 1 train will arrive at station 231 St in 0 minutes
Next S bound 1 train will arrive at station Marble Hill - 225 St in 2 minutes
Next S bound 1 train will arrive at station 215 St in 3 minutes
Next N bound 2 train will arrive at station 3 Av - 149 St in 1 minutes
Next N bound 2 train will arrive at station Jackson Av in 4 minutes
Next S bound 2 train will arrive at station Park Pl in 0 minutes
Next S bound 2 train will arrive at station Bronx Park East in 1 minutes
Next S bound 2 train will arrive at station E 180 St in 3 minutes
Next S bound 2 train will arrive at station West Farms Sq - E Tremont Av in 5 minutes
Next S bound 3 train will arrive at station Nostrand Av in 0 minutes
Next N bound 3 train will arrive at station Park Pl in 0 minutes
Next N bound 3 train will arrive at station Chambers St in 2 minutes
Next N bound 3 train will arrive at station 14 St in 7 minutes
Next N bound 3 train will arrive at station 34 St - Penn Station in 10 minutes
Next N bound 3 train will arrive at station Borough Hall in 0 minutes
Next N bound 4 train will arrive at station 59 St in 1 minutes
Next S bound 5 train will arrive at station Winthrop St in 0 minutes
Next N bound 5X train will arrive at station Wall St in 0 minutes
Next N bound 5X train will arrive at station Fulton St in 1 minutes
Next N bound 5X train will arrive at station Brooklyn Bridge - City Hall in 3 minutes
Next N bound 6 train will arrive at station Whitlock Av in 0 minutes
Next N bound 6 train will arrive at station Elder Av in 1 minutes
Next N bound 6 train will arrive at station Morrison Av- Sound View in 2 minutes
Next S bound 6 train will arrive at station 51 St in 59 minutes
Next S bound 6 train will arrive at station 116 St in 0 minutes
Next S bound 6 train will arrive at station 110 St in 2 minutes
Next S bound 6 train will arrive at station 103 St in 3 minutes
Next S bound 6 train will arrive at station 96 St in 4 minutes
Next S bound 1 train will arrive at station 79 St in 0 minutes
Next S bound 1 train will arrive at station 72 St in 2 minutes
Next S bound 1 train will arrive at station 66 St - Lincoln Center in 3 minutes
Next S bound 1 train will arrive at station Marble Hill - 225 St in 1 minutes
Next S bound 1 train will arrive at station 215 St in 2 minutes
Next N bound 2 train will arrive at station Nereid Av in 0 minutes
Next S bound 2 train will arrive at station Beverly Rd in 0 minutes
Next S bound 2 train will arrive at station Newkirk Av in 2 minutes
Next S bound 2 train will arrive at station Flatbush Av - Brooklyn College in 4 minutes
Next N bound 2 train will arrive at station Freeman St in 0 minutes
Next S bound 2 train will arrive at station Fulton St in 2 minutes
Next N bound 2 train will arrive at station 116 St in 0 minutes
Next N bound 2 train will arrive at station 125 St in 1 minutes
Next N bound 2 train will arrive at station 135 St in 3 minutes
Next S bound 2 train will arrive at station 96 St in 0 minutes
Next S bound 2 train will arrive at station 72 St in 2 minutes
Next S bound 2 train will arrive at station Times Sq - 42 St in 7 minutes
Next N bound 2 train will arrive at station Beverly Rd in 0 minutes
Next N bound 2 train will arrive at station Church Av in 2 minutes
Next N bound 2 train will arrive at station Chambers St in 0 minutes
Next N bound 2 train will arrive at station Franklin Av in 59 minutes
Next S bound 3 train will arrive at station Bergen St in 0 minutes
Next S bound 3 train will arrive at station Clark St in 59 minutes
Next S bound 3 train will arrive at station Borough Hall in 1 minutes
Next S bound 3 train will arrive at station Hoyt St in 2 minutes
Next N bound 3 train will arrive at station Franklin Av in 0 minutes
Next N bound 3 train will arrive at station Eastern Pkwy - Brooklyn Museum in 2 minutes
Next S bound 4 train will arrive at station Burnside Av in 0 minutes
Next S bound 4 train will arrive at station 176 St in 2 minutes
Next S bound 4 train will arrive at station Mt Eden Av in 3 minutes
Next S bound 4 train will arrive at station 170 St in 4 minutes
Next S bound 4 train will arrive at station 167 St in 6 minutes
Next S bound 4 train will arrive at station Fordham Rd in 1 minutes
Next S bound 4 train will arrive at station 183 St in 2 minutes
Next N bound 6X train will arrive at station Castle Hill Av in 1 minutes
Next N bound 6X train will arrive at station Zerega Av in 2 minutes
Next N bound 6X train will arrive at station Westchester Sq - E Tremont Av in 3 minutes
Next N bound 6X train will arrive at station Middletown Rd in 4 minutes
Next N bound 6X train will arrive at station Buhre Av in 6 minutes
Next N bound 6X train will arrive at station Pelham Bay Park in 8 minutes
Next N bound 6 train will arrive at station Elder Av in 1 minutes
Next N bound 6 train will arrive at station Morrison Av- Sound View in 2 minutes
Next S bound 6 train will arrive at station 28 St in 0 minutes
Next S bound 6 train will arrive at station 77 St in 0 minutes
Next S bound 6 train will arrive at station E 143 St - St Mary's St in 0 minutes
Next S bound 6 train will arrive at station Cypress Av in 2 minutes
Next S bound 6 train will arrive at station Brook Av in 3 minutes
Next S bound 6 train will arrive at station 3 Av - 138 St in 4 minutes
Next N bound 6 train will arrive at station 23 St in 1 minutes
Traceback (most recent call last):
  File "/Users/stevebaker/Documents/projects/kafkaProjects/cloudboxlabs/blog-code/streaming/consumer.py", line 78, in <module>
    MTATrainTracker().run()
  File "/Users/stevebaker/Documents/projects/kafkaProjects/cloudboxlabs/blog-code/streaming/consumer.py", line 74, in run
    self.process_message(msg.decode('utf-8'))
  File "/Users/stevebaker/Documents/projects/kafkaProjects/cloudboxlabs/blog-code/streaming/consumer.py", line 31, in process_message
    trip_update = json.loads(message)
  File "/Users/stevebaker/anaconda/envs/beepscore/lib/python3.6/json/__init__.py", line 354, in loads
    return _default_decoder.decode(s)
  File "/Users/stevebaker/anaconda/envs/beepscore/lib/python3.6/json/decoder.py", line 339, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "/Users/stevebaker/anaconda/envs/beepscore/lib/python3.6/json/decoder.py", line 357, in raw_decode
    raise JSONDecodeError("Expecting value", s, err.value) from None
json.decoder.JSONDecodeError: Expecting value: line 1 column 1 (char 0)

Process finished with exit code 139 (interrupted by signal 11: SIGSEGV)