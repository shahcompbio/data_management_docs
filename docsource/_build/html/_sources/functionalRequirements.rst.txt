Functional Requirements
========================

1. Store multi-modal data with support for hierarchical meta-data. (in-progress)

2. Patient centric/project centric analyses. (in-progress)

3. Export out (an untracked copy), move or replicate (with tracking) experiments and analyses with provenance in order to publish datasets externally (would be good if we can do this with reproducibility - ability for others to easily run our scripts). 

4. Permissions - ideally private, protected, public levels of rwx access on all access points - UI, cli, api and file system. But at minimum, group level protected access. 

5. Add data to an existing dataset and either rerun an existing analysis or run a new analysis.

6. Continue an analysis from where it left off in case of a crash (don't trash intermediate results).

7. Give users the ability to create and manage their own project space and run their own analyses (not be limited to a single isabl_bot user).

8. Archive data by moving to warm and tracking it in warm.

9. Delete data (and lineage) that is no longer required.

10. Compute interchangeably on cluster and cloud.

11. Provide metrics on user, compute and storage information.

12. Ability to add/change notes to track changes (natural language) either through jira or within isabl itself at various levels - project, experiment, analysis. (done?)

13. REST API (done)

14. Provide a pluggable interface for analysis pipelines/workflows in order to separate the analysis concern from the data management concern. (done)

15. Provenance and reproducibility. (done?)

16. Data lake search scoped by permissions. (done)

17. A visual interface to view results of analyses for spot checking. (done)

18. Workflow management - orchestration of multiple pipeline runs.


