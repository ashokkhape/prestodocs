.. presto documentation master file, created by
   sphinx-quickstart on Wed Sep  8 23:40:21 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

How to run presto using docker
==============================

.. toctree::
   :maxdepth: 2
   :caption: Contents:


===================
Aim of the document
===================

The document is aimed at helping Linux based users to install and run presto. 
Presently, the information is not comprehensive. This leads to loss of time in trying to figure out the gaps.

==============
What is Docker 
==============
Docker is an open platform for developing, shipping, and running applications. Docker enables the users to separate their applications from their infrastructure so one can deliver software quickly. With Docker, one can manage their infrastructure in the same ways they manage your applications.

===========================
Why run presto using docker
===========================

PRESTO calls into usage a lot of other functions defined in the algorithm (see INSTALL.md file). The dependencies are:

#. FFTW3.X
#. PGPLOT
#. TEMPO
#. GLIB
#. CFITSIO

Running PRESTO without Docker would require individual installation of each of the above-mentioned components. The tasks get challenging due to the differences in the Operating Systems, their inbuilt compilers etc.  Aware of these challenges, the developers allowed the flexibility to build the docker image using the Dockerfile.

===============
Steps to follow
===============

#. Install Docker Engine from the link below (in case it is not already installed).Visit https://docs.docker.com/engine/install/

#. Run Docker service on your machine. If you face any permission issues, run it as superuser using :command:`sudo` as a prefix.

#. Clone presto repository on your machine using the command below

   .. code-block:: shell

      git clone https://github.com/scottransom/presto.git

#. Before proceeding any further, ensure you are in the correct directory (the newly cloned repository). 
   If not, navigate to newly cloned repository using the command

   .. code-block:: shell

      cd <presto_repo_folder>/presto

#. Build the presto docker image using the below command

   .. code-block:: shell

      docker build -t presto-image .

   To validate the success of the above command, check whether the image has been built using

   .. code-block:: shell

      docker images

   The output on the console should have presto-image.

   .. note:: 
      In case you have used docker previously, multiple images will be returned. 
      Please ensure you select the correct image. Additionally you can validate the image 
      by checking the date and time the image was created.

#. For plotting the graphs, as shown in the user manual (refer to commands :command:`exploredat` 
   and :command:`explorefft` in the user manual [1]), PGPLOT is required. Docker containers do not 
   support GUI apps; hence, you need to allow the docker container to use xserver on the host machine. 
   This can be done using the command below:

   .. code-block:: shell

      xhost +local:root

#. The following step achieves: 
   
   .. note:: This is one single command.

   * Running the docker image.
   * Creating a folder in the local host to map with the docker folder mnt. You can choose any existing 
     folder but it is better to create a new folder for this purpose and map it accordingly by replacing 
     :file:`{folder_to_be_mapped}` with its path.
   * This mapped folder in the local host will contain the output files generated.
   * The folder that is being mapped to the docker mnt folder should contain the dataset which will be operated upon.

   .. code-block:: shell

      docker run -it -d -v <folder_to_be_mapped>:/mnt -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$(DISPLAY) -h $(HOSTNAME) --name=presto presto-image

#. Verify the status of this container using the command below. The status should be “UP”. 
   In case the status is not “UP” please explore the error messages to understand further.

   .. code-block:: shell

      docker ps

#. Once the status is “UP” you can connect to this container using a secure shell (ssh).

   .. code-block:: shell

      docker exec -it presto /bin/bash

#. Change the current directory to :file:`/mnt`  as this folder in the docker container is mapped to 
   the host machine folder containing the dataset and the output files.

   .. code-block:: shell

      cd /mnt

#. Now all the presto commands can be used and outputs can be observed in volume 
   :file:`{folder_to_be_mapped}`  folder (in step step 7).

#. Once done, you can exit the docker container using the command below

   .. code-block:: shell

      exit

#. It is advisable to kill the container if it is no longer needed immediately. 
   Not killing the container might create unwanted issues when you try to start the container again.
   
   .. code-block:: shell

      docker kill presto

==========
References
==========

#. https://www.cv.nrao.edu/~sransom/PRESTO_search_tutorial.pdf
#. Please refer to docker documentation for additional `docker commands`_.
#. https://github.com/scottransom/presto

.. _docker commands: https://docs.docker.com/engine/reference/commandline/docker/


