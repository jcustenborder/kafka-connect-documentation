============
Schema Types
============

.. _schema-array:

.. rubric:: ARRAY


An ordered sequence of elements, each of which shares the same type.


.. _schema-boolean:

.. rubric:: BOOLEAN


Boolean value (true or false)


.. _schema-bytes:

.. rubric:: BYTES


Sequence of unsigned 8-bit bytes


.. _schema-float32:

.. rubric:: FLOAT32


32-bit IEEE 754 floating point number


.. _schema-float64:

.. rubric:: FLOAT64


64-bit IEEE 754 floating point number


.. _schema-int16:

.. rubric:: INT16


16-bit signed integer Note that if you have an unsigned 16-bit data source, INT32 will be required to safely capture all valid values


.. _schema-int32:

.. rubric:: INT32


32-bit signed integer Note that if you have an unsigned 32-bit data source, INT64 will be required to safely capture all valid values


.. _schema-int64:

.. rubric:: INT64


64-bit signed integer Note that if you have an unsigned 64-bit data source, the Decimal logical type (encoded as BYTES) will be required to safely capture all valid values


.. _schema-int8:

.. rubric:: INT8


8-bit signed integer Note that if you have an unsigned 8-bit data source, INT16 will be required to safely capture all valid values


.. _schema-map:

.. rubric:: MAP


A mapping from keys to values.


.. _schema-string:

.. rubric:: STRING


Character string that supports all Unicode characters.


.. _schema-struct:

.. rubric:: STRUCT


A structured record containing a set of named fields, each field using a fixed, independent Schema.

