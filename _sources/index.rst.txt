.. Kafka Connect Connectors documentation master file, created by
   sphinx-quickstart on Tue Jun 20 13:01:05 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Kafka Connect documentation!
=======================================

`Kafka Connect <https://kafka.apache.org/documentation/#introduction>`_ is a fault tolerant framework for running
connectors and tasks to pull data into and out of a Kafka Cluster. It is essentially the
`E <https://en.wikipedia.org/wiki/Extract,_transform,_load#Extract>`_ and `L <https://en.wikipedia.org/wiki/Extract,_transform,_load#Load>`_
of `ETL <https://en.wikipedia.org/wiki/Extract,_transform,_load>`_. Kafka Connect allows connectors and tasks to be spread
across a grouping of machines for increased throughput and resiliency.

This project is a collection of connectors and transforms that can be leveraged to build a realtime ETL pipeline.

.. toctree::
   :maxdepth: 1
   :caption: Contents:

   installation
   projects
   connectors
   transformations
   schemas


Indices and tables
==================

* :ref:`genindex`
* :ref:`search`


.. toctree::
   :hidden:
   :maxdepth: 0

   configdef.type
   schema.type
   glossary