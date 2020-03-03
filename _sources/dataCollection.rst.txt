===============
Data Collection
===============

.. _fig-main:

.. figure:: _static/Spectrum_Tissue_Collection_Workflow.png

    MSK Spectrum Tissue Collection Workflow.

Clinic
=======
.. figure:: _static/Clinic_Dataflow.png

    Clinic Dataflow.

+--------------------------+-------------------------+
| Field in Workflow        | Data Fields             |
+==========================+=========================+
| Screen Clinic            | - Patient ID            |
|                          | - MRN                   |
+--------------------------+-------------------------+
| Surgery scheduled and    | - Surgery #             |
| technician notified      | - Surgery Type          |
|                          | - Surgery Date          |
|                          | - Surgeon               |
|                          | - Surgery Location      |
|                          | - Room                  |
+--------------------------+-------------------------+


OR Collection
==============
.. figure:: _static/OR_Sample_Collection_Dataflow.png

    OR Collection Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| OR Sample Collection     | - Excluded                      |
|                          | - Final Pathology               |
|                          | - Specify Diagnosis             |
|                          | - Reason for Exclusion          |
|                          | - Specimen Site                 |
|                          | - Site Details                  |
|                          | - Primary Site                  |
+--------------------------+---------------------------------+


Single Cell Dissociation
=========================
.. figure:: _static/Single_Cell_Dissociation_Dataflow.png

    Single Cell Dissociation Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| Single Cell Dissociation | - Processing Date               |
|                          | - Tissue Type                   |
|                          | - Total Cell Count (cells/ml)   |
|                          | - Live Cell Count (cells/ml)    |
|                          | - Total Volume (ml)             |
|                          | - Viability (%)                 |
|                          | - Downstream Submission         |
+--------------------------+---------------------------------+
| Flow Cytometry           | - Sorting Method                |
|                          | - Flow Instrument               |
|                          | - Flow Data Summary             |
|                          | - Flow Data fcs files           |
+--------------------------+---------------------------------+
| Storage of sorted single | - Storage Populations           |
| cell suspensions         |                                 |
+--------------------------+---------------------------------+

Single Cell RNA Sequencing (scRNASeq)
======================================
.. figure:: _static/scRNA_seq_dataflow.png

    scRNA seq Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| scRNA                    | - Submitted Populations         |
|                          | - Sequencing Technique          |
|                          | - scRNA Date of Submission      |
|                          | - scRNA Sequencing Location     |
|                          | - Initial Submission QC         |
+--------------------------+---------------------------------+
| 10X cDNA and Library     | - # of Cells Captured           |
| Prep                     | - QC Checks                     |
+--------------------------+---------------------------------+
| scRNA Sequencing         | - scRNA IGO ID                  |
|                          | - scRNA IGO Submission ID       |
|                          | - scRNA REX Sample ID           |
|                          | - scRNA Sample Status           |
|                          | - scRNA iLab Submission Form    |
|                          | - scRNA REX Submission Form     |
+--------------------------+---------------------------------+
| Storage of sorted single | - Storage Populations           |
| cell suspensions         |                                 |
+--------------------------+---------------------------------+

Direct Library Prep (DLP)
==========================
.. figure:: _static/DLP_dataflow.png

    DLP Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| DLP                      | - DLP Date of Submission        |
|                          | - DLP Sequencing Location       |
+--------------------------+---------------------------------+
| DLP Sequencing           | - DLP IGO ID                    |
|                          | - DLP IGO Submission ID         |
|                          | - DLP REX Sample ID             |
|                          | - DLP Sample Status             |
|                          | - DLP iLab Submission Form      |
|                          | - DLP REX Submission Form       |
+--------------------------+---------------------------------+

PPBC Collection
================
.. figure:: _static/PPBC.png

    PPBC Collection Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| FFPE                     | - PPBC Bank Number              |
| Frozen Tissue (-80c)     | - PPBC Aliquot Numbered         |
|                          | - PPBC Accession #              |
|                          | - PPBC Downstream Submission    |
+--------------------------+---------------------------------+

Whole Genome Sequencing (WGS)
==============================
.. figure:: _static/WGS.png

    WGS Dataflow.

+--------------------------+--------------------------------------+
| Field in Workflow        | Data Fields                          |
+==========================+======================================+
| Microdissection          | - Date requested from PPBC           |
|                          | - Date received from PPBC            |
|                          | - H&E available?                     |
|                          | - Sectioning Type                    |
|                          | - # of Curls Cut                     |
|                          | - # Slides Cut for Microdissection   |
|                          | - Thickness of tissue (um)           |
+--------------------------+--------------------------------------+
| DNA/RNA Extractions      | - Concentration (ng/ul)              |
|                          | - Volume (ul)                        |
+--------------------------+--------------------------------------+
| Bulk WGS                 | - WGS Date of Submission             |
|                          | - WGS Sequencing Location            |
|                          | - WGS IGO ID                         |
|                          | - WGS IGO Submission ID              |
|                          | - WGS REX Sample ID                  |
|                          | - WGS Sample Status                  |
|                          | - WGS iLab Submission Form           |
|                          | - WGS REX Submission Form            |
+--------------------------+--------------------------------------+

Immunofluorescence (IF)
========================
.. figure:: _static/IF.png

    IF Dataflow.

+--------------------------+---------------------------------+
| Field in Workflow        | Data Fields                     |
+==========================+=================================+
| IF                       | - IF Date of Submission         |
|                          | - # Slides submitted            |
|                          | - IF Panel                      |
+--------------------------+---------------------------------+

