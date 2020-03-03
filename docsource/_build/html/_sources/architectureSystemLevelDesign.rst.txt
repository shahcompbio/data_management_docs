Architecture - High Level Design
=================================

This section describes the larger system that supports the storage and analysis of data within the Isabl subsystem.

.. _fig-main:

.. figure:: _static/shah_lab_data_flow.png

    Shah Lab data storage and analysis system.

Design Rationale:
------------------

**eLab**

eLab was selected for storage of specimen data in order to overcome REDCap's limitation of not being able to store hierarchical data beyond a single level of (patient) hierarchy.

**REDCap**

REDCap was selected for the following reasons.

1. **ID collision detection**: It serves as a data collection or aggregation point from various other data sources, all of which have identifiable ids, while some of which may not have de-identified Ids that are common to the other sources (makes data joins difficult if not impossible). There is also the possibility of some datasets coming in with Ids that are unique only in their Id space (not globally unique). Specimen Ids coming from sources like Lab Medicine and IMF are an example of this. This holding area for data will serve as a place where data can be collected, globally identified and joined, and later de-identified before it gets propagated into the research space.

2. **Formal Specification** It serves as a place where a formal specification can be created for all data coming in (REDCap's data dictionary works as the formal spec). This specification aids in the development of validation mechanisms that will ultimately aid in keeping the data lake clean.

3. **PHI space** It has infosec approval.

4. **Low overhead** It is quick to set up and fairly easy to iterate on. No need for us to develop and maintain database and UI code. 

5. **Ability to integrate with other systems - has institutional support** The MSK EXTRACT system and workflow is built around the use of REDCap. Information systems has developed a lot of tooling around REDCap. Having REDCap will help integrate well with EXTRACT workflow and systems when it comes time to apply ontologies to further enrich the dataset. Also the data governance team is familiar with REDCap and having REDCap can help facilitate any collaboration we have with them.

6. **Review and annotation** Provide a view into the raw meta-data (outside of final visualization) so researchers and view the meta-data in detail and also provide annotations. 

7. **Parallelization of effort** Allow data collection and analysis pipeline execution to happen concurrently, at different speeds and with different groups to avoid bottlenecking.

8. **Multi-user system** with access control to prevent some users from seeing PHI or performing edits. 

9. **API** Has API. 

10 [Does have the drawback of not supporting hierarchical data like patient->specimen->aliquot level hierarchy.]

**Isabl Data Lake**

1. Has the ability to present results information on the UI. 

2. Simple interface with the main focus of data tracking.

3. Lightweight and scalable system (scalable at least to a Pb of data or storage limits of cluster).

4. Has API.

5. Multi-user system (but no access control)

6. [May lack generality and flexibility to support the storage/access and retrieval of multi-model data. This is being investigated.]

7. Need for separate isabl - admin access to django admin + sql (level of access is proportional to level of productivity), low maintainance overhead for devops work, quick to make code updates. Also helps core team improve their product through feedback from our end-to-end use.

**eLab**

1. - Has sample accessioning functionality.

2. Has specimen-aliquot level hierarchy, so specimen info needs to be entered only once for all of its aliquots.

3. Has ability to perform bulk updates of aliquots belonging to a particular specimen.

4. Simple yet powerful browser based query system.

5. Supports custom views with filters.

6. Generates globally unique ids. 

7. Has API.

8. Multi user system (but no access control).

9. Attach files at aliquot level (can't do with spreadsheets). 





