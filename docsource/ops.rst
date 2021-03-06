Operations
===========

This section contains a set of runbooks and workflows for various backend operations required by the system.

Workflows:
----------

1. **Molecular Data**

This workflow details the process followed by Shah Lab for collecting and analysing blood and soft tissue samples using bioinformatics pipelines.

1.1 Specimen Accessioning: Specimens are accessioned by various MSK groups. Shahlab accessions scDNA and scRNA specimens. During scDNA and scRNA accessioning, specimen and aliquot meta-data is entered into eLabs and the specimens and aliquots are placed in storage. This step is conducted by a Shah Lab lab tech. Processing of other specimen types is not detailed here as it is done by the other MSK groups.

1.2 Sample Submission: Samples (or specimen) are submitted to IGO's REX system for sequencing by a Shah Lab tech. Metadata associated with the submission process is stored in eLab for all sample types. This process is currently executed manually on request.  

1.3 Meta-data Extraction: Meta-data associated with specimens accessioned at other MSK labs is extracted from IDB and stored in a REDCap instance. The process is automated and executed periodically (24 hour period).  

1.4 Sequencing: Once the submitted samples have been sequenced by IGO, their sequenced data (typically fastqs) and IGO generated meta-data is placed on a shared drive that can be accessed via juno, MSK's HPC cluster. This process is conducted by IGO and Shahlab is notified of the sequenced data via email. 

1.5 Data Import: Specimen meta-data and raw sequencing data (from IGO) is extracted from their respective sources, (REEDCap, eLab for meta-data, and an IGO shared drive for sequencing data and meta-data) merged and imported into the isabl data lake for final storage. The data import is done using isabl's API through the back end and not the user interface. During import, Isabl creates storage locations for "Experiments" as defined by its data model. The data that is imported must follow strict schema (data type and version) validation in order to ensure a clean data lake. This process is currently executed manually on request by a Shah Lab data manager.

1.6 Pipeline Execution: Various pipelines (or applications) may be executed on the imported data to yield "Analyses" as defined by the isabl data model. This process is currently executed manually by a Shah Lab developer on request. 

1.7 Access to Results: The sequenced data, meta-data and the results of the analyses may be accessed via Isabl's user interface for spot checking and manual verification. Results may be accessible by anyone within MSK.

1.8. Visualization: Result sets and associated meta-data is extracted from the data lake, transfored and loaded into an Elasticsearch instance. The Elasticsearch instance sources this data to a visualization engine that makes the data available through visual presentations. Visualizations may be viewable by anyone.  

2. **Clinical Data**

TBD

3. **Imaging Data**

TBD

The figure below illustrates this process for all types of molecular, clinical and imaging data being collected and analysed at Shah Lab.

[In order for image data to cross the de-identification line, a tool called HoBBit developed by Thomas Fuch's lab may be used.

.. _fig-main:

.. figure:: _static/shah_lab_data_flow.png

    Shah Lab Data Flow.


Routine Developer Operations:
-----------------------------

1. **Deployment** (See section on Installation)



2. **Set up a new project in isabl**


3. **Add user**

Using admin UI, apply the following steps

3.1. Use "Users" view to add the user (include email address), set Permissions to "active" and group to "Analyst" or "Engineer"

3.2. Use "Email Addresses" view to add email address of user and mark the user as "verified" and "primary".

3.3. Use the "Tokens" view to get user token and give to user for API access.


4. **Import data**

Check app.py documentation for each App. For example, see cellranger app

https://github.com/shahcompbio/isabl_apps/blob/master/isabl_apps/apps/shahcompbio/cellranger/app.py


5. **Delete imported data**

Sometimes it is necessary to delete imported data because the data was improperly imported, or for some other reason. The sequence of steps outlined below must be used to delete data.

5.1 TAKE A DB BACKUP!

5.2 Delete experiment dirs on data lake.

5.3 Delete experiment, aliquot, sample and individual objects in that sequence in the database via admin UI.

5.4 If there are results that were produced for the experiment through app runs, then delete the corresponding analysis dirs on the data lake, and the analysis objects on the database. This will require first deleting entries in the experiment-analysis many-many table via postgres. [Todo: Check if there are additional records/objects that need deleting. Also check if deleting analysis object from admin UI also cascade cleans records from the many-many table].


6. **Run analysis pipelines** (apps)

Check app.py documentation for each App. For example, see cellranger app

https://github.com/shahcompbio/isabl_apps/blob/master/isabl_apps/apps/shahcompbio/cellranger/app.py

To understand the current workflow, you first need to understand the problem that it tries to solve.
The main problem the current workflow is trying to solve is single user ownership of all files in the Isabl data lake.

Single user ownership would be a simple problem to solve if sudo privileges were allowed on the cluster.
Unfortunately, sudo is disabled and we are not able to run the command “chown”. As a result Elli's lab created
the following workflow.

In Isabl, any user with analyst privileges is able to run apps, and apps produce analyses. Each analysis has its own
indexed directory in the Isabl data lake. When a user kicks off an application, the analysis directory in the
data lake will be temporarily owned by the app running user, and group readable and writeable.

**Workflow:**

Isabl users with analyst permissions run apps that produce analyses. At this stage, the analyses directories will be
owned by the running users, but also group readable and writeable. If successful, the analyses status will be
set to the status “FINISHED”. Important to note, you are not able to view analyses results while they’re in
“FINISHED” state.

The ADMIN_USER, runs a command called “isabl process-finished” which converts all analyses in “FINISHED”
state to “SUCCEEDED” state. The conversion process includes the following:

- Copying analyses directories so they are owned by the ADMIN_USER
- Modifying permissions to only be group readable
- Creating analyses results dictionary (i.e. results visible via UI)
- Deleting the original user owned analyses directory

**IMPORTANT!**

The following must be ensured for the workflow to work properly:

- ``export ISABL_API_URL=https://<isabl_server_ip>/api/v1/``: Ensures that you are pointing to the production instance of Isabl
- ``export ISABL_CLIENT_ID=client id primary key``: Setting this environment variable configures your Isabl client settings from the Isabl API. Key settings that get set are:
      - ``ADMIN_USER`` is set. This setting is crucial because this flag lets isabl_cli know to set the analyses to a “FINISHED” state. By no means should the running user set themselves as the admin(unless they are the admin). This will finalize the results under the ownership of the running user, which is something we do not want.
      - ``BASE_STORAGE_URL`` = sets the path of the Isabl data lake on cluster.
      - **Run apps from the same file system that the datalake is on.**

**Analyses Batch Submit Setup (LSF)**

Isabl has the ability to submit multiple analyses to the cluster with a single command. In order to get this
functionality to work you must setup the following:

- In your apps_repo/apps_repo/apps/, modify__init__.py to include your apps

.. code-block:: python

    from apps_repo.apps.some_app.apps import app1
    from apps_repo.apps.some_app.apps import app2


- In your apps_repo/apps_repo, modify lsf.py to include a method that sets job submission parameters per app. This step is crucial or your job will be submitted to the short queue and killed after a set amount of time.

.. code-block:: python

    LSF_RUNTIME_3_DAYS = 4320
    LSF_RUNTIME_5_DAYS = 7200
    LSF_CONTROL_QUEUE = "-q control"

    def get_lsf_requirements(app, targets_methods):
        queue = LSF_CONTROL_QUEUE
        memory = 1
        cores = 1
        extra = None

        if isinstance(app, apps.app1):
            queue = LSF_CONTROL_QUEUE
            runtime = LSF_RUNTIME_5_DAYS
            cores = 2
            memory = 10

        if isinstance(app, apps.app2):
            queue = LSF_CONTROL_QUEUE
            runtime = LSF_RUNTIME_3_DAYS
            cores = 2
            memory = 10

        extra = extra or ""
        queue = queue or ""
        return " ".join(
            f"-n {cores} -W {runtime} -R 'rusage[mem={memory}]' {extra} {queue}".split()

- Modify your clients model to include the following configuration.


.. code-block:: JSON

  {
    "SUBMIT_ANALYSES": "isabl_cli.batch_systems.submit_lsf",
    "SUBMIT_CONFIGURATION": {
        "get_requirements": "apps_repo.lsf.get_lsf_requirements"
  }


**Note:** do not forget to export ``ISABL_CLIENT_ID=client id primary key`` for these settings to take effect.

Examples of kicking off multiple analyses at once:

.. code-block:: bash

    isabl some-assembly app1 -fi projects 1 # 1 in this example is your projects primary key



7. **View data** (end-user)



8. **Export data**



9. **Integrate apps**



10. **System maintenance**

    8.1. Periodic (monthly or quarterly) system maintenance involves testing upstream code updates from the following repos in staging and then applying the changes to production.

    https://github.com/isabl-io/msk.git => https://github.com/shahcompbio/isabl_shahlab.git

    https://github.com/isabl-io/api.git => https://github.com/shahcompbio/isabl_api.git

    https://github.com/isabl-io/cli.git => https://github.com/shahcompbio/isabl_cli.git

    This must be done in the development environment first to ensure there are no regressions. First merge changes from upstream into a branch on origin that is at the head of master (the commands below are set up to be in master itself and this should be changed in the next refinement of these steps), and then rebuild the docker image and run isabl_api unit tests. If there is no regression, proceed to the next step. Else, notify upstream team about regressions and repeat. Next, repeat the same steps for the cli after starting the api server to test against. If cli tests pass, push changes for both cli and api to origin branch on origin.

    Once changes have been pushed successfully to origin master for both api and cli, proceed to update production when no jobs are running against the server. Once update is complete, notify all users to update their cli to head of origin master.

    .. code-block:: bash

        $ git checkout master
        $ git fetch upstream master
        $ git merge upstream/master
        $ docker-compose build
        $ echo "isabl_api unit test command below"
        $ docker-compose run --rm --user root django pytest --ds example.settings --cov=isabl_api
        $ docker-compose down
        $ docker-compose up
        $ echo "isabl_cli unit test command below"
        $ cd ../isabl_cli
        $ git checkout master
        $ git fetch upstream master
        $ git merge upstream/master
        $ source venv/bin/activate
        $ python setup.py install
        $ py.test tests/ --cov=isabl_cli -s
        $ git push origin master
        $ cd ../isabl_api
        $ git push origin master

    8.2 In staging environment, check for changes made to upstream repos https://github.com/isabl-io/cookiecutter-api.git and https://github.com/isabl-io/msk.git and manually apply these changes to https://github.com/shahcompbio/isabl_shahlab.git. Note that while isabl_shahlab.git was generated by executing cookiecutter-api.git and it is a sibling of msk.git. It will be useful to also get the upstream team to merge changes from the root repo https://github.com/pydanny/cookiecutter-django.git occasiobnally into cookiecutter-api.git as there may be important security updates being made to this repo.

       TODO: In the future, it may be easier to deploy directly from the cookiecutter-django.git rather than maintaining the interemdiate repos.

    8.3. Copy production db into staging environment and run unit tests in staging environment to verify code and database integrity. If verification passed, merge branch into master.
    
    a) Bring database backup from production into staging 
    
    b) Pull upstream changes in master into staging
    
    c) Update db credentials in staging by applying prod credentials
    
    d) Rebuild django image only
    
    e) drop db, create db and import prod db
    
    f) restart container with logging and ensure migrations succeeeded
    
    g) test with admin ui to ensure data is viewable
    
    h) run unit tests
    
    i) update CLI
    
    j) test hello_world app integration
    

    8.4. Update production environment.

    .. code-block:: bash

        $ docker-compose -f production.yml down
        $ cd requirements/isabl_api
        $ git stash
        $ git pull origin master
        $ git stash pop
        $ cd ../..
        $ docker-compose -f production.yml build django
        $ docker-compose -f production.yml run --rm django python manage.py migrate
        $ docker-compose -f production.yml up -d

    8.5 Hot Fixes



11. **System monitoring**



12. **System metrics**



13. **Backup relational database**

Create backup
    
.. code-block:: bash
    
    $ docker-compose -f production.yml exec postgres backup
    
Verify by listing backups
    
.. code-block:: bash
        
    $ docker-compose -f production.yml exec postgres backups
    
Copy backup to host dir after getting container id
    
.. code-block:: bash
    
    $ docker ps
    
    $ docker cp <postgres_container_id>:/backups/<backup_file_name>.sql.gz /datadrive/backups
    
Backup is copied to /datadrive/backups
    
Add notes to /datadrive/backups/NOTES
    
scp to local store

.. code-block:: bash

    $ cd ~/dev/temp/isabl_prod_db_backups
    
    $ scp <user>@<server_ip>:/datadrive/backups/* . 
   
Reference: https://github.com/pydanny/cookiecutter-django/blob/master/docs/docker-postgres-backups.rst   


14. **Restore relational database**

Verify backups
    
.. code-block:: bash
        
    $ docker-compose -f production.yml exec postgres backups
    
Review notes in /datadrive/backups/NOTES to know which backup copy to use

Restore

.. code-block:: bash
        
    $ docker-compose -f production.yml down
    
    $ docker-compose -f production.yml up -d postgres
    
    $ docker-compose -f production.yml exec postgres restore <backup_file_name>.sql.gz
    
    $ docker-compose -f production.yml down
    
    $ docker-compose -f production.yml up -d
    
Reference: https://github.com/pydanny/cookiecutter-django/blob/master/docs/docker-postgres-backups.rst   
    
    


15. **Backup/Replicate/Archive data lake**



16. **Restore data lake**


17. **Verify if database and data lake are in synch**


18. **Mirror data lake to cloud**

19. **Generate PDF of schema**

This assumes OS is Mac.

.. code-block:: bash

    $ brew install graphviz
    $ cd <path to isabl_api>
    $ pip install -r requirements.txt
    $ pip install pygraphviz --install-option="--include-path=/usr/local/Cellar/graphviz/2.42.2/include/graphviz" --install-option="--library-path=/usr/local/Cellar/graphviz/2.42.2/lib/graphviz"
    python manage.py graph_models isabl_api -o docs/schema.pdf -e -d -E --pygraphviz -R -X CustomField,BaseSampleModel,TimeStampedModel,BaseModel,BaseSlugModel,Preferences,BaseBioModel,Submission,User