NonFunctional Requirements
==========================

1. Individual-centric relational model (Individual > Sample > Analysis).

2. Maintainability: A centralized system for the administration of data across many compute units.

3. Maintainability: Runbooks for backup, restore and database migrations of database. Runbooks for backup, restore and migration of data in the data lake.

4. Standardization: Adhere to standards like FHIR [3]. [TBD]

5. Security: Secure login and deployment keys.

6. Usability: Simple workflow for each operational use case.

7. Extensibility: Ability to extend system for different types of data like blood, pathology etc.

8. Extensibility: Ability to extend entire system to allow for bespoke needs of compute units.

9. Scalability: Support thousands of patients, with tens of samples, tens of experimental data and tens of analyses results (10^3 (thousands of patients) • 10^1 (tens of samples) • 10^1 (tens of experimental data) • 10^1 (tens of analyses) = 10^6)

10. Governance: Ensure data integrity - quality through formal (unambiguous in terms of correctness and completeness) documentation, formal validation, hashing (to guard against data corruption), immutability (app data should not be modifiable, but only allowed to be extended) and process idempotency.
Convention over configuration as a strategy.
