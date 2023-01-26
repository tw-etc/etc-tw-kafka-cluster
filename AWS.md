# Running on Amazon WebServices

Creating a kafka-cluster with monitoring tools setup in [AWS](https://aws.amazon.com). **No support** of SSL or ACLs.

:warning: Only differences to [local Vagrant version](README.md) are described.

## Init

Adapt [ansible/inventories/aws/hosts](ansible/inventories/aws/hosts) according to your setup.

* Create 5 EC2 instances in AWS using READHAT 8 or above
* generate key/value pair file and download to your local and add permissions as chmod 400
* copy all the 5 public dns names in the ansible/inventories/aws/hosts
*

## pre-req

```
run this command on all the monitor servers if REDHAT 9 is used , if REDHAT 8 no need to run

sudo update-crypto-policies --set DEFAULT:SHA1

```

## Setup

```bash
cd ansible

ansible-playbook -i inventories/aws/ --private-key ~/bdavay.cer cluster-aws.yml
```

### Connections

| Name | |
|:-- |:-- |
|Kafka Brokers|kafka-1:9092,kafka-2:9092,kafka-3:9092|


## Kafka

Either ssh into one of your machines where Kafka is installed or install Kafka CLI tools locally.

### Topic Creation

```bash
kafka-topics.sh --zookeeper kafka-1:2181 --create --replication-factor 1 --partitions 4 --topic sample
```

### Producer

```bash
kafka-console-producer.sh --broker-list kafka-1:9092,kafka-3:9092 --topic sample

Hey, is Kafka up and running?
```

### Consumer

```bash
kafka-console-consumer.sh --bootstrap-server kafka-1:9092,kafka-3:9092 --topic sample --from-beginning

Hey, is Kafka up and running?
```

### Producer Perf Test

```bash
kafka-producer-perf-test.sh --producer-props bootstrap.servers="kafka-1:9092,kafka-2:9092,kafka-3:9092" --topic sample --num-records 2000 --throughput 100 --record-size 256

```
