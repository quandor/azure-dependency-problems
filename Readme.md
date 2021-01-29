Problem description
===
If you introduce a newer Jackson dependency in your project than the version declared by
```
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-messaging-eventhubs</artifactId>
  <version>5.4.0</version>
</dependency>
```
communication with eventhub and/or blob storage container is not established without the
sdk giving any reason.

Observing the problem
===
To observe the problem do the following steps

1. `mvn clean jar`
1. ```
   java
     -Dstorage_connection_string=<storage-connection-string> \
     -Dstorage_container_name=<storage-container-name> \
     -Deventhub_namespace_connection_string=<eventhub-namespace-connection-string> \
     -Deventhub_name=<eventhub-name> \
     -Deventhub_consumer_group=<eventhub-consumer-group> \
     -jar target/azure-dependency-problems.jar```
1. See the log repeating ```
   com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore - Found blob for partition ...
   com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore - Blob <partition> is owned by ...```

Source of the problem
===
The source of this behaviour is the dependency
```
<dependency>
  <groupId>com.fasterxml.jackson.dataformat</groupId>
  <artifactId>jackson-dataformat-xml</artifactId>
  <version>2.12.0</version>
</dependency>
```
As soon as you remove that dependency from `pom.xml` and repeat
the steps above you will see a successful registration process
of the EventhubClient.

Sadly we did not make it so far to spot the code which is having
trouble with this dependency.
Ideally this problem would leave a trace in the logs.
