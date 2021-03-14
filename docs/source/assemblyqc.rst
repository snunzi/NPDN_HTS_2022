Assess Assembly Quality
========================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/1y0qND-SfwQUltUH7rWfdyS8B52HDXT7kNYUOTn6taDg

Introduction
^^^^^^^^^^^^

Not all genome assemblies are good. In order to see what the quality of our assembly is, lets assess.

Assembly Statistics
^^^^^^^^^^^^^^^^^^^^
First, we would like to view basic statistics about our assembly. We will use the program 'Quast' for this which produces nice summary statistics and allows us to compare our new assembly to a reference assembly. 

.. admonition:: Hands-On: Genome QC Statistics

	1. At the top of the Tools panel (on the left), search for 'Quast' and click on it.

	2. Run Quast with the following parameters:

		* Contigs/scaffolds file: ``CA-Root_S1_scaffolds.fasta``

		* Use a reference genome? ``Yes``

		* Reference genome: ``psy62.fna.gz``

	3. Leave all else as default and click 'Execute'

You should see multiple output file produced. Let's look at the HTML report, ``Quast on X: HTML report`` and gather some key information (click on eye icon next to this file). 

-------------------------

.. container:: toggle

    .. container:: header

        **This assemble covers how much of the psy62 reference genome?**
		
    Our assembled contigs cover ~98% of the psy62 genome.



.. container:: toggle

    .. container:: header

        **What is the N50?(You may have to click 'Extended report' on the html to see this statistic)**
		
    The N50 should be ~158,000.


.. container:: toggle

    .. container:: header

        **How many contigs were generated?**
		
    There are 62 total contigs.

----------------------------

Percent Identity
^^^^^^^^^^^^^^^^^

If you look at the Quast output, you'll see some of the contigs in our assembly were completely unaligned to the psy62 genome. Are these mis assemblies, or contamination? Let's use a more precise aligner and look at the percent identity between our assembly and the psy62. 

.. admonition:: Hands-On: Genome Comparison

	1. At the top of the Tools panel (on the left), search for 'dnadiff' and click on it.

	2. Run the tool with following parameters:

		* Reference sequence: ``psy62.fna.gz`` (Use drop down arrow)

		* Query Sequence: ``CA-Root_S1_scaffolds.fasta``
	
	3. Click Execute.
	
View the results file by clicking on the eye icon next to the ``DNAdiff on X: report`` in the history panel. 

-------------------------

.. container:: toggle

    .. container:: header

        **Are all contigs now aligned, or are some unaligned?**

    All contigs should now be aligned. If some contigs still were not aligned we would need to Blast the unaligned contigs to make sure they were not contamination.

	
.. container:: toggle

    .. container:: header

        **What is the percent identity of our genome assembly?**

    Percent identity is ~99%. Two different scores are presented, 1-to-1 and M-to-M, 1-to-1 keeps alignments that were marked best on BOTH sequences (ref and query), while M-to-M is keeps alignments that are marked best on either (ie. will report multiple alignments if there was a duplication event). Both measures are appropriate and used/reported. 

----------------------------

Genome Length Filtering
^^^^^^^^^^^^^^^^^^^^^^^^

You should see by the genome statistics that many contigs we generated are quite short, under 500bp. Because our read length was only 300bp, this means that the contig comes from only 1-2 reads. These contigs are not very reliable and likely introduce some sequencing error and potential contamination into our assembly. Let's filter them out so they are not used in our further analysis. 

.. admonition:: Hands-On: Contig Filtering

    1. At the top of the Tools panel (on the left), search for 'filter sequences by length' and click on it.

    2. Run this tool with following parameters:
		
	* Fasta file: ``CA-Root_S1_scaffolds.fasta``

        * Minimal length: ``500``

        * Maximum length: ``0``

Gene Completeness
^^^^^^^^^^^^^^^^^^

While the statistics above will tell us very obvious problems with the genome assembly, it gives no information about the quality of the sequence content. We want to evaluate if the genome sequence contains all the genes we expect to find in the considered species, and if their sequence are correct.

BUSCO (Benchmarking Universal Single-Copy Orthologs) is a tool allowing to answer this question: by comparing genomes from various more or less related species, the authors determined sets of ortholog genes that are present in single copy in (almost) all the species of a clade (Bacteria, Fungi, Plants, Insects, Mammalians, …). Most of these genes are essential for the organism to live, and are expected to be found in any newly sequenced genome from the corresponding clade. Using this data, BUSCO is able to evaluate the proportion of these essential genes (also named BUSCOs) found in a genome sequence or a set of (predicted) transcript or protein sequences. This is a good evaluation of the “completeness” of the genome or annotation.

.. admonition:: Hands-On: BUSCO

	1. At the top of the Tools panel (on the left), search for 'busco' and click on it.

	2. Run BUSCO with the following parameters:

		* Sequences to analyze: ``Filter sequences by length on X``

		* Mode: ``Genome``

        	* Lineage: ``proteobacteria``
	
	3. Click Execute. 

To examine the output click on the eye icon next to the ``Busco on X: short summary``. 

-------------------------

.. container:: toggle

    .. container:: header

        **How many complete single copy BUSCO genes did we find? Out of how many?**

    There should be about 202 out of 219 complete and single copy buscos. 

	
.. container:: toggle

    .. container:: header

        **Do you think that the genome quality is good enough to perform further analysis (annotation, phylogeny, primer degsign, etc)?**

    Most of the BUSCO genes are found as complete single copy, and very few are fragmented, which means that our genome have a good quality as it contains most of the expected content. That’s a very good material to perform an annotation. 

----------------------------

Let's discuss and then move on to further analysis. 
