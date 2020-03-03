CLI Access to Data
==================

Calls to the isabl CLI is documented at https://docs.isabl.io/retrieve-data. Below are some additional examples.


1. Get analysis paths for all SCRNA runs at the sample level.

.. code-block:: bash

    isabl get-paths analyses -fi application__name "SCRNA"


2. Get analysis paths for all SCRNA runs at the individual level.

.. code-block:: bash

    isabl get-paths analyses -fi application__name "SCRNA Individual Application"