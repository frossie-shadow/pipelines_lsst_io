######################################################
Testing the Science Pipelines Installation with a Demo
######################################################

This demo will allow you to quickly test your LSST Science Pipelines installation, :doc:`regardless of your installation method <index>`.

1. Activate the LSST Science Pipelines
======================================

Remember to first load the LSST Science Pipelines into your shell's environment.
The method depends on how the Science Pipelines were installed:

- :ref:`Conda <conda-install-activate>`
- :ref:`newinstall.sh <install-from-source-loadlsst>`
- :ref:`lsstsw <lsstsw-setup>`

2. Download the Demo Project
============================

Choose a directory to run the demo in.
We'll call this directory :file:`demo_data`.

.. code-block:: bash

   mkdir -p demo_data
   cd demo_data

Then download the demo's data (if you aren't running the current stable release, see the note below):

.. code-block:: bash

   curl -L https://github.com/lsst/lsst_dm_stack_demo/archive/13.0.tar.gz | tar xvzf -
   cd lsst_dm_stack_demo-13.0

.. note::

   The demo's version should match your installed software.
   If you installed from source (with :doc:`lsstsw <lsstsw>`), clone the demo repository instead of downloading a release:

   .. code-block:: bash

      git clone https://github.com/lsst/lsst_dm_stack_demo.git
      cd lsst_dm_stack_demo

The demo repository consumes roughly 41 MB, contains input images, reference data, and configuration files.
The demo script will process SDSS images from two fields in Stripe 82, as shown in the following table:

==== ====== ===== =========
run  camcol field filters
==== ====== ===== =========
4192 4      300   *(ur)giz*
6377 4      399   *(gz)uri*
==== ====== ===== =========

*Filters in parentheses are not processed if run with the* ``--small`` *option, see below*

3. Run the Demo
===============

Now setup the processing package and run the demo:

.. code-block:: bash

   setup obs_sdss
   ./bin/demo.sh # --small to process a subset of images

For each input image the script performs the following operations:

* generate a subset of basic image characterization (e.g., determine photometric zero-point, detect sources, and measures positions, shapes, brightness with a variety of techniques),
* creates a :command:`./output` subdirectory containing subdirectories of configuration files, processing metadata, calibrated images, FITS tables of detected sources. These "raw" outputs are readable by other parts of the LSST pipeline, and
* generates a master comparison catalog in the working directory from the band-specific source catalogs in the ``output/sci-results/`` subdirectories.

4. Check the Demo Results
=========================

The demo will take a minute or two to execute (depending upon your machine), and will generate a large number of status messages.
Upon successful completion, the top-level directory will contain an output ASCII table that can be compared to the expected results from a reference run.
This table is for convenience only, and would not ordinarily be produced by the production LSST pipelines.  

========================== ==================================
Demo Invocation            Demo Output               
========================== ==================================
:command:`demo.sh`         :file:`detected-sources.txt`
:command:`demo.sh --small` :file:`detected-sources_small.txt`
========================== ==================================

The demo output may not be identical to the reference output due to minor variation in numerical routines between operating systems (see :jira:`DM-1086` for details).
The :command:`bin/compare` script will check whether the output matches the reference to within expected tolerances:

.. code-block:: bash

   ./bin/compare detected-sources.txt

The script will print "``Ok``" if the demo ran correctly.

For more information about the processing done by the demo, refer to `its README on GitHub <https://github.com/lsst/lsst_dm_stack_demo>`_.
