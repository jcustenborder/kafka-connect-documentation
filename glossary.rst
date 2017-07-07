========
Glossary
========


.. glossary::
    ETL
        Extract Transform Load

    Source Connector
        A Source connector is a connector that extends `SourceConnector <https://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/connect/source/SourceConnector.html>`_
        and is used by Kafka Connect to pull data into a Kafka Cluster. In the typical :term:`ETL` pattern a SourceConnector
        would be used to extract data from a source system. Source systems can be anything from a relational database, to
        a remote web service.

    Sink Connector
        A Sink connector is a connector that extends `SinkConnector <https://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/connect/sink/SinkConnector.html>`_
        and is used by Kafka Connect to pull data into a Kafka Cluster. In the typical :term:`ETL` pattern a SinkConnector
        would be used to load data into a target system.

    Transformation
        A Transformation is used to make changes to data on the fly before it is written to Kafka or written to the target
        system. This could be masking a field, or setting the value of a field.

    Distributed Mode
        Distributed mode is a configuration of Kafka Connect that allows a user to configure multiple worker nodes to
        share the workload of multiple connectors. In the event of a failure of one of the worker nodes, the running connectors
        will be distributed among the remaining worker nodes. This allows a high degree of fault tolerance.

    Standalone Mode
        Standalone mode is a configuration of Kafka Connect that runs as a single node. This mode is typically used with
        listening style connectors like the syslog and splunk connectors. This is not a fault tolerant mode. If fault tolerance
        is required it must be handled externally to Kafka Connect. For example with the syslog connector you can place a
        load balancer in front of multiple standalone nodes.

    Kafka Topic
        A Kafka Topic is the logical layer of a Kafka Cluster. A topic is made of one or more :term:`partition(s)` which
        can be spread across several :term:`kafka broker(s)`

    Kafka Broker(s)
        A Kafka broker is a server in a Kafka Cluster which is used to store data.

    Partition(s)
        A partition is a logical breakdown of a Topic which allows the data to be hosted across several machines. Kafka
        does not have a global guaranteed order but it does guarantee order within a partition. Partitions are selected
        based on a hash of the key.

    SSL
        A method for securing data between two hosts.

    Schema Registry
        The `schema registry <https://github.com/confluentinc/schema-registry>`_ is a serializer and deserializer that integrates
        with `Apache Avro <https://avro.apache.org/>`_ to track the schemas associated with data in a topic. It also is
        used to ensure the proper schema is used when writing to a topic.

    Avro
        `Apache Avro <https://avro.apache.org/>`_ is a binary serialization system. It drastically reduces the overall size
        of the data written to disk, which directly correlates to less IO and storage requirements.

    Key
        The key is the the part of the message that is used to determine the partition that data will be written to. If the
        topic is configured as a :term:`compacted topic` then only the last version of a key will be stored.

    Value
        The value is the part of the message that usually contains the bulk of the message. The value will typically contain
        fields with a representation of the data.

    CDC
        Change Data Capture

    Compacted Topic
        A compacted topic is used to store the last value of data for a key. This is used heavily in :term:`CDC` use cases.