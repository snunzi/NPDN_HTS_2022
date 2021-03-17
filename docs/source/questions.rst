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

