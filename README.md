# Confluent-self-managed-connect
walk-through on how to set up self-managed connect via an avro schema via schema registry

## Deploying a Self-Managed Custom Connector

## Background:
The purpose of this walkthrough is to demonstrate how to deploy a self-managed connector instance on a local VM or desktop to integrate with an existing CCloud environment.

## Prerequisites
- CCloud environment and cluster
- A provisioned Connect VM with suitable OS version - https://docs.confluent.io/platform/current/installation/versions-interoperability.html#operating-systems
- Connect Azure Instance Suggested - A4v2 (8Gib Memory/4vCPU)
- Connect VM deployed in same region as CCloud Cluster with network connectivity
- Confluent CLI installed - https://docs.confluent.io/confluent-cli/current/install.html

## Connector Setup

Connector is a component of Confluent Platform.  Download connector to your local machine/VM from the following link: https://www.confluent.io/get-started/?ajs_aid=22bdc8c0-02b8-4ba3-af9c-9361b17f9e6a&ajs_uid=552106#confluent-platform
- You will need to select you deployment as "Self-Managed" and enter in some information to get to the download.
- Choose Local and download the ZIP/TAR file onto your Connect VM.

Once installed, a guided configuration can be found from the CCloud UI by navigating to CLI and Tools/Kafka Connect/Standalone.
1.  Generate Kafka cluster API key and secret so that your self-managed local Connect instance can authenticate to your confluent cloud environment.
2.  Leave the optional boxes unchecked
3.  Generate config  <img width="898" alt="Screen Shot 2023-09-28 at 11 56 00 AM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/9d4d857f-6ac9-408f-8898-e7c660b09f4c">
4.  Create a new file named my-connect-standalone.properties in the etc/kafka directory of your Connect VM and copy/paste your config file.
5.  Log into CCloud via the CLI
     - confluent login {enter email/pwd}
     - confluent environment list
     - confluent environment use {environment-id}
     - confluent kafka cluster list
     - confluent kafka cluster use {cluster-id}
6.  Create a topic so that we can run our sink connector to consume from:
    - confluent kafka topic create --partitions 1 test-topic
7.  Produce some valid JSON data to the test-topic using the CLI as shown below: <img width="762" alt="Screen Shot 2023-09-28 at 12 02 24 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/c64d94c6-4cb6-4f13-84e3-fe9378f6cc0a">
     
7.  Create a connector property file.  Save the file in the config directory of your Connect VM, name it my-file-sink.properties to look like the following:
     - name=my-file-sink
     - connector.class=org.apache.kafka.connect.tools.MockSinkConnector
     - tasks.max=1
     - topics=test-topic
     - file=my_file_sink_output.txt
8.  Run the connect-standalone script with the filenames and arguments
     - ./bin/connect-standalone  ./etc/kafka/my-connect-standalone.properties ./etc/kafka/my-file-sink.properties
9.  You should now be able to check the topic to see the messages produced and consumed from the connector
<img width="1484" alt="Screen Shot 2022-10-25 at 11 44 12 AM" src="https://user-images.githubusercontent.com/100879140/197833630-067e9ffc-d865-429e-8c7c-6c29b6097fa9.png">
<img width="1486" alt="Screen Shot 2022-10-25 at 11 46 50 AM" src="https://user-images.githubusercontent.com/100879140/197834144-21ad9589-e5a8-4c79-8f2e-ff2704981357.png">
