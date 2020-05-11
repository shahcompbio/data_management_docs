Installation Instructions
=========================

All installation instructions are for Ubuntu 16.04.

Server Development Deployment (API)
-----------------------------------

You may wish to make a local server deployment if you are interested in developing and testing isabl apps.

1. Set up the following dependencies:

| a) python 3.6+ (http://ubuntuhandbook.org/index.php/2017/07/install-python-3-6-1-in-ubuntu-16-04-lts/),
| (https://developers.redhat.com/blog/2018/08/13/install-python3-rhel/)
|
| b) pip and virtual env (https://packaging.python.org/guides/installing-using-pip-and-virtualenv/)
|
| c) Docker (https://docs.docker.com/install/linux/docker-ce/ubuntu/)
|
| d) Docker-compose (https://docs.docker.com/compose/install/)
|
| e) apt-get install git  (or yum install git)


2. Make a local storage directory for raw files.


.. code-block:: bash

    $ mkdir -p ~/isabl_storage

3. Clone repo.

.. code-block:: bash

    $ [cd to a dir where the api is to be installed]
    $ git clone https://github.com/shahcompbio/isabl_api.git
    $ git remote -v
    $ git remote add upstream git@github.com:isabl-io/api.git
    $ git remote -v

4. In docker-compose.yml, the volume for the django service should include path to data lake which for consistency should be the same as what clients will set as BASE_STORAGE_DIRECTORY in isabl_cli/settings.py. By default, this points to `~/isabl_storage`. Note: use an absolute path (and replace USER_NAME below with your username).

For postgres, it is necessary to bind mount the host backups dir so backups can be easily accessed from outside of the container.

.. code-block:: cfg

          services:
            django:
              volumes:
                - .:/app
                - /Users/<USER_NAME>/isabl_storage:/Users/<USER_NAME>/isabl_storage

          services:
            postgres:
              volumes:
                - local_postgres_data:/datadrive/db
                - ./backups:/backups

5. For mac, change `psycopg2-binary==2.8.4` to `psycopg2-binary==2.8.5` ./setup.json


6. Update template UI by editing `./example/templates/frontend.html` and replacing the script tags the the tags below.

.. code-block:: python

    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script src="https://cdn.jsdelivr.net/npm/isabl-web@0.1.75"></script>


7. If you are deploying a dev server on a machine other than your laptop (localhost), add the IP or domain name of the hosting machine to ALLOWED_HOSTS in `./example/settings.py`.


.. code-block:: python

    # replace
    ALLOWED_HOSTS = ["localhost", "0.0.0.0", "127.0.0.1"]
    # with
    ALLOWED_HOSTS = ["1.2.3.4", "my_vm_domain_name"]


Also replace "localhost in the following files and locations

 .. code-block:: bash

    $ grep -R "localhost" .

    ./example/views.py:            settings, "DJANGO_HOST", "localhost:8000"
    ./example/settings.py:EMAIL_HOST = "localhost"
    ./example/settings.py:    DJANGO_HOST = "localhost:" + PORT
    ./example/settings.py:    DJANGO_HOST = "localhost:8000"
    ./isabl_api/integrations/drive.py:        auth_host_name="localhost",

8. Build images and start server.

.. code-block:: bash

    $ docker-compose build
    $ docker images
    $ docker-compose run --rm django python manage.py makemigrations
    $ docker-compose run django python manage.py migrate
    $ docker-compose run django python manage.py showmigrations
    $ docker-compose run --rm django python manage.py createsuperuser --email admin@shahlab.mskcc.org --username admin
    $ docker-compose run --rm --user root django pytest --ds example.settings --cov=isabl_api
    $ docker-compose up


9. Open browser and log in to the admin UI at http://localhost:8000/admin/.


.. note:: The trailing slash after `admin` is required.


10. In admin UI,

| a) navigate to the Clients section and create a new client object with no attributes. A client with id = 1 should be created if it does not already exist. This id is required by the isabl cli to distinguish itself from other isabl clients.

| b) navigate to the Center section and add a new Center “MSKCC IGO” with __IGO__ as acronym

| c) navigate to the Project section and add a new project “SPECTRUM TEST” (name doesn’t matter)


11. See "Staging Deployment" section to learn how to seed the database with a copy of the production database.


Client Development Deployment (CLI)
-----------------------------------

1. Clone isabl_cli and shahlab_apps repos.


.. code-block:: bash

    $ cd <to a dir where cli and apps should be installed>
    $ git clone https://github.com/shahcompbio/isabl_cli.git
    $ git clone https://github.com/shahcompbio/shahlab_apps.git
    $ virtualenv -p python3 venv
    $ source venv/bin/activate
 
2. change `base_url` in `isabl_cli/api.py` to point to server url.

Replace,


.. code-block:: python


          base_url = environ.get("ISABL_API_URL", "http://0.0.0.0:8000/api/v1/")


with,

.. code-block:: python


          base_url = environ.get("ISABL_API_URL", "http://localhost:8000/api/v1/")


OR you may set an environment variable in your shell.


.. code-block:: bash

    $ export ISABL_API_URL="http://localhost:8000/api/v1/"


3.  In isabl_cli/settings.py,

replace,


.. code-block:: python


          client_id = os.environ.get("ISABL_CLIENT_ID")


with,


.. code-block:: python


          client_id = os.environ.get("ISABL_CLIENT_ID")
          client_id = 1


OR, you may set an environment variable in your shell.


.. code-block:: bash

    $ export ISABL_CLIENT_ID="1"


4. Note that the BASE_STORAGE_DIRECTORY in isabl_cli/settings.py points to ~/isabl_storage by default. This is where your raw files will be stored by isabl.


5. Install dependencies. Notice apps and cli use the same virtual environment.


.. code-block:: bash

    $ cd isabl_cli
    $ python setup.py install
    $ cd ../shahlab_apps
    $ python setup.py install

    
6. Create file `~/.isabl/settings.json` and add the following contents


.. code-block:: cfg

          {
            "api_token": "GET_TOKEN_FROM_ISABL_ADMIN_UI_TOKENS_SECTION",
            "token": null
          }



7. In isabl_cli/settings.py update _DEFAULTS map to include applications that have been registered on the server. For example,


.. code-block:: python

          "INSTALLED_APPLICATIONS": [
            "shahlab_apps.apps.hello_world.apps.HelloWorld",
            "shahlab_apps.apps.scrna.apps.SCRNA",],
            

8. Running `isabl` now should bring up the help menu with apps-grch37 and apps-grch38 applications. This confirms that isabl_cli is able to communicate with isabl_api.


.. code-block:: text

    $ isabl
          Usage: isabl [OPTIONS] COMMAND [ARGS]...

            Run Isabl command line tools.

          Options:
            --version  Show the version and exit.
            --help     Show this message and exit.

          Commands:
            apps-grch37              GRCh37 applications.
            apps-grch38              GRCh38 applications.
            get-bams                 Get storage directories, use `pattern` to match...
            get-bed                  Get a BED file for a given Sequencing Tehcnique.
            get-count                Get count of database instances.
            get-data                 Get file paths for experiments sequencing data.
            get-metadata             Retrieve metadata for multiple instances.
            get-outdirs              Get analyses outdirs, use `pattern` to match...
            get-paths                Get storage directories, use `pattern` to match...
            get-reference            Get reference resource for an Assembly.
            get-results              Get analyses results.
            import-bedfiles          Register targets and baits bed_files in...
            import-data              Find and import experiments data from many...
            import-reference-data    Register reference data in the assembly's data...
            import-reference-genome  Register an assembly reference genome.
            login                    Login with isabl credentials.
            merge-project-analyses   Merge analyses by project primary key.
            patch-results            Update the results field of many analyses.
            process-finished         Process and update finished analyses.


9. To execute unit tests, you need to have a local server running.


.. note:: DO NOT RUN UNIT TESTS AGAINST THE PRODUCTION SERVER!


.. code-block:: bash

    $ cd ../cli
    $ py.test tests/ --cov=isabl_cli -s
    $ cd ../shahlab_apps
    $ py.test tests/ --cov=isabl_shahlab_apps -s

            
10. To check the applications under each genome reference, type


.. code-block:: bash

    $ isabl apps-grch37
          Usage: isabl apps-grch37 [OPTIONS] COMMAND [ARGS]...

            GRCh37 applications.

          Options:
            --help  Show this message and exit.

          Commands:
            helloworld-0.1.1.r1  A hello world app that demonstrates component level...
            
This completes installation. To run an app, see the section on  `Routine Operations <ops.rst>`__.


.. code-block:: bash

    $ deactivate



Staging Deployment
------------------

The staging environment is used to,

| 1. verify compatibility of the application code with the existing database, and to
|
| 2. verify compatibility of the application code with the production environment.

The staging environment must be set up in exactly the same way as the production environment to ensure code compatibility with the production environment. The only exception to this is that unit tests must be executed in the staging environment for verification of code and database compatibility. See Development deployment for unit test execution.

The following steps may be used to deploy the production database to a staging environment.

| 1. Turn off tls in compose/production/caddy/Caddyfile so http can be used instead of https

.. code-block:: bash

    tls off

.. code-block:: bash

    $ docker-compose -f production.yml build caddy

| 2. Set up postgres and import production dump

.. code-block:: bash

    $ docker-compose up -d postgres
    $ docker-compose exec postgres bash
    $ su - postgres

| 3. Export environment variables from production

.. code-block:: bash

    $ export POSTGRES_HOST=<FROM_PROD>
    $ export POSTGRES_PORT=<FROM_PROD>
    $ export POSTGRES_DB=<FROM_PROD>
    $ export POSTGRES_USER=<FROM_PROD>
    $ export POSTGRES_PASSWORD=<FROM_PROD>

| 4. Use command below to create a `root` user with superuser privileges and a $POSTGRES_USER from prod db with no extra privileges

.. code-block:: bash

    $ createuser --interactive --pwprompt

| 5. Then create db with prod db name

.. code-block:: bash

    $ createdb -O $POSTGRES_USER $POSTGRES_DB

| 6. Verify roles and new database

.. code-block:: bash

    $ psql
    postgres# \du  # to list roles
    postgres# \l   # to list database
    postgres# \q

| 7. Load production db dump

.. code-block:: bash

    $ gunzip -c /backups/<file_name> | psql $POSTGRES_DB

| 8. Verify load

.. code-block:: bash

    $ psql
    postgres# \c isabl_shahcompbio;
    postgres# select * from auth_user;
    postgres# \q

| 9. Put prod credentials in .envs/.production/.postgres

| 10. Start server and verify database contents through admin UI

.. code-block:: bash

    $ docker-compose down
    $ docker-compose -f production.yml up -d


Server Production Deployment (API)
----------------------------------

1. Create a VM with minumum 2 CPUs, 8 GB memory, 30 DB OS drive and a 255GB external data drive.


2. Confgure the VM to use a static IP.


3. Attach and mount data disk as `/datadrive`.


4. Do an OS update. Ensure only root can rwx, others can only r.


.. code-block:: bash

    $ sudo apt-get update
    $ sudo apt-get upgrade
    $ sudo chmod -R 774 /datadrive
    $ ssh-keygen -t rsa -b 4096 -C "<username>@github"
    $ echo "place public key in github, then test with ssh -T"
    $ ssh -T git@github.com


5. Install dependencies (see Step 1. in development deployment)


6. Set up a new environment. In order to set up an environment from first principles, the cookiecutter app should be installed with `` pip install cookiecutter `` and `` cookiecutter https://github.com/isabl-io/cookiecutter-api `` after setting up virtualenv. But since this app requires some post configuration changes to be made to it, these changes have been made and committed to a shahcompbio repo. So an easier way to set up is to simply clone this repo as shown below.


.. code-block:: bash

    $ cd /datadrive
    $ docker system prune -a
    $ docker system prune --volumes -f
    $ git clone https://github.com/shahcompbio/isabl_shahlab.git
    $ cd isabl_shahlab


7. In production.yml, change volume mappings for django and postgres.

The volume for the django service must include path to data lake which for consistency should be the same as what clients will set as BASE_STORAGE_DIRECTORY in isabl_cli/settings.py.

For postgres, it is necessary to bind mount the host backups dir so backups can be easily accessed from outside of the container.


.. code-block:: cfg

          services:
            django:
              volumes:
                 - /work/shah/isabl_data_lake:/work/shah/isabl_data_lake
                 - /logs:/logs


            postgres:
              volumes:
                - production_postgres_data:/var/lib/postgresql/data
                - /backups:/backups

8. Set up a sshfs mount to the cluster in order for the web application to view the results file using a non-root account.

Note: Verify that you can first log into the cluster where the data lake resides.

Note: Verify that the mount is viewable via user account that's running isabl_api webapp and via the docker container ``docker-compose -f production.yml django ash``.

Note: If the sshfs remote mount command does not work use argument ``-o debug,sshfs_debug,loglevel=debug`` to debug


.. code-block:: bash

        $ sudo mkdir <data_lake_path>
        $ chown -R <isabl_user>:<isabl_user> <data_lake_path>
        $ yum install fuse-sshfs
        OR
        $ sudo apt-get update
        $ sudo apt-get install sshfs
        $
        $ echo "command below from non-root account will mount the remote mount"
        $ sshfs -o nonempty -o follow_symlinks -o IdentityFile="<path_to_your_private_key>" -o allow_other <user>@<remote_server>:<path_to_datalake> <path_to_datalake>
        $
        $ echo "command below from non-root account will unmount the remote mount"
        $ fusermount -u <data_lake_path>

.. code-block: bash



9. Install isabl API.


.. code-block:: bash

    $ git submodule init
    $ rm -rf requirements/isabl_api
    $ git submodule add git@github.com:shahcompbio/api.git requirements/isabl_api
    $ git submodule update --init --recursive
    $ cd requirements/isabl_api
    $ git pull origin master


10. Add “SHAH” as SYSTEM_ID_PREFIX in requirements/isabl_api/isabl_api/settings.py. The SYSTEM_ID_PREFIX will be added to all project system ids and will differentiate shahlab system ids from other lab system ids in the global scope.


11. Generate TLS certificates.

Create a config file for the CSR called req.conf with the following fields. Edit only the fields whose values are encased in `<>`. For CN, provide an A NAME, also called a glue record. But a C NAME should also work. For <alternate_server_name> provide C NAMES, if any. Note that this configuration includes "Subject Alternate Name" or SAN. This is a required field for Chrome and Firefox browsers.


.. code-block:: cfg

                [req]
		distinguished_name = req_distinguished_name
		req_extensions = v3_req
		prompt = no
		[req_distinguished_name]
		C = US
		ST = NY
		L = NewYork
		O = MSKCC
		OU = ComputationalOncology
		CN = <server_name>
		[v3_req]
		keyUsage = keyEncipherment, dataEncipherment
		extendedKeyUsage = serverAuth
		subjectAltName = @alt_names
		[alt_names]
		DNS.1 = <alternate_server_name>
		DNS.2 = <alternate_server_name>
		DNS.3 = <alternate_server_name>
		DNS.4 = <alternate_server_name>

Generate the CSR using the configuration above on the server. Make sure to replace <server_name> in the command below. The result is the .csr (certificate signing request file) and .key (private key file).

.. code-block:: bash

    $ openssl req -new -out <server_name>.csr -newkey rsa:2048 -nodes -sha256 -keyout <server_name>.key.temp -config req.conf

Use the csr file to get a valid certificate file, in PEM format, from a Certificate Authority. At MSKCC, make a request to IT using the MyITApp website and explicitly request a certificate for nginx.

Once the certificate is received, verify that it is valid by running the command below. The output should contain fields from req.conf above.

.. code-block:: bash

    $ openssl x509 -in <server_name>.pem -text -noout

Check the MD5 hashes of the <server_name>.csr, <server_name>.pem and <server_name>.key files to make sure they match.


.. code-block:: bash

    $ openssl x509 -noout -modulus -in <server_name>.pem | openssl md5
    $ openssl rsa -noout -modulus -in <server_name>.key | openssl md5
    $ openssl req -noout -modulus -in <server_name>.csr | openssl md5


12. Add cert copy commands to compose/production/caddy/Dockerfile


.. code-block:: cfg

    cp ./compose/production/caddy/<server_name>.pem /etc/<server_name>.pem
    cp ./compose/production/caddy/<server_name>.key /etc/<server_name>.key


13. Copy TLS certificate paths in the docker-container into caddy config file ./compose/production/caddyfile.


.. code-block:: cfg

            http://isabl.shahlab.mskcc.org {
              redir https://{host}{uri}
            }

            {$DOMAIN_NAME} {
                root /var/www/html

                proxy / django:5000 {
                    transparent
                    except /media
                }

                browse
                log stdout
                errors stdout
                gzip
                tls /etc/<server_name>.pem /etc/<server_name>.key
            }


14. In .envs/.production/.caddy add `https://` to DOMAIN_NAME. This is required to explicitly tell caddy to use https and 80/443 ports.


15. Apply the mailgun integration. This is required for the proper functioning of the web application. See section on configuring the stack at https://cookiecutter-django.readthedocs.io/en/latest/deployment-with-docker.html.

|    15.1 Disable MAILGUN config
|    15.2 grep for and comment out all "send_mail" method calls

OR

|    15.1 Set up a mailgun account.
|    15.2 Create a domain.
|    15.2 Then copy-paste, domain and key for that domain into MAILGUN_DOMAIN and MAILGUN_API_KEY in .envs/.production/.django.
|    15.3 For that domain, add an email recipient.
|    15.4 Restart server. Once set up, the recipient will receive crash report emails each time django crashes
|    [TODO: add test for testing crash reports]


16. In isabl_shahlab/templates/frontend.html, hardcode version number of frontend. The version can be obtained from https://github.com/isabl-io/web/blob/master/package.json


.. code-block:: cfg

     <script src="https://cdn.jsdelivr.net/npm/vue"></script>
     <script src="https://cdn.jsdelivr.net/npm/isabl-web@0.1.12"></script>

16. Configure server [TODO].

.envs/.production/.django
.envs/.production/.postgres
.envs/.production/.caddy



17. Build the application and verify migrations.


.. note:: If password authentication fails for postgres, use ‘docker system prune --volumes -f’ and try this step again.


.. code-block:: bash

    $ docker-compose -f production.yml build
    $ docker images
    $ docker-compose  run --rm django python manage.py collectstatic
    $ docker-compose -f production.yml run --rm django python manage.py check
    $ docker-compose -f production.yml run --rm django python manage.py migrate
    $ docker-compose -f production.yml run --rm django python manage.py showmigrations
    $ docker-compose -f production.yml run --rm django python manage.py createsuperuser
    $ docker-compose -f production.yml down
    $ docker-compose -f production.yml up

18. Set up default user groups using the command below. Use the admin UI to add additional view access for the Analysts group to the following models.

CustomField, Individual, Center, Disease, Experiment, Technique, Platform, Project, Submission, Analysis, Application, Assembly

.. code-block:: bash

    $ docker-compose -f production.yml run --rm django python manage.py create_default_groups


.. note:: Other useful commands are,

    $ docker-compose -f production.yml up -d   # to start in detached mode

    $ docker-compose logs -f -t  # to attach to logs
      (detach with ctrl+c)

    $ docker-compose -f production.yml run --rm django python manage.py shell   # to start a django shell

    $ docker-compose -f production.yml scale django=4   # to scale django

    $ docker-compose -f production.yml scale celeryworker=2  # to scale celery workers


Client Production Deployment (CLI)
----------------------------------

1. Clone isabl_cli and shahlab_apps repos. If you plan to execute isabl apps, you will need to install the CLI on Juno,  (where the data lake resides). Or, you may wish to remote mount the juno data lake directory if you wish to work with the CLI on your local machine.


.. code-block:: bash

    $ cd <to a dir where cli and apps should be installed>
    $ git clone https://github.com/shahcompbio/shahlab_apps.git
    $ git clone https://github.com/shahcompbio/isabl_cli.git
    $ virtualenv -p python3 venv
    $ source venv/bin/activate


2. Install dependencies. Notice apps and cli use the same virtual environment.


.. code-block:: bash

    $ cd isabl_cli
    $ python setup.py install
    $ cd ../shahlab_apps
    $ python setup.py install


2. In isabl_cli/api.py,

replace,


.. code-block:: python


          base_url = environ.get("ISABL_API_URL", "http://0.0.0.0:8000/api/v1/")


with,


.. code-block:: python


          base_url = environ.get("ISABL_API_URL", "https://isabl.shahlab.mskcc.org/api/v1/")

OR, you may set an environment variable in your shell.


.. code-block:: bash

    $ export ISABL_API_URL="https://isabl.shahlab.mskcc.org/api/v1/"


3. In isabl_cli/settings.py,

replace,


.. code-block:: python


          client_id = os.environ.get("ISABL_CLIENT_ID")


with,


.. code-block:: python


          client_id = os.environ.get("ISABL_CLIENT_ID")
          client_id = 1


OR you may set an environment variable in your shell.


.. code-block:: bash

    $ export ISABL_CLIENT_ID="1"


4. In isabl_cli/settings.py update _DEFAULTS map to point to the location of the data lake. The default location is `~/isabl_storage`. This is the location of the data lake on juno.


.. code-block:: python

    "BASE_STORAGE_DIRECTORY": "/work/shah/isabl_datalake",



5. Re-install CLI if you made code edits in steps 2-4.


.. code-block:: bash


          (venv)$ python setup.py install



6. Create file `~/.isabl/settings.json` and add the following contents


.. code-block:: cfg

          {
            "api_token": "GET_TOKEN_FROM_ISABL_ADMIN",
            "token": null
          }


7. In isabl_cli/settings.py update _DEFAULTS map to include applications that have been registered on the server. For example,


.. code-block:: python

          "INSTALLED_APPLICATIONS": [
            "shahlab_apps.apps.cellranger.apps.CELLRANGER",
            "shahlab_apps.apps.scrna.apps.SCRNA",
            "shahlab_apps.apps.kallisto.apps.KALLISTO",
            "shahlab_apps.apps.cellphonedb.apps.CELLPHONEDB",],



6. Running `isabl` now should bring up the help menu with apps-grch37 and apps-grch38 applications. This confirms that isabl_cli is able to communicate with isabl_api.


.. code-block:: text

    $ isabl
          Usage: isabl [OPTIONS] COMMAND [ARGS]...

            Run Isabl command line tools.

          Options:
            --version  Show the version and exit.
            --help     Show this message and exit.

          Commands:
            apps-grch37              GRCh37 applications.
            apps-grch38              GRCh38 applications.
            get-bams                 Get storage directories, use `pattern` to match...
            get-bed                  Get a BED file for a given Sequencing Tehcnique.
            get-count                Get count of database instances.
            get-data                 Get file paths for experiments sequencing data.
            get-metadata             Retrieve metadata for multiple instances.
            get-outdirs              Get analyses outdirs, use `pattern` to match...
            get-paths                Get storage directories, use `pattern` to match...
            get-reference            Get reference resource for an Assembly.
            get-results              Get analyses results.
            import-bedfiles          Register targets and baits bed_files in...
            import-data              Find and import experiments data from many...
            import-reference-data    Register reference data in the assembly's data...
            import-reference-genome  Register an assembly reference genome.
            login                    Login with isabl credentials.
            merge-project-analyses   Merge analyses by project primary key.
            patch-results            Update the results field of many analyses.
            process-finished         Process and update finished analyses.