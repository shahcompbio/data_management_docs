Introduction
=============


Isabl is a centralized system for managing bioinformatics data across one or more data lakes associated with compute units. The goal of the system is to allow data to be stored in a centralized location (MSK cluster) while allowing for computation to occur on one or more heterogenous compute units that include MSK cluster, Azure and AWS.

The system is being built with a high degree of automation in mind. i.e. it is expected that the only end-user interaction the system will require is read-only access to data and analysis results in the data lake. All other operations will be conducted through the back-end as described in the Ops section.
