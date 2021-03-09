Introduction to Genome Assembly
================================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/1LU6mebF8-E3fIf1wZDN8oPVdP3EQBXXCuix9dJnLtFs

Introduction
^^^^^^^^^^^^

With some plant pathogens it is not possible, or very hard, to purify DNA/RNA for sequencing from just the specimen of interest. Instead you will isolate DNA that is contaminated, sometimes heavily, with DNA/RNA of a different origin (host, other microbes, etc.). This is the case for HLB samples, which we can not culture. Such contamination can pose an issue with genome assembly.

In this practical you will learn assemble an HLB genome with preprocessing to remove host and other contaminant genome reads.

Upload Reference Assembly
^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to only assemble HLB reads, we will map to a reference genome and extract only those reads that map to it. First, lets upload a complete genome assembly available on NCBI. There are a few complete genomes available on NCBI, we chose this one because it contains all known prophages (some strains contain zero or only select prophages). If we had more time, we could align reads to all complete genomes to ensure we maximize the number of HLB reads we extract.

.. admonition:: Hands-On: Upload Genome Assembly


    1. At the top of the Tools panel (on the left), click Upload

    2. Click on 'Regular' Tab at the op

    3. Click 'Paste/Fetch Data' and paste in the NCBI link to the published psy62 HLB genome:

    https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/023/765/GCF_000023765.2_ASM2376v2/GCF_000023765.2_ASM2376v2_genomic.fna.gz.

    4. Click 'start'

    5. After upload completes, let's rename it to something more useful. On the history panel you should see the newly uploaded genome. Hit the pencil icon, to 'edit attributes' and rename the file to 'psy62.fna.gz'. Save changes by clicking the save icon.



Map Reads to Target Genome
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Map Reads to HLB Genome

    1. In tools menu, search for 'bowtie2' and click on it.

    2. Run bowtie2 tool with the following parameters

    * “Is this single or paired library”: ``Paired-end Dataset Collection``

    * “FASTQ Paired Dataset”: the collection of preprocessed Illumina-sequenced reads, output of trimmomatic tool  (names end in R1_paired and R2_paired).

    *  “Write unaligned reads (in fastq format) to separate file(s)”: ``No``

    *  “Write aligned reads (in fastq format) to separate file(s)”: ``No``

    Activating this option may seem attractive since the aligned reads are what we are interested in, but filtering for those reads with a dedicated tool in a separate step allows us to filter on the properties of the read pairs instead of those of individual reads.

    * “Do you want to set paired-end options?”: ``No``

    * “Will you select a reference genome from your history or use a built-in index?”: ``Use a genome from the history and build index``

    * “Select reference genome”: ``psy62.fna.gz``

    * “Set read groups information?”: ``Do not set``

    * “Select analysis mode”: ``Default setting only``

    * “Do you want to tweak SAM/BAM Options?”: ``No``

    * “Save the bowtie2 mapping statistics to the history”: ``Yes``

The tool run should produce two output datasets. One with the actual mapped reads of the sample and one with the corresponding mapping statistics the sample, which we want to have a brief look at next.

Inspect the mapping stats of each sample by clicking on the corresponding collection, then on the galaxy-eye (eye) icon

-------------------------

.. container:: toggle

    .. container:: header

        **What percentage of reads mapped to the HLB genome?**

    This sample should have ~5% of the reads mapped to the HLB psy62 genome.

----------------------------


HLB Read Extraction
^^^^^^^^^^^^^^^^^^^

Mapping with bowtie2 should have produced a sam file that contains all alignment information. We need to filter this to get only the reads that mapped to the HLB reference genome (these are the reads we will use for assembly--all other reads are likely host or other microbiota) We will use the tool samtools for this filtering.

.. admonition:: Hands-On: Map Reads to HLB Genome

    1. In the tools menu seach for 'samtools view' tool to filter the Illumina-sequenced reads mapped with Bowtie2.

    2. Set the following parameters for samtools view:

    * param-collection “SAM/BAM/CRAM data set”: ``the collection of mapped Illumina-sequenced reads, output of Bowtie2 tool``

    * “What would you like to look at?”: ``A filtered/subsampled selection of reads``

    * in “Configure filters”

        * “Require that these flags are set”: ``Read is unmapped and Mate is unmapped``

        .. image:: _static/samtoolsfilter.png

    * “What would you like to have reported?”: ``All reads retained after filtering and subsampling``

    * “Produce extra dataset with dropped reads?”: ``No``

    * “Output format”: ``BAM (-b)``

    3. Click 'Execute'


Conversion to fastq format
^^^^^^^^^^^^^^^^^^^^^^^^^^

Assembly tools, typically, expect their input data to be fastq-formatted, but what we have after mapping and filtering is data in BAM format. BAM files represent alignment information in compressed (non-human readable) format. Hence, we need to convert the retained Illumina-sequenced reads back into their original fastq format before proceeding to assembly.

.. admonition:: Hands-On: Convert BAM to fastq.gz

    1. In the tools menu search for 'samtools fastx' tool and click on it.

    2. Set the following parameters for samtools fastx:

        * param-collection “BAM or SAM file to convert”: ``the collection of filtered Illumina-sequenced reads, output of first Samtools view tool run``

        * “Output format”: ``compressed FASTQ``

        * “outputs”: ``READ1 and READ2``

    3. Click 'Execute'

Stop here and we will talk about how to do the actual assembly together. The assembly is the most time consuming part, and can easily take 10+ hours. Therefore, we will not perform the assembly now. We will work with the genome that has already been assembled from there reads. If you would like to perform the assembly on your own time, there are many assembly tutorials available for Galaxy here : https://training.galaxyproject.org/training-material/topics/assembly/

Upload Assembly
^^^^^^^^^^^^^^^

We will upload the assembly produced from the dataset we have been working with. This assembly was produced with Spades, a tool available in Galaxy. On your own time, you can follow the assembly tutorial available on the Galay training material page to assemble the genome using our filtered reads.

.. admonition:: Hands-On: Upload Genome Assembly

    1. At the top of the Tools panel (on the left), click Upload

    2. Click on 'Regular' Tab at the op

    3. Click 'Choose local files' and navigate to where you placed the folder 'hlb_assembly' and select file.

    4. Click 'start'
