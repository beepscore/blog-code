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

### python protobuf classes
Apparently author already generated pyton protobuf classes like nyct_subway_pb2.py by running:

    python -m grpc_tools.protoc -Iprotos/ --python_out=. --grpc_python_out=. protos/gtfs-realtime.proto

### Kafka
In tutorial, both the Kafka cluster and the application components run on separate EC2 on AWS.
Try running locally instead.


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
