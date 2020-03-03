TODOs
=====

This list contains ideas for iterative improvement of the system.


1. A web server through which we can provide read access to the raw files by URL.

2. Symmetric import/export of relational and lake data.

3. Summary data for monitoring.

4. Metadata that specifies source, sink, format, encoding, provenance.

5. Makefile type interface to the docker runs so docker is not being called directly. This will potentially simplify the number of arguments that need to be passed by separating docker arguments from app arguments.

6. “docker run shahlab.azurecr.io/scp/single_cell_pipeline:v0.2.7 single_cell -h” should have default values and enumerated values documented for args like --submit, --storage etc.

7. Display output plots on vue UI.

8. Data consolidation between cloud and cluster with mirroring as a possible solution. Related to data archival, but would be good if the two can be decoupled to keep the system simple.

9. Data archival - movement between hot/warm/archive in data lake.

10. Data lake migration strategy which includes a change control process.   