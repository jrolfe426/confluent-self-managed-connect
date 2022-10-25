# confluent-self-managed-connect
walk-through on how to set up self-managed connect via an avro schema via schema registry

## Deploying a Self-Managed Custom Connector

## Background:
The purpose of this walkthrough is to demonstrate how to deploy a self-managed sink connector on a local VM or desktop to integrate with an existing CCloud environment.  It also will leverage Confluent's fully-managed schema registry for data governance/validation.

## Prerequisites
- CCloud environment and cluster (Basic is suffice) with public endpoints
- Confluent CLI installed

## Connector Setup

Connector is a component of Confluent Platform.  Download connector to your local machine/VM from the following link: https://www.confluent.io/get-started/?ajs_aid=22bdc8c0-02b8-4ba3-af9c-9361b17f9e6a&ajs_uid=552106#confluent-platform

A guided approach can be found from the CCloud UI by navigating to CLI and Tools/Kafka Connect/Standalone.
1.  Generate Kafka cluster API key and secret so that your self-managed local connector can authenticate to your confluent cloud environment.
2.  Create a Schema Registry API key and secret
3.  Leave the optional boxes unchecked
4.  Generate config  <img width="1001" alt="Screen Shot 2022-10-25 at 10 03 14 AM" src="https://user-images.githubusercontent.com/100879140/197810274-97d117d7-1be7-428a-89f5-67f9177835ac.png">
5.  Create a new file named my-connect-standalone.properties in the etc directory and copy/paste your config file.
6.  Create a topic so that we can run our sink connector to consume from:
    - confluent kafka topic create --partitions 1 test-topic
7.  Create a Schema for use on the test-topic.  Navigate to Schema Registry/ + Add Schema
    - Name the Schema - Test Topic Schema/Avro
   <img width="1381" alt="Screen Shot 2022-10-25 at 10 28 56 AM" src="https://user-images.githubusercontent.com/100879140/197816508-9f264366-4bd5-419d-aa9b-dae0391416f9.png">
    - Validate and Create
    - Take note of the Schema ID.  This will be used later for our producer and connect sink.<img width="743" alt="Screen Shot 2022-10-25 at 10 30 30 AM" src="https://user-images.githubusercontent.com/100879140/197817269-9803a564-9a96-41fc-8b9a-79cf3a5c9bfb.png">
