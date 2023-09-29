# Confluent-self-managed-connect
Walk-through on how to set up self-managed connect on dedicated hardware or VM.

## Deploying a Self-Managed Custom Connector

## Background:
The purpose of this walkthrough is to demonstrate how to deploy a self-managed connector instance on a local VM or desktop to integrate with an existing CCloud environment.

## Prerequisites
- CCloud environment and cluster
- A provisioned Connect VM with suitable OS version - https://docs.confluent.io/platform/current/installation/versions-interoperability.html#operating-systems
- Connect Azure Instance Suggested - A4v2 or similar (8Gib Memory/4vCPU)
- Connect VM deployed in same region as CCloud Cluster with network connectivity
- Confluent CLI installed - https://docs.confluent.io/confluent-cli/current/install.html

## Connector Setup

Connect is a component of Confluent Platform.  Download Connect to your local machine/VM from the following link: https://www.confluent.io/get-started/?ajs_aid=22bdc8c0-02b8-4ba3-af9c-9361b17f9e6a&ajs_uid=552106#confluent-platform
- You will need to select your deployment as "Self-Managed" and enter in some information to get to the download.
  <img width="981" alt="Screen Shot 2023-09-28 at 12 10 38 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/0a15e8a6-2ca6-4b86-acaa-51f50c17fd96">

- Choose Local and download the ZIP/TAR file onto your Connect VM.

Once installed, a guided configuration can be found from the CCloud UI by navigating to CLI and Tools/Kafka Connect/Standalone.
1.  Generate Kafka cluster API key and secret so that your self-managed local Connect instance can authenticate to your confluent cloud environment.
2.  Leave the optional boxes unchecked
3.  Generate config  <img width="898" alt="Screen Shot 2023-09-28 at 11 56 00 AM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/9d4d857f-6ac9-408f-8898-e7c660b09f4c">
4.  Create a new file named my-connect-standalone.properties in the etc/kafka directory of your Connect VM and copy/paste your generated config file.
5.  Log into CCloud via the CLI
     - confluent login {enter email/pwd}
     - confluent environment list
     - confluent environment use {environment-id}
     - confluent kafka cluster list
     - confluent kafka cluster use {cluster-id}
     - Confluent api-key create --resource {lkc-xxxx} --description {testing}
     - Confluent api-key use {xxxxxxx} --resource {lkc-xxxx}
6.  Create a topic so that we can run our sink connector to consume from:
    - confluent kafka topic create --partitions 1 test-topic
7.  Produce some valid JSON data to the test-topic using the CLI as shown below: <img width="762" alt="Screen Shot 2023-09-28 at 12 02 24 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/c64d94c6-4cb6-4f13-84e3-fe9378f6cc0a">
     
8.  Create a connector property file.  Save the file in the etc/kafka directory of your Connect VM, name it my-file-sink.properties to look like the following:
   <img width="504" alt="Screen Shot 2023-09-28 at 12 24 19 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/56867a8d-de4a-4962-9121-03899ba4e957">



9.  Run the connect-standalone script with the filenames and arguments
     - ./bin/connect-standalone  ./etc/kafka/my-connect-standalone.properties ./etc/kafka/my-file-sink.properties
10.  You should now be able to check the topic to see the messages produced and consumed from the connector
<img width="1484" alt="Screen Shot 2022-10-25 at 11 44 12 AM" src="https://user-images.githubusercontent.com/100879140/197833630-067e9ffc-d865-429e-8c7c-6c29b6097fa9.png">

## Installing Additional Connectors Using Confluent Hub

Now that we've successfully installed our Connect instance and verified it can communicate with CCloud, we can install additional connectors to our Connect VM.  Confluent Hub - https://www.confluent.io/hub/ is a repo that contains hundreds of Kafka connectors and provides documentation as well as installation detail.
1. In order to properly install the connectors we will need one important field added to the file.  The plugin.path will need added to show where the connectors will be installed.<img width="1241" alt="Screen Shot 2023-09-28 at 1 20 55 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/7f8b6946-4773-4710-af4e-1ad41b78074f">


4.  Navigate to https://www.confluent.io/hub/ and enter MongoDB in the search bar.   You should land on a page similar to this: 
<img width="1453" alt="Screen Shot 2023-09-28 at 12 48 53 PM" src="https://github.com/jrolfe426/confluent-self-managed-connect/assets/100879140/2f0f304b-7748-4d6a-972d-a1220050795c">

5.  To install, enter the following from your Connect VM:
    - sudo confluent-hub install mongodb/kafka-connect-mongodb:1.11.0
6.  Select the directory/path you wish to install the connector.  This will need to match the plugin.path field from step 2.
7.  Select which worker configs the connector will be installed to and accept the licence terms.
8.  Navigate to /usr/share/confluent-hub-components/mongodb-kafka-connect-mongodb/etc to view/access the property files for configuration.

