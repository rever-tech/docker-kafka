# Docker images for Apache Kafka

> This repository contains two Dockerfiles, one for ZooKeeper and one for Kafka service.

## ZooKeeper

### Build

The pre-built image is publish in Docker Hub with name r3v3r/zookeeper

To build it yourself, run this command on project directory:
```sh
$ docker build -t <your_image_name_here>[:<tag name>] .
```
Example: Build an image from this Dockerfile with name rever/zookeeper and tag latest
```sh
$ docker build -t rever/zookeeper:latest ./zookeeper
```
### Run

Run with default configuration:
```sh
$ docker run -d -p 2181:2181 r3v3r/zookeeper 
```
NOTE: Replace `-p 2181:2181` with `-p <your_external_port>:2181`

Run with custom configuration
```sh
$ docker run -d -p <your_external_http_port>:2181 \
    -v <folder_contains_config_file_on_host>:/opt/zookeeper/conf \
    -v <data_folder_on_host>:<data_folder_on_container>:rw \
    [--name <your_container_name>] \
    r3v3r/zookeeper [<your_custom_config>]
```
NOTE: <data_folder_on_container> must be the same with `dataDir` in config file, default is `/data/zookeeper`

Example:
```sh
$ docker run -d -p 2181:2181 \
    -v /root/zookeeper/config:/opt/zookeeper/conf \
    -v /root/zookeeper/data:/data/zookeeper:rw \
    --name test_zookeeper \
    r3v3r/zookeeper
```
This command will spawn a docker container that run zookeeper service with these configurations:

 * Configuration file in `/root/zookeeper/config` of host machine
 * Data of ssdb will be storaged in `/root/zookeeper/data` of host machine

## Kafka

### Build
The pre-built image is publish in Docker Hub with name r3v3r/kafka

To build it yourself, run this command on project directory:
```sh
$ docker build -t <your_image_name_here>[:<tag name>] ./kafka
```
Example: Build an image from this Dockerfile with name rever/kafka and tag latest
```sh
$ docker build -t rever/kafka:latest ./kafka

### Run

Run with default configuration
```sh
$ docker run -d -p 9092:9092 --link <id_or_name_of_container_run_zookeeper>:zk r3v3r/kafka
```
NOTE: Replace `-p 9092:9092` with `-p <your_external_port>:9092`; replace <id_or_name_of_container_run_zookeeper> with ID or Name of container that run zookeeper service

Run with custom configuration
```sh
$ docker run -d -p <your_external_port>:9092 \
	[-v <folder_contains_config_file_on_host>:<folder_contains_config_file_in_container>] \
	[--name <your_container_name>] \
	r3v3r/kafka \
	[-c <path_to_server_config_file>] \
	[-z <zookeeper_host>:<zookeeper_port>] \
	[[-D <config_key>=<config_value>]...]
```
NOTE: Configuration passed via params will override configuration value in config file

Example:
```sh
$ docker run -d -p 9092:9092 \
	r3v3r/kafka \
	-z 172.17.0.12:2181 \
	-D auto.create.topics.enable=true \
	-D advertised.host.name=0.0.0.0 
	-D advertised.port=9092
```
This command will spawn a docker container that run kafka server with these configurations:

 * ZooKeeper service at: `172.17.0.12:2181`
 * Custom configurations:
 	* auto.create.topics.enable=true 
 	* advertised.host.name=0.0.0.0
 	* advertised.port=9092