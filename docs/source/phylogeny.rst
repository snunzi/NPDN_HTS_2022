Generate a Phylogeny
========================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/1FOGhGhxTwqkRySyjGdmwyI2E9F87STNfvlqQTXd6HGw

Introduction
^^^^^^^^^^^^

Phylogentic analysis can help elucidate pathogen origins and whether there were multiple versus single introduction events an the potential source. Here, we will build a phylogenetic tree including our newly assembled genome, and genomes publicly available on NCBI.

Annotate Genome
^^^^^^^^^^^^^^^^

The first step in building a phylogeny is annotating our newly assembled genome. Here we will use the program Prokka to identify features of interest in our genome.

First let's import a new history with our assembled genome, and other genomes we want to include in our phylogeny.

.. admonition:: Hands-On: Import history

	1. At the top of the screen click on ``Shared Data`` and select ``Pages``

	2. In the search field, search for ``NPDN`` and click on the page ``NPDN HTS 2022``

	3. Find the history for ``NPDN 2022 Data Day 1 Part II`` Select the green plus sign to import into your Galaxy environment.


.. admonition:: Hands-On: Genome Annotation

	1. At the top of the Tools panel (on the left), search for 'prokka' and click on it.

	2. Run Prokka with the following parameters (leave everything else unchanged):

		* contigs to annotate: ``CA_root_scaffolds``

		* Additional Outputs: ``Annotation in GFF3 Format`` and ``Statistics related to annotated features``

		.. image:: _static/prokka.png

	3. Click Execute.

	4. When prokka finishes running, rename the gff file produced by prokka.

		* Find the prokka output file ``Prokka on X: gff``, click on the pencil icon next to it to edit attributes

		* Rename to ``CA-root.gff3`` and click save.

Once prokka had finished, you should have the following files produced:

* The GFF file contains all of the information about the features annotated
* The .txt file contains a summary of the number of features annotated.


Take a look at the summary file (``Prokka on X: txt``) to see how many annotations were generated.

.. container:: toggle

    .. container:: header

        **How many coding sequences (CDS) were found in out assembled genome?**

    By looking at the summary .txt file, you should see we annotated ~1067 CDS.


Pan-Genome with Roary
^^^^^^^^^^^^^^^^^^^^^^
In order to build our phylogeny, we need to know which genes are shared and which are unique across the genomes we are interested in. For the sake of time, we will only be working with the 8 compete HLB genomes available on NCBI. We will use the program Roary, which takes annotation files (gff) and calculates the pan genome.

.. admonition:: Hands-On: Pan-Genome Analysis


	1. At the top of the Tools panel (on the left), search for 'roary' and click on it.

	2. Run Roary with the following parameters:

		* Individual gff files or a dataset collection: ``Individual``

		* Select all gff files in the history (Ctrl+click until all are highlighted blue)

		* Leave all other parameters as default.

	3. Click Execute.

	4. When the program completes running, download the result file ``Roary on X Gene Presence Absence`` to your computer and rename it to 'presence-absence.csv'

		* Click on the name of the file in the history menu, then click the disk icon to download.

		.. image:: _static/download.png

What does Roary do?

* converts coding sequences into protein sequences
* clustered these protein sequences by several methods
* further refines clusters into orthologous genes
* for each sample, determines if gene is present/absent: produces gene_presence_absence.csv
* uses this gene p/a information to build a tree, using FastTree: produces accessory_binary_genes.fa.newick
* overall, calculates number of genes that are shared, and unique: produces summary_statistics.txt
* aligns the core genes (if option used, as above) for downstream analyses

Click on the eye icon next to the results file ``Roary on X summary statistics`` to view statistics on number of shared genes.

.. container:: toggle

    .. container:: header

        **How many core genes were found among the genomes?**

    By looking at the summary statistics file, you should see 990 core genes found.

Infer phylogeny
^^^^^^^^^^^^^^^^^

Here, we will infer the phylogeny using core gene snps.

.. admonition:: Hands-On: Phylogeny Analysis


	1. At the top of the Tools panel (on the left), search for 'raxml' and select 'Phylogenetic reconstruction with RaXML'

	2. Parameters to set:

		* Source file with aligned sequences: ``Roary on X core gene alignment``

		* RAxML options to use: ``Full option list``

		* Multiple boostrap specification: ``By bootstrapping specification``

		* Random seed for rapid bootstrapping: Input any number ie. ``736``

		* Algorithm to execute: ``Rapid bootstrap and best ML tree search (a)``

	3. Press Execute.

	4. Download result file ``Bipartitions`` (This is tree file with bootstrap support values) and rename the file ``raxml.tree``

	Visualize Tree in itol
	""""""""""""""""""""""

	.. admonition:: Hands-On: Visualize Tree

	1. Open iTOL webpage: https://itol.embl.de/upload.cgi

	2. Choose file: ``raxml.tree`` and click upload.

	2. To display bootstrap support values: After tree is displayed, on the right side there is a control panel. Click ``Advanced`` and set:

		* Bootstraps/metadata: ``Display``

			* Under this bootstrap panel select ``Text``

		.. image:: _static/iotl-boot.png

	3. Delete Branches with low support values. One option is to delete branches with low support values (There is no agreed upon cutoff). Under Advanced on control panel:

		* Delete Branches Bootstrap < ``80``

	4. Click Export on the control panel to select you download format and save tree image.


Visualize Core and Pan Genome
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Here, we will use the web tool Phandango to visualize the phylogeny, as well as pan and core genomes of our analyzed genomes.

.. admonition:: Hands-On: Phylogeny Analysis

	1. Go to http://phandango.net

	2. Drag and drop the two results files we downloaded ('presence-absence.csv' and 'raxml.tree') onto the landing page.

	3. View the tree of samples and their core and pan genomes

		* Each blue colored column is a gene: genes are present or absent in each isolate

		* the core genes are shared by all isolates

You should notice that our newly assembled genome has a large portion of genes absent that are shared among the other other genomes. Remember, we are working with a draft genome so not all genes will be identified. We are likely missing some genes that are present, but did not assemble. This may impact our phylogeny, depending on what proportion is missing.

.. container:: toggle

    .. container:: header

        **Look at the Phandango plot and circle the pan and core genomes.**

    You should have circled all the shared genes as core, and the total of core and accessory genes as pan.

	.. image:: _static/phandango-pan-core.png
