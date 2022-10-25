# confluent-self-managed-connect
walk-through on how to set up self-managed connect via an avro schema via schema registry

## Deploying a Self-Managed Custom Connector

## Background:
The purpose of this walkthrough is to demonstrate how to deploy a self-managed sink connector on a local VM or desktop to integrate with an existing CCloud environment.  It also will leverage Confluent's fully-managed schema registry for data governance/validation.

## Prerequisites
- CCloud environment and cluster (Basic is suffice) with public endpoints
- Confluent CLI installed

## Connector Setup
<img width="1056" alt="Screen Shot 2022-10-25 at 10 44 07 AM" src="https://user-images.githubusercontent.com/100879140/197820147-c71f77ec-6755-4326-9466-5ce93b92d767.png">

Connector is a component of Confluent Platform.  Download connector to your local machine/VM from the following link: https://www.confluent.io/get-started/?ajs_aid=22bdc8c0-02b8-4ba3-af9c-9361b17f9e6a&ajs_uid=552106#confluent-platform

A guided approach can be found from the CCloud UI by navigating to CLI and Tools/Kafka Connect/Standalone.
1.  Generate Kafka cluster API key and secret so that your self-managed local connector can authenticate to your confluent cloud environment.
2.  Create a Schema Registry API key and secret
3.  Leave the optional boxes unchecked
4.  Generate config  <img width="1001" alt="Screen Shot 2022-10-25 at 10 03 14 AM" src="https://user-images.githubusercontent.com/100879140/197810274-97d117d7-1be7-428a-89f5-67f9177835ac.png">
5.  Create a new file named my-connect-standalone.properties in the etc directory and copy/paste your config file.
6.  Log into CCloud via the CLI
     - confluent login. <enter email/pwd>
     - confluent environment list
     - confluent environment use <desired environment id>
     - confluent kafka cluster list
     - confluent kafka cluster use <desired cluster id>
10.  Create a topic so that we can run our sink connector to consume from:
    - confluent kafka topic create --partitions 1 test-topic
11.  Create a Schema for use on the test-topic.  From the UI, navigate to Schema Registry/ + Add Schema
    - Name the Schema - self-managed schema/Avro
    - Validate and Create
    - Take note of the Schema ID.  This will be used later for our producer and connect sink.
    <img width="926" alt="Screen Shot 2022-10-25 at 10 45 40 AM" src="https://user-images.githubusercontent.com/100879140/197820507-11671485-58ea-4dfc-883b-28fde70bc905.png">
