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

# see Appendices
Appendix install dependencies for producer
Appendix install dependencies for consumer
Appendix install kafka (also installs dependency zookeeper)

## How to run app
In tutorial, both the Kafka cluster and the application components run on separate EC2 on AWS.
Try running locally instead.

    conda activate beepscore

### start zookeeper
 
     zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties
 
#### zookeeper log files
log files at /usr/local/var/lib/zookeeper/version-2/log.1
log directory size is ~67 Mb.

similar directory /user/local/var/log/zookeeper is empty. Did the python file change location?

### start kafka server

 open a second terminal window, run:

     kafka-server-start /usr/local/etc/kafka/server.properties

 I think kafka connects to zookeeper on port 2181.
 producer and consumer connect to kafka on port 9092.
 https://stackoverflow.com/questions/51338769/kafka-node-only-works-on-zookeeper-port-2181

#### kafka log files
log files at /usr/local/var/lib/kafka-logs also subdirectory test-0
log directory size is 1.08 Gb.

similar directory /user/local/var/log/kafka is empty. Did the python file change log location?

### in pycharm run producer.py

iterm kafka server partial log:

[2018-09-14 16:11:27,402] INFO [Log partition=test-0, dir=/usr/local/var/lib/kafka-logs] Loading producer state till offset 0 with message format version 2 (kafka.log.Log)
[2018-09-14 16:11:27,408] INFO [Log partition=test-0, dir=/usr/local/var/lib/kafka-logs] Completed load of log with 1 segments, log start offset 0 and log end offset 0 in 35 ms (kafka.log.Log)
[2018-09-14 16:11:27,409] INFO Created log for partition test-0 in /usr/local/var/lib/kafka-logs with properties {compression.type -> producer, message.format.version -> 2.0-IV1, file.delete.delay.ms -> 60000, max.message.bytes -> 1000012, min.compaction.lag.ms -> 0, message.timestamp.type -> CreateTime, message.downconversion.enable -> true, min.insync.replicas -> 1, segment.jitter.ms -> 0, preallocate -> false, min.cleanable.dirty.ratio -> 0.5, index.interval.bytes -> 4096, unclean.leader.election.enable -> false, retention.bytes -> -1, delete.retention.ms -> 86400000, cleanup.policy -> [delete], flush.ms -> 9223372036854775807, segment.ms -> 604800000, segment.bytes -> 1073741824, retention.ms -> 604800000, message.timestamp.difference.max.ms -> 9223372036854775807, segment.index.bytes -> 10485760, flush.messages -> 9223372036854775807}. (kafka.log.LogManager)
[2018-09-14 16:11:27,409] INFO [Partition test-0 broker=0] No checkpointed highwatermark is found for partition test-0 (kafka.cluster.Partition)
[2018-09-14 16:11:27,411] INFO Replica loaded for partition test-0 with initial high watermark 0 (kafka.cluster.Replica)
[2018-09-14 16:11:27,413] INFO [Partition test-0 broker=0] test-0 starts at Leader Epoch 0 from offset 0. Previous Leader Epoch was: -1 (kafka.cluster.Partition)
[2018-09-14 16:11:27,429] INFO [ReplicaAlterLogDirsManager on broker 0] Added fetcher for partitions List() (kafka.server.ReplicaAlterLogDirsManager)
[2018-09-14 16:11:29,317] INFO Updated PartitionLeaderEpoch. New: {epoch:0, offset:0}, Current: {epoch:-1, offset:-1} for Partition: test-0. Cache now contains 0 entries. (kafka.server.epoch.LeaderEpochFileCache)

### in pycharm run consumer.py

pycharm log:
/Users/stevebaker/anaconda/envs/beepscore/bin/python /Users/stevebaker/Documents/projects/kafkaProjects/cloudboxlabs/blog-code/streaming/consumer.py

Next S bound 1 train will arrive at station South Ferry in 1 minutes
Next N bound 1 train will arrive at station Van Cortlandt Park - 242 St in 0 minutes
Next S bound 1 train will arrive at station Chambers St in 1 minutes
Next S bound 1 train will arrive at station Cortlandt St in 2 minutes
...
< I omitted several hundred lines>
...
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

# Appendix install dependencies for producer

    python producer.py
    
## problem
ModuleNotFoundError: No module named 'confluent_kafka'

## fix- install package
In anaconda installed package python-confluent-kafka 0.11.4 (I think this contains module confluent_kafka)

    python producer.py

## problem
Traceback (most recent call last):
  File "producer.py", line 41, in <module>
    MTARealTime().run()
  File "producer.py", line 13, in __init__
    with open('.mta_api_key', 'r') as key_in:
FileNotFoundError: [Errno 2] No such file or directory: '.mta_api_key'

## fix- mta key
https://datamine.mta.info/user/register
I requested a key. Had to provide email, street address, phone number.
Keep api key secret, don't commit it to version control which could accidentally expose it.
Added file .mta_api_key containing api key.
.gitignore ignores filename .mta_api_key

## now producer parses, but can't connect to kafka on port 9092
without starting zookeeper or kafka,

    python producer.py
    
pycharm breakpoint shows producer.py produce_trip_updates
requests to mta, gets response, parses protobuf and converts to json.

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

## problem- producer can't connect to kafka on port 9092
consumer has similar problem.

## fix
brew install kafka and zookeeper and start them as described in "How to run app"
                
# Appendix install dependencies for consumer

    python consumer.py
    
## problem
  File "consumer.py", line 5, in <module>
    import arrow
ModuleNotFoundError: No module named 'arrow'

## fix
In anaconda installed package arrow, not pyarrow.
https://arrow.readthedocs.io/en/latest/

## problem- consumer can't connect to kafka on port 9092
producer has similar problem.

    python consumer.py
    
%3|1536891241.394|FAIL|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused
%3|1536891241.394|ERROR|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused
%3|1536891241.394|ERROR|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: 1/1 brokers are down

## fix
brew install kafka and zookeeper and start them as described in "How to run app"

# Appendix install kafka (also installs dependency zookeeper)
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
 I didn't edit it.
 
# Appendix stop kafka server
in terminal ctrl-C

...
2018-09-14 16:55:44,278] INFO [ReplicaManager broker=0] Shut down completely (kafka.server.ReplicaManager)
[2018-09-14 16:55:44,279] INFO Shutting down. (kafka.log.LogManager)
[2018-09-14 16:55:44,304] INFO [ProducerStateManager partition=test-0] Writing producer snapshot at offset 21147 (kafka.log.ProducerStateManager)
[2018-09-14 16:55:44,309] INFO [ProducerStateManager partition=__consumer_offsets-37] Writing producer snapshot at offset 3 (kafka.log.ProducerStateManager)
[2018-09-14 16:55:44,331] INFO Shutdown complete. (kafka.log.LogManager)
[2018-09-14 16:55:44,338] INFO [ZooKeeperClient] Closing. (kafka.zookeeper.ZooKeeperClient)
[2018-09-14 16:55:44,340] INFO Session: 0x100016f2abe0000 closed (org.apache.zookeeper.ZooKeeper)
[2018-09-14 16:55:44,341] INFO EventThread shut down for session: 0x100016f2abe0000 (org.apache.zookeeper.ClientCnxn)
[2018-09-14 16:55:44,341] INFO [ZooKeeperClient] Closed. (kafka.zookeeper.ZooKeeperClient)
[2018-09-14 16:55:44,342] INFO [ThrottledChannelReaper-Fetch]: Shutting down (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:44,704] INFO [ThrottledChannelReaper-Fetch]: Stopped (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:44,704] INFO [ThrottledChannelReaper-Fetch]: Shutdown completed (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:44,704] INFO [ThrottledChannelReaper-Produce]: Shutting down (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:45,708] INFO [ThrottledChannelReaper-Produce]: Stopped (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:45,708] INFO [ThrottledChannelReaper-Produce]: Shutdown completed (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:45,709] INFO [ThrottledChannelReaper-Request]: Shutting down (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:46,709] INFO [ThrottledChannelReaper-Request]: Stopped (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:46,709] INFO [ThrottledChannelReaper-Request]: Shutdown completed (kafka.server.ClientQuotaManager$ThrottledChannelReaper)
[2018-09-14 16:55:46,711] INFO [SocketServer brokerId=0] Shutting down socket server (kafka.network.SocketServer)
[2018-09-14 16:55:46,723] INFO [SocketServer brokerId=0] Shutdown completed (kafka.network.SocketServer)
[2018-09-14 16:55:46,726] INFO [KafkaServer id=0] shut down completed (kafka.server.KafkaServer)

# Appendix bike sample data
Different tutorial than subway data
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
