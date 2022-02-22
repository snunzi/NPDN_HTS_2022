Group Questions
================

Here are some questions from the group. 

Generating Phylogeny with Bootstraps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Generating Tree in Galaxy
"""""""""""""""""""""""""

In the tutorial we generated a Maximum Likelihood tree without bootstrapping. Here is some information about how to generate a tree with RaxML. 

.. admonition:: Hands-On: Bootstrap Trees

	1. At the top of the Tools panel (on the left), search for 'raxml' and select 'Phylogenetic reconstruction with RaXML'
	
	2. Parameters to set:
	
		* Source file with aligned sequences: ``Roary on X core gene alignment`` 
		
		* RAxML options to use: ``Full option list``
		
		* Multiple boostrap specification: ``By bootstrapping specification``
		
		* Random seed for rapid bootstrapping: Input any number ie. ``736``
		
		* Algorithm to execute: ``Rapid bootstrap and best ML tree search (a)``

	3. Press Execute. 
	
	4. Download result file ``Bipartitions`` (This is tree file with bootstrap support values)

Visualize Tree in itol
""""""""""""""""""""""

.. admonition:: Hands-On: Visualize Tree

	1. Open iTOL webpage: https://itol.embl.de/upload.cgi
	
	2. Choose file: ``Bipartions file you downloaded from Galaxy`` and click upload.
	
	2. To display bootstrap support values: After tree is displayed, on the right side there is a control panel. Click ``Advanced`` and set:
	
		* Bootstraps/metadata: ``Display``
		
			* Under this bootstrap panel select ``Text``
		
		.. image:: _static/iotl-boot.png
	
	3. Delete Branches with low support values. One option is to delete branches with low support values (There is no agreed upon cutoff). Under Advanced on control panel:
		
		* Delete Branches Bootstrap < ``80`` 
		
	4. Click Export on the control panel to select you download format and save tree image.
	

Fastq from SRA
^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Download SRA Files as Fastq

	1. In Galaxy search for the tool 'Faster Download and Extract Reads in FASTQ' and click on it. 
	
	2. Run with the following parameters:
	
		* select input type: ``SRR accession``
		
		* Accession: Whatever your accession number is (ie. ``SRR11794480``)
	
	3. Click Execute.

