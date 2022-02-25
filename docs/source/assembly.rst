Intro to Genome Assembly
=========================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/18dPo81PCN_cEQyBI3qmdRlmzcgRnXzuqUtPCaWnwrPs

Introduction
^^^^^^^^^^^^

With some plant pathogens it is not possible, or very hard, to purify DNA/RNA for sequencing from just the specimen of interest. Instead you will isolate DNA that is contaminated, sometimes heavily, with DNA/RNA of a different origin (host, other microbes, etc.). This is the case for CLas samples, which we can not culture. Such contamination can pose an issue with genome assembly.

In this practical you will learn assemble an CLas genome with preprocessing to remove host and other contaminant genome reads.

Upload Reference Assembly
^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to only assemble HLB reads, we will map to a reference genome and extract only those reads that map to it. First, lets upload a complete genome assembly available on NCBI. There are a few complete genomes available on NCBI, we chose this one because it contains all known prophages (at the time we published this genome). If we had more time, we could align reads to all complete genomes to ensure we maximize the number of HLB reads we extract.

.. admonition:: Hands-On: Upload Genome Assembly


    1. At the top of the Tools panel (on the left), click Upload

    2. Click on 'Regular' Tab at the top

    3. Click 'Paste/Fetch Data' and paste in the NCBI link to the published psy62 HLB genome:

    https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/023/765/GCF_000023765.2_ASM2376v2/GCF_000023765.2_ASM2376v2_genomic.fna.gz

    4. Click 'start'

    5. After upload completes, let's rename it to something more useful. On the history panel you should see the newly uploaded genome. Hit the pencil icon, to 'edit attributes' and rename the file to 'psy62.fna.gz'. Save changes by clicking the save icon.



Map Reads to Target Genome
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Map Reads to HLB Genome

	1. In tools menu, search for 'bowtie2' and click on it.

	2. Run bowtie2 tool with the following parameters

		* “Is this single or paired library”: ``Paired-end``

		* “FASTA/Q File #1”: Click on the down arrow and select ``Trimmomatic on CA_Root_R1.fastq.gz (R1 paired)``

		* “FASTA/Q File #2”: Click on the down arrow and select ``Trimmomatic on CA_Root_R2.fastq.gz (R2 paired)``

		* “Will you select a reference genome from your history or use a built-in index?”: ``Use a genome from the history and build index``

		* “Select reference genome”: ``psy62.fna.gz``

		* “Save the bowtie2 mapping statistics to the history”: ``Yes``

		* Leave the rest as default.

	3. Click Execute.

The tool run should produce two output datasets, one with mapped reads and the other with the corresponding mapping statistics. Let's take a look at the mapping statistics.

Inspect the mapping stats by clicking on eye icon next to ``Bowtie2 on X:mapping stats`` output in your history panel.

-------------------------

.. container:: toggle

    .. container:: header

        **What percentage of reads mapped to the HLB genome?**

    This sample should have ~5% of the reads mapped to the HLB psy62 genome.

----------------------------


HLB Read Extraction
^^^^^^^^^^^^^^^^^^^

Mapping with bowtie2 should have produced a sam file that contains all the alignment information. We need to filter this to get only the reads that mapped to the HLB reference genome (these are the reads we will use for assembly--all other reads are likely host or other microbiota) We will use the tool samtools for this filtering.

.. admonition:: Hands-On: Map Reads to HLB Genome

    1. In the tools menu search for 'samtools view' tool to filter the reads mapped with Bowtie2.

    2. Run samtools view with the following parameters:

		* “SAM/BAM/CRAM data set”: ``Bowtie2 on X: alignments``

		* “What would you like to look at?”: ``A filtered/subsampled selection of reads``

		* in “Configure filters”

			* “Require that these flags are set”: ``Read is mapped in a proper pair``

			.. image:: _static/samtoolsfilter.png

    3. Click 'Execute'


Conversion to fastq format
^^^^^^^^^^^^^^^^^^^^^^^^^^

Assembly tools typically expect their input data to be fastq-formatted, but what we have after mapping and filtering is data in BAM format. BAM files represent alignment information in compressed (non-human readable) format. We need to convert only the reads mapped to the HLB genome back into their original fastq format before proceeding to assembly.

.. admonition:: Hands-On: Convert BAM to fastq.gz

    1. In the tools menu search for 'samtools fastx' tool and click on it.

    2. Set the following parameters for samtools fastx:

        * “BAM or SAM file to convert”: ``Samtools view on X: filtered alignments``

        * “Output format”: ``compressed FASTQ``

        * “outputs”: ``READ1 and READ2``

		* Leave all other parameters as defaults.

    3. Click 'Execute'

The assembly is the most time consuming part, and can easily take 10+ hours. Therefore, we will not perform the assembly now. We will work with the genome that has already been assembled from there reads. If you would like to perform the assembly on your own time, there are many assembly tutorials available for Galaxy here : https://training.galaxyproject.org/training-material/topics/assembly/

Upload Assembly
^^^^^^^^^^^^^^^

We will upload the assembly produced from the dataset we have been working with. This assembly was produced with Spades, a tool available in Galaxy. On your own time, you can follow the assembly tutorial available on the Galaxy training material page to assemble the genome using our filtered reads.

.. admonition:: Hands-On: Upload Genome Assembly

    1. At the top of the Tools panel (on the left), click Upload

    2. Click on 'Regular' Tab at the top

    3. Click 'Choose local files' and navigate to where you placed the ``CA-Root_scaffolds.fasta`` assembly I sent you.

    4. Click 'start'
