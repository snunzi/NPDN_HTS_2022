Metagenomics Part 2
========================

Kraken allowed us to identify what virus(es) were present in out sample, but gave us no information on whether this is a new strain, percent identity, etc. We will perform assembly of our reads to get more information.

Host Removal
^^^^^^^^^^^^^

First, we need to remove host contamination since this is a metagenomic sample. 

.. admonition:: Hands-On: Map to Host Genome

	1. In tools menu, search for 'bowtie2' and click on it.

	2. Run bowtie2 tool with the following parameters

		* “Is this single or paired library”: ``Paired-end``

		* “FASTA/Q File #1”: Click on the down arrow and select ``SampleC_reads_R1.fastq.gz``
		
		* “FASTA/Q File #2”: Click on the down arrow and select ``SampleC_reads_R2.fastq.gz``

		* “Will you select a reference genome from your history or use a built-in index?”: ``Use a genome from the history and build index``

		* “Select reference genome”: ``prunus_genome.fna.gz``

		* “Save the bowtie2 mapping statistics to the history”: ``Yes``
		
		* Leave the rest as default.
	
	3. Click Execute.
	

Bowtie2 should produce two output datasets, one with mapped reads and the other with the corresponding mapping statistics. Let's take a look at the mapping statistics. 

Inspect the mapping stats by clicking on eye icon next to ``Bowtie2 on X:mapping stats`` output in your history panel. 

-------------------------

.. container:: toggle

	.. container:: header

		**What percentage of reads mapped to the prunus genome?**

	This sample should have ~83% of the reads mapped to the host, prunus, genome.

----------------------------

Non-Host Read Extraction
^^^^^^^^^^^^^^^^^^^

Mapping with bowtie2 should have produced a sam file that contains all the alignment information. We need to filter this to get only the reads that did not map to the prunus reference genome (these are the reads we will use for assembly--all other reads are likely host) We will use the tool samtools for this filtering.

.. admonition:: Hands-On: Map Reads to HLB Genome

	1. In the tools menu search for 'samtools view' tool to filter the reads mapped with Bowtie2.

	2. Run samtools view with the following parameters:

		* “SAM/BAM/CRAM data set”: ``Bowtie2 on X: alignments``

		* “What would you like to look at?”: ``A filtered/subsampled selection of reads``

		* in “Configure filters”

			* “Require that these flags are set”: ``Read is unmapped`` and ``Mate is unmapped``

			.. image:: _static/bowtie2_prunus.png

	3. Click 'Execute'


Conversion to fastq format
^^^^^^^^^^^^^^^^^^^^^^^^^^

Assembly tools typically expect their input data to be fastq-formatted, but what we have after mapping and filtering is data in BAM format. BAM files represent alignment information in compressed (non-human readable) format. We need to convert only reads that did not map to host back into their original fastq format before proceeding to assembly.

.. admonition:: Hands-On: Convert BAM to fastq.gz

	1. In the tools menu search for 'samtools fastx' tool and click on it. 

	2. Set the following parameters for samtools fastx:
	
		* “BAM or SAM file to convert”: ``Samtools view on X: filtered alignments``
		
		* “Output format”: ``compressed FASTQ``

		* “outputs”: ``READ1 and READ2``
		
		* Leave all other parameters as defaults.

	3. Click 'Execute'
	
	4. When job completes, rename the output files to something more useful. 
	
		* Click on pencil icon next to ``data X converted to fastqsanger.gz (READ 1)`` and rename to ``SampleC_nonhost_R1.fastq.gz``
		
		* Click on pencil icon next to ``data X converted to fastqsanger.gz (READ 2)`` and rename to ``SampleC_nonhost_R2.fastq.gz``
		
Genome Assembly with Metaspades
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next we will assemble all reads that did not map to host using a specialized version of Spades designed for metagenomic samples, Metaspades. 

.. admonition:: Hands-On: Assembly with Metaspades

	1. In the tools menu search for 'metaspades' tool and click on it. 

	2. Run this tool with following parameters:
		
		* Forward Reads: ``SampleC_nonhost_R1.fastq.gz``

		* Reverse Reads: ```SampleC_nonhost_R2.fastq.gz``
		
		* Leave the rest as default
	
	3. Click Exceute. 
	
When the assembly completes, take a look at the ``SPades scaffolds`` output.

-------------------------

.. container:: toggle

	.. container:: header

		**How many scaffolds were assembled?**

	This sample should ~422 scaffolds assembled.

----------------------------

Contig Length Filtering
^^^^^^^^^^^^^^^^^^^^^^^^

Because it would take us a long time to blast search over 400 contigs, we will filter by length and only look at the longest contigs here. Normally we would pick a much lower threshold (~200 nt) in order not to miss anything, especially viroids.  

.. admonition:: Hands-On: Contig Filtering

	1. At the top of the Tools panel (on the left), search for 'filter sequences by length' and click on it.

	2. Run this tool with following parameters:
		
		* Fasta file: ```SPades scaffolds``

		* Minimal length: ``3000``

		* Maximum length: ``0``
		
-------------------------

.. container:: toggle

	.. container:: header

		**How many contigs are left after filtering?**

	This sample should have ~2 contigs left after filtering.

--------------------------

Blast Contigs
^^^^^^^^^^^^^^

While Galaxy does have a built in Blast tool, I found it very slow. With the small number of contigs we have left, we can use Blast through NCBI. 

.. admonition:: Hands-On: Contig Filtering

	1. In the history panel, click on the eye icon to view your newly filtered contigs ``Filter sequences by length on X``.

	2. Copy the entire content of this file. (Should be two contigs in fasta format)
	
	3. Open the NCBI Blastn website in another browser tab: https://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastSearch
	
	4. Paste your contigs sequences	you copied into the box under ``Enter accession number(s), gi(s), or FASTA sequence(s)``
	
	5. Scroll down and hit Blast. 


-------------------------

.. container:: toggle

	.. container:: header

		**What was your top Blast hit for each of your two contigs?**

	You should see your longer contig is Plum Pox Virus D (with entire genome recovered), while your other contig is host contamination.

----------------------------

Questions/Discussion
