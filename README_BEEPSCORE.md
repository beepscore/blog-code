# Purpose
Do Kafka tutorial.

# References

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
