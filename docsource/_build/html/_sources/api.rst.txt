API Access to Data
==================

Overview
--------

Calls to the isabl API is documented at https://docs.isabl.io/retrieve-data. This page provides additional information and examples that may be helpful for getting started with using the API.

The isabl API documentation can be accessed at https://isabl.shahlab.mskcc.org/api/v1/.

The API may be accessed via the isabl CLI. Instructions for installing the CLI are at https://shahlab.ca/isabl_api/installation.html
under the Development Deployment section and Production Deployment section. Follow CLI Production Deployment for accessing production data.


Isabl has a project centric database. The schema for the database is available at https://github.com/shahcompbio/isabl_api/blob/master/docs/_static/schema.pdf

In the simple context of "input->process->output", the Experiment object holds information about the inputs. The Application object holds information about the application, or process, that is to be executed, and the Analysis object holds information about the outputs, in addition to any runtime configuration that was used by the process.


A good starting point for accessing information for a full run is to look up the experiment object as this object has references to other relevant objects like the Project that the experiments belongs to, the Sample(s) on which the experiment was set up and the corresponding set of analyses that were generated from the Experiment.

Once the CLI has been set up, it may be used to make this simple call to an experiment object as shown below.


.. code-block:: python

    import isabl_cli as ii
    import pprint
    pp = pprint.PrettyPrinter(width=80, compact=True, indent=2)

    pp.pprint(ii.get_experiments(system_id='SHAH_H000005_T01_01_scWT01')[0].__dict__)


The code above prints out an experiment object for an experiment with the specified system_id. The print to console out is presented below. Notice that it contains information of related objects such as 'projects', 'results', 'sample' and 'individual' in addition to information about the input set that the experiment encapsulates as 'raw data'.


.. code-block:: bash

    Retrieving 1 from experiments API endpoint...
    { 'aliquot_id': 'Unspecified Aliquot',
      'bam_files': {},
      'center': { 'acronym': 'IGO',
                  'created': '2019-05-09T14:43:13.359533-04:00',
                  'created_by': 'jdoe',
                  'custom_fields': {},
                  'data': {},
                  'model_name': 'Center',
                  'modified': '2019-05-09T14:43:13.359558-04:00',
                  'name': 'MSKCC IGO',
                  'notes': '',
                  'pk': 1,
                  'slug': 'MSKCC IGO (IGO)',
                  'storage_url': None,
                  'storage_usage': 0,
                  'tags': [],
                  'uuid': 'c6fc7946-cade-4f09-9f36-82a662bf1ac0'},
      'created': '2019-05-09T16:06:48.568422-04:00',
      'created_by': 'jdoe',
      'custom_fields': { 'experiment_center_identifier': 'IGO',
                         'is_pdx': None,
                         'is_single_cell': True,
                         'read_length': None,
                         'read_type': None},
      'data': {},
      'identifier': 'UNKNOWN_23',
      'model_name': 'Experiment',
      'modified': '2019-08-17T02:28:15.182340-04:00',
      'notes': '',
      'pk': 23,
      'platform': { 'created': '2019-05-09T15:19:20.435716-04:00',
                    'created_by': 'jdoe',
                    'custom_fields': {},
                    'data': {},
                    'manufacturer': '10X',
                    'model_name': 'Platform',
                    'modified': '2019-05-09T15:19:20.435742-04:00',
                    'notes': 'TODO: update Platform System and Version once Jamie '
                             'gets exact specs.',
                    'pk': 1,
                    'slug': '10X-HISEQ-3',
                    'storage_url': None,
                    'storage_usage': 0,
                    'system': 'HISEQ',
                    'tags': [],
                    'uuid': '46c12331-0546-4af0-bcb7-009a5033d346',
                    'version': '3'},
      'projects': [ { 'analyst': None,
                      'coordinator': None,
                      'created': '2019-05-09T14:41:14.585604-04:00',
                      'created_by': 'jdoe',
                      'custom_fields': {},
                      'data': {},
                      'description': None,
                      'group': None,
                      'model_name': 'Project',
                      'modified': '2019-05-09T16:39:32.637903-04:00',
                      'notes': None,
                      'owner': None,
                      'pk': 1,
                      'principal_investigator': None,
                      'short_title': 'spectrum--',
                      'storage_url': '/isabl_data_lake/projects/1',
                      'storage_usage': 0,
                      'tags': [],
                      'title': 'SPECTRUM',
                      'uuid': '49e0dd4f-36ea-49a9-9aa9-066617e54f12'}],
      'raw_data': [ { 'file_data': {},
                      'file_type': 'FASTQ_I1',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L001_I1_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '394574965'},
                    { 'file_data': {},
                      'file_type': 'FASTQ_R2',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L001_R2_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '2956111208'},
                    { 'file_data': {},
                      'file_type': 'FASTQ_R1',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L001_R1_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '1289884414'},
                    { 'file_data': {},
                      'file_type': 'FASTQ_I1',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L002_I1_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '393042999'},
                    { 'file_data': {},
                      'file_type': 'FASTQ_R2',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L002_R2_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '2943172695'},
                    { 'file_data': {},
                      'file_type': 'FASTQ_R1',
                      'file_url': '/isabl_data_lake/experiments/00/23/23/data/SHAH_H000004_T01_01_scWT01_622abe966ebbd935_ROVCD45POS_IGO_09443_B_1_S1_L002_R1_001.fastq.gz',
                      'hash_method': 'os.path.getsize',
                      'hash_value': '1283130062'}],
      'results': [ { 'analyses': [],
                     'application': {'name': 'SCRNA', 'pk': 1, 'version': '1.0.3'},
                     'pk': 1,
                     'references': [],
                     'results': { 'cell_types': '/isabl_data_lake/analyses/00/01/1/report/cell_types.png',
                                  'command_err': '/isabl_data_lake/analyses/00/01/1/head_job.err',
                                  'command_log': '/isabl_data_lake/analyses/00/01/1/head_job.log',
                                  'command_script': '/isabl_data_lake/analyses/00/01/1/head_job.sh',
                                  'mito': '/isabl_data_lake/analyses/00/01/1/report/mito.png',
                                  'ribo': '/isabl_data_lake/analyses/00/01/1/report/ribo.png',
                                  'settings': '/isabl_data_lake/analyses/00/01/1/settings.yaml',
                                  'summary': '/isabl_data_lake/analyses/00/01/1/report/summary.html',
                                  'total_counts_v_features_by_counts': '/isabl_data_lake/analyses/00/01/1/report/total_counts_v_features_by_counts.png',
                                  'tsne_by_cell_type': '/isabl_data_lake/analyses/00/01/1/report/tsne_by_cell_type.png',
                                  'umap_by_cell_type': '/isabl_data_lake/analyses/00/01/1/report/umap_by_cell_type.png',
                                  'umi': '/isabl_data_lake/analyses/00/01/1/report/umi.png'},
                     'run_time': 39.5089701333333,
                     'status': 'SUCCEEDED',
                     'storage_url': '/isabl_data_lake/analyses/00/01/1',
                     'targets': [ { 'pk': 23,
                                    'system_id': 'SHAH_H000004_T01_01_scWT01'}],
                     'wait_time': None},
                   { 'analyses': [],
                     'application': { 'name': 'CELLRANGER',
                                      'pk': 3,
                                      'version': '3.0.2'},
                     'pk': 3,
                     'references': [],
                     'results': { 'command_err': '/isabl_data_lake/analyses/00/03/3/head_job.err',
                                  'command_log': '/isabl_data_lake/analyses/00/03/3/head_job.log',
                                  'command_script': '/isabl_data_lake/analyses/00/03/3/head_job.sh',
                                  'settings': '/isabl_data_lake/analyses/00/03/3/settings.yaml',
                                  'web_summary': '/isabl_data_lake/analyses/00/03/3/ROVCD45POS_IGO_09443_B_1/outs/web_summary.html'},
                     'run_time': 295.050850666667,
                     'status': 'SUCCEEDED',
                     'storage_url': '/isabl_data_lake/analyses/00/03/3',
                     'targets': [ { 'pk': 23,
                                    'system_id': 'SHAH_H000004_T01_01_scWT01'}],
                     'wait_time': None}],
      'sample': { 'category': 'TUMOR',
                  'collection_days': None,
                  'created': '2019-05-09T16:06:48.539831-04:00',
                  'created_by': 'jdoe',
                  'custom_fields': {},
                  'data': {},
                  'disease': { 'acronym': 'HGSOC',
                               'created': '2019-05-09T14:44:00.073157-04:00',
                               'created_by': 'jdoe',
                               'custom_fields': {},
                               'data': {},
                               'model_name': 'Disease',
                               'modified': '2019-05-09T14:44:00.073189-04:00',
                               'name': 'High Grade Serous Ovarian Cancer',
                               'notes': '',
                               'pk': 1,
                               'slug': 'High Grade Serous Ovarian Cancer (HGSOC)',
                               'storage_url': None,
                               'storage_usage': 0,
                               'tags': [],
                               'uuid': 'ec8a7ab4-e59a-45db-8e7d-1a28413d34aa'},
                  'identifier': 'ROVCD45POS_IGO_09443_B_1',
                  'individual': { 'birth_year': 1800,
                                  'center': { 'acronym': 'IGO',
                                              'created': '2019-05-09T14:43:13.359533-04:00',
                                              'created_by': 'jdoe',
                                              'custom_fields': {},
                                              'data': {},
                                              'model_name': 'Center',
                                              'modified': '2019-05-09T14:43:13.359558-04:00',
                                              'name': 'MSKCC IGO',
                                              'notes': '',
                                              'pk': 1,
                                              'slug': 'MSKCC IGO (IGO)',
                                              'storage_url': None,
                                              'storage_usage': 0,
                                              'tags': [],
                                              'uuid': 'c6fc7946-cade-4f09-9f36-82a662bf1ac0'},
                                  'created': '2019-05-09T16:06:48.533761-04:00',
                                  'created_by': 'jdoe',
                                  'custom_fields': {},
                                  'data': {},
                                  'gender': 'FEMALE',
                                  'identifier': 'SPECTRUM-OV-002',
                                  'model_name': 'Individual',
                                  'modified': '2019-10-22T17:46:54.958559-04:00',
                                  'notes': '',
                                  'pk': 4,
                                  'species': 'HUMAN',
                                  'storage_url': None,
                                  'storage_usage': 0,
                                  'submission': 1,
                                  'system_id': 'SHAH_H000004',
                                  'tags': [{'name': 'P-0039615', 'pk': 1}],
                                  'uuid': '211f902b-852c-4ae3-a8f0-1d50577bd39d'},
                  'model_name': 'Sample',
                  'modified': '2019-05-09T16:06:48.561310-04:00',
                  'notes': None,
                  'pk': 23,
                  'storage_url': None,
                  'storage_usage': 0,
                  'submission': 1,
                  'system_id': 'SHAH_H000004_T01',
                  'tags': [],
                  'uuid': '60c5c980-5289-4047-9a3f-a15871a0e3e9'},
      'storage_url': '/isabl_data_lake/experiments/00/23/23',
      'storage_usage': 9259916343,
      'submission': 1,
      'system_id': 'SHAH_H000004_T01_01_scWT01',
      'tags': [],
      'technique': { 'category': 'RNA',
                     'created': '2019-05-09T15:11:49.717123-04:00',
                     'created_by': 'jdoe',
                     'custom_fields': {},
                     'data': {},
                     'method': 'WT',
                     'model_name': 'Technique',
                     'modified': '2019-05-09T15:28:28.987206-04:00',
                     'name': 'Single Cell RNA Seq',
                     'notes': '',
                     'pk': 1,
                     'reference_data': {},
                     'slug': 'RNA|WT|Single Cell RNA Seq',
                     'storage_url': None,
                     'storage_usage': 0,
                     'tags': [],
                     'uuid': '85b5c983-9633-426e-9e70-a768cc6cac88'},
      'uuid': '415912f4-ad06-4dae-8816-ab5e0e273f6f'}


Access to scRNASeq Data
-----------------------

The following set of example code snippets may be used for extracting Single Cell scRNASeq data from isabl.


Initialization.


.. code-block:: python


    import isabl_cli as ii
    import pprint
    import os

    pp = pprint.PrettyPrinter(width=80, compact=True, indent=2)
    app_name      = 'SCRNA'
    app_version   = '<<< SOME VERSION, for example 1.0.3 >>>'
    path_template = ".cache/{0}/{0}.rdata"


Step 1: Identify the individual (patient) to get sample data for.


.. code-block:: python

    individuals = ii.get_instances('individuals')

    for individual in individuals:
       print(f"ID: {individual['identifier']}")


.. code-block:: bash

    ID: SPECTRUM-OV-002
    ID: SPECTRUM-OV-003
    ID: SPECTRUM-OV-007
    ID: SPECTRUM-OV-014
    ID: SPECTRUM-OV-008
    ...


Step 2: Get a list of samples for a specific individual (patient).


.. code-block:: python

    individual_id = '<<< SOME ID THAT YOU GOT IN PREVIOUS STEP >>>'

    samples_for_query = ''
    samples = ii.get_instances('samples', individual__identifier=individual_id)
    for sample in samples:
       samples_for_query += sample['identifier'] + ','

    pp.pprint(samples_for_query)


.. code-block:: bash

    Retrieving 4 from samples API endpoint...
    ROVCD45POS_IGO_09443_B_1,ROVCD45NEG_IGO_09443_B_2,OMENTCD45POS_IGO_09443_B_3,Sample_002

Step 3: Identify a list of experiment objects for the given samples.


.. code-block:: python

    experiments = ii.get_instances('experiments', sample__identifier__in=samples_for_query)
    pp.pprint(experiments)


.. code-block:: bash

    Retrieving 4 from experiments API endpoint...
    [ Experiment(SHAH_H000004_T01_01_scWT01),
      Experiment(SHAH_H000004_T02_01_scWT01),
      Experiment(SHAH_H000004_T03_01_scWT01), Experiment(SHAH_H000004_N01_01_WG01)]


Step 4: For each identified experiment, traverse results for pipeline runs that succeeded. Then construct file paths to retrieve the latest R data file path: {storage_url}/.cache/{sample_id}/{sample_id}.rdata


.. code-block:: python

    for experiment in experiments:
        for analysis in experiment['results']:
            if analysis['application']['name']             == app_name \
                    and analysis['application']['version'] == app_version \
                    and analysis['status']                 == 'SUCCEEDED':
               rdata = os.path.join(analysis['storage_url'], path_template.format(experiment['sample']['identifier']))
               pp.pprint(f"{analysis['pk']} - {analysis['application']['name']} - {analysis['application']['version']} - {rdata}")


.. code-block:: bash

    ('1 - SCRNA - 1.0.3 - '
     '.../analyses/00/01/1/.cache/ROVCD45POS_IGO_09443_B_1/ROVCD45POS_IGO_09443_B_1.rdata')
    ('60 - SCRNA - 1.0.3 - '
     '.../analyses/00/60/60/.cache/ROVCD45NEG_IGO_09443_B_2/ROVCD45NEG_IGO_09443_B_2.rdata')
    ('108 - SCRNA - 1.0.3 - '
     '/.../analyses/01/08/108/.cache/OMENTCD45POS_IGO_09443_B_3/OMENTCD45POS_IGO_09443_B_3.rdata')

Step 5: Get results paths to individual level analyses.

.. code-block:: python

    analyses = ii.get_instances("analyses", application__name="SCRNA Individual Application")

    for anal in analyses:
       if 'individual_level_analysis' in anal.__dict__.keys():
            pp.pprint(anal.__dict__['storage_url'])


.. code-block:: bash

    Retrieving 27 from analyses API endpoint...
    '/.../analyses/03/75/375'
    '/.../analyses/07/38/738'
    '/.../analyses/07/39/739'
     ...