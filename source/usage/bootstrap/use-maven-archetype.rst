.. _more-about-maven-archetype:

More about Maven archetype
==========================

.. note:: This page give precisions about Maven archetype deployment and project generation.
  Quick tutorial with step by step usage is also available :ref:`here <quick-initialization>`.

To initialize a new project based on the basic-application, you have to follow several steps.
Each steps are detailed one by one in the following sections.


Build the archetype (optional)
------------------------------

.. note:: If archetype is available on Nexus repository, there is no need to build and publish it.

In the folder ``igloo-parent/basic-application``


.. code-block:: bash
   :caption: Install the archetype locally

   ./build-and-push-archetype.sh ../basic-application/ local


.. code-block:: bash
   :caption: Install the archetype on our repository

   ./build-and-push-archetype.sh ../basic-application/ snapshot


Generate a new project
----------------------

Place yourself in a new folder or somewhere like /tmp/. This command will
generate a new folder where you are containing your new project.


.. code-block:: bash
   :caption: Using you local repository

   mvn archetype:generate -DarchetypeVersion=X.X -DarchetypeCatalog=local -DartifactId=your-artifact-id -DgroupId=your.group.id -Dversion=0.1-SNAPSHOT -Dpackage=com.your.package -DarchetypeApplicationNamePrefix="YourApplication" -DarchetypeSpringAnnotationValuePrefix="yourApplication" -DarchetypeFullApplicationName="Customer - Your application" -DarchetypeDatabasePrefix=c_database_prefix -DarchetypeDataDirectory=your-data-directory


.. code-block:: bash
   :caption: Using the snapshot repository

   mvn archetype:generate -DarchetypeCatalog=https://nexus.tools.kobalt.fr/repository/igloo-snapshots/ -DartifactId=your-artifact-id -DgroupId=your.group.id -Dversion=0.1-SNAPSHOT -Dpackage=com.your.package -DarchetypeApplicationNamePrefix="YourApplication" -DarchetypeSpringAnnotationValuePrefix="yourApplication" -DarchetypeFullApplicationName="Customer - Your application" -DarchetypeDatabasePrefix=c_database_prefix -DarchetypeDataDirectory=your-data-directory


.. code-block:: bash
   :caption: Using the release repository

   mvn archetype:generate -DarchetypeCatalog=https://nexus.tools.kobalt.fr/repository/igloo-releases/ -DartifactId=your-artifact-id -DgroupId=your.group.id -Dversion=0.1-SNAPSHOT -Dpackage=com.your.package -DarchetypeApplicationNamePrefix="YourApplication" -DarchetypeSpringAnnotationValuePrefix="yourApplication" -DarchetypeFullApplicationName="Customer - Your application" -DarchetypeDatabasePrefix=c_database_prefix -DarchetypeDataDirectory=your-data-directory


Push the new project
--------------------

Go in the newly generate folder containing your project and push it on gitlab :

.. code-block:: bash

   /bin/bash init-git.sh <project_name> <git_url>
   git push -u origin master

.. note:: please check issue|ci management and scm urls in root ``pom.xml``

.. warning:: After having push your project, delete the project folder and initialize a
   new one directly from gitlab before starting your work.
