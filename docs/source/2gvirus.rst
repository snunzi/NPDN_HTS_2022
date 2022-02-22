2G Viral Metagenomics
======================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/1co8nFTBXemb2USTyM5UX7qi7k9Qo1ErjfYFRG7LcF5M

Introduction
^^^^^^^^^^^^

Detecting viruses from metagenomic sequencing data can be done with two main techniques:

* Taxonomic assignment of reads
* Contig assembly followed by taxonomic assignment of contigs

First, we will focus on the taxonomic assignment of reads.

Import Data
^^^^^^^^^^^
Lets import data from a shared history. These are raw reads, exactly how you would receive them from a sequencing company or off your own Illumina sequencer.

.. admonition:: Hands-On: Import Viral Metagenomic Reads

  1. At the top of the screen click on ``Shared Data`` and select ``Pages``

  2. In the search field, search for ``NPDN`` and click on the page ``NPDN HTS 2022``

  3. Find the history for ``NPDN 2022 Data Day 2 Part I`` Select the green plus sign to import into your Galaxy environment.


Host Removal
^^^^^^^^^^^^^
The first step in any sequencing analysis is quality check (FastQC) and trimming (Trimmomatic or alternative). These sequences have already been trimmed since you practiced those steps on day 1.

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
^^^^^^^^^^^^^^^^^^^^^^^^^

Mapping with bowtie2 should have produced a bam file that contains all the alignment information. We need to filter this to get only the reads that did not map to the prunus reference genome (these are the reads we will use for assembly--all other reads are likely host) We will use the tool samtools for this filtering.

.. admonition:: Hands-On: Remove Host Reads

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

Sequence analysis tools typically expect their input data to be fastq-formatted, but what we have after mapping and filtering is data in BAM format. BAM files represent alignment information in compressed (non-human readable) format. We need to convert only reads that did not map to host back into their original fastq format before proceeding.

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

Read Assignment with Kraken
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In this tutorial we will be using kraken to identify members in a mixed set of metagenomic reads.

.. admonition:: Hands-On: Taxonomic Read Assignment with Kraken

    1. In the tools menu search for 'kraken' tool and click on it.

    2. Run kraken with the following parameters:

		* Single or paired end reads: ``paired``

		* Forward strand:  ``SampleC_nonhost_R1.fastq.gz`` (file we just filtered).

		* Reverse strand: ``SampleC_nonhost_R2.fastq.gz`` (file we just filtered).

		.. image:: _static/kraken_input.png

		* Select a kraken database: ``viral_2020``

		* Leave all others as default and click ``Execute``


Examine Kraken Output
^^^^^^^^^^^^^^^^^^^^^^

You should see a new output file at the top of your history panel called ``Kraken on data x: Classification``. Lets take a look at it.

When the file turns green (analysis done running) click on the eye icon next to the file to view it.

The columns correspond to the following:

1. "C"/"U": one letter code indicating that the sequence was either classified or unclassified.

2. The sequence ID, obtained from the FASTA/FASTQ header.

3. The taxonomy ID Kraken used to label the sequence; this is 0 if the sequence is unclassified.

4. The length of the sequence in bp.

5. A space-delimited list indicating the LCA mapping of each k-mer in the sequence. For example, "562:13 561:4 A:31 0:1 562:3" would indicate that:

	* the first 13 k-mers mapped to taxonomy ID #562

	* the next 4 k-mers mapped to taxonomy ID #561

	* the next 31 k-mers contained an ambiguous nucleotide

	* the next k-mer was not in the database

	* the last 3 k-mers mapped to taxonomy ID #562

.. container:: toggle

    .. container:: header

        **After looking at the first few sections of the results, in general are more reads classified or unclassified?**

    You should see the first column contains a lot of "U's", therefore most of the reads appear to be unclassified. Remember, we are just screening these against the virus database, so these reads could be host, bacteria, etc.

Kraken Report
^^^^^^^^^^^^^^
While the raw kraken output contains a lot of information, it is impossible to make sense of without summarizing it. Here, we will generate a kraken report to summarize the results.

.. admonition:: Hands-On: Generate a Kraken Report

	1. In the tools menu search for 'kraken-report' tool and click on it.

	2. Run kraken-report with the following parameters:

		* Kraken output: ``Kraken on data x: Classification``

		* Select a Kraken database: ``viral_2020``

When this analysis finished running it should generate a file ``Kraken-report on x``. Click the eye icon next to the result file and view the results.

The columns in the output correspond to the following:

1. percentage of reads in the clade/taxon in Column 6

2. number of reads in the clade.

3. number of reads in the clade but not further classified.

4. code indicating the rank of the classification: (U)nclassified, (D)omain, (K)ingdom, (P)hylum, (C)lass, (O)rder, (F)amily, (G)enus, (S)pecies).

5. NCBI taxonomy ID.

6. Scientific name

.. container:: toggle

    .. container:: header

        **What is the predominant classified species in the sample?**

    You should see the majority of the sample was unclassified (probably host, bacteria, etc.), and the predominant virus in the sample was plum pox virus.

	.. image:: _static/kraken_results.png


Kraken allowed us to identify what virus(es) were present in out sample, but gave us no information on whether this is a new strain, percent identity, etc. We will perform assembly of our reads to get more information.



Genome Assembly with Metaspades
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next we will assemble all reads that did not map to host using a specialized version of Spades designed for metagenomic samples, metaSpades.

.. admonition:: Hands-On: Assembly with metaviralSpades

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

	This sample should ~434 scaffolds assembled.

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
