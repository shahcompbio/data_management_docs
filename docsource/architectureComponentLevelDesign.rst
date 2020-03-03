Architecture - Component Level Design
======================================

Architecture and Dependencies:
------------------------------

This section describes the components that make up the Isabl data lake sub-system that sits in the larger MSK system described in the section `Architecture - High Level Design <architectureSystemLevelDesign.html>`__.


.. _fig-main:

.. figure:: _static/isablComponentLevel.png

    Isabl dependency diagram.


1. The **Data Lake** is a file system where all file data is stored with just enough design and data structure needed to find/locate the data. The design and structure required for this function is built into the **Isabl (ingest) API**. In essence, the data is stored in a manner that does not presume the type of questions that might need to be answered by the data through queries made on the data in the future. The data in the lake is heterogeneous by nature, but it is critical that each type of data is well defined (documented unambiguously), identifiable and addressable (down to every data element), machine readable, well formatted (for ease of egest) and validated in order to allow for automatic egest. These factors are the responsibility of the Isabl **Apps** that write data into the Data Lake. Isabl Apps are executed through the **CLI**, a command line interface to the ingest API. The CLI may be installed on any compute unit that has network access to the Isabl ingest API.  The ingest API, CLI and Apps form the primary components of the isabl system.

2. **Egest** (secondary) data stores (a.k.a data marts) are built as needed for answering specific questions through queries made on the data. They may be built on demand and for specific subsets of the data (like sequence, blood, imaging etc). At the very least, an ElasticSearch store should exist to support basic querying and visualization.

3. All data will be made available to the end-user through **REST APIs** and any web **Browser**.

4. Compute may be any software process that needs to access data through APIs. The Browser, Isabl apps (that are executed via the CLI), **ETL** processes etc. are all examples of compute units that will utilize the APIs.


Design Features:
----------------

1. The system separates the data management concern (of data in the lake) from the analysis pipeline execution concern by providing a pluggable **CLI** through which analysis pipelines can be integrated into the system.

2. The system also separates out the data query and visualization concerns through the egest components. The postgres database is only used to locate data in the lake and view summary statistics for the lake.

3. Metadata is stored on the lake and made accessible via egest stores.

4. The system supports the standardization of input and output data formats by separating out the pre and post processing steps like validation and data format converion from the main analysis code through an **App** code wrapper infrastructure. Tools like validators and converters can be standardized and made available to Apps through this infrastructure. The system therefore allows analysts and scientists to construct the pipelines, and engineers to take these pipelines and standardize the way in which they interact with the Data Lake. An important aspect of standardization is reduction of information entropy. This makes downstream comparisons a lot easier.

5. The system provides a stateful analysis process that includes intermediate states to allow scientists to provide feedback in order to complete an analysis.
