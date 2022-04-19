## Install Kafka in  Redhat's OCP
[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/3Acy_0e4mfs/0.jpg)](https://www.youtube.com/watch?v=3Acy_0e4mfs)   
Administrator  >  OperatorHub > Red Hat Integration - AMQ Streams
## Install Kafka in  Minikube
https://strimzi.io/quickstarts/
```bash
minikube start --memory=4096 # 2GB default memory isn't always enough
```
### Applying Strimzi installation file
Next we apply the Strimzi install files, including ``ClusterRoles``, ``ClusterRoleBindings`` and some **Custom Resource Definitions** (CRDs). The CRDs define the schemas used for declarative management of the Kafka cluster, Kafka topics and users.
```bash
kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka
```

### Provision the Apache Kafka cluster
After that we feed Strimzi with a simple Custom Resource, which will then give you a small persistent Apache Kafka Cluster with one node each for Apache Zookeeper and Apache Kafka:
```bash
# Apply the `Kafka` Cluster CR file
kubectl apply -f https://strimzi.io/examples/latest/kafka/kafka-persistent-single.yaml -n kafka 
```

We now need to wait while Kubernetes starts the required pods, services and so on:
```bash
kubectl wait kafka/my-cluster --for=condition=Ready --timeout=300s -n kafka 
```
The above command might timeout if you’re downloading images over a slow connection. If that happens you can always run it again.

### Send and receive messages
Once the cluster is running, you can run a simple producer to send messages to a Kafka topic (the topic will be automatically created):
```bash
kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic
```
And to receive them in a different terminal you can run:
```bash
kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic --from-beginning
```

Enjoy your Apache Kafka cluster, running on Minikube!