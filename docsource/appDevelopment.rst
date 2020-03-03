Isabl Application Development
==============================


Things to keep in mind when developing apps -

Data is imported into the experiments directory of the data lake. This directory has group read permissions (drwxr-x---). i.e. Only admins can import data into the data lake. Developers who write data importers should not attempt to alter the umask of the experiment directory to include group write permissions.

Applications can write results to the analysis directory that isabl creates for the application. Applications must only write to this directory.

By design, isabl enforces these file permissions

    .. code-block:: cfg

        /data-lake-root/ (not writable)
        /data-lake-root/analyses/ (not writable)
        /data-lake-root/analyses/00/ (not writable)
        /data-lake-root/analyses/00/01/ (this has to be writable by the group)

