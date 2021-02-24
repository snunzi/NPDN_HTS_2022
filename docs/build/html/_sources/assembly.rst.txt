Introduction to Genome Assembly
================================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/14uYUYaudMrC8T5xoHLVhRDwNkDD_1tQo4uJ46WvezgY

Introduction
^^^^^^^^^^^^

With some plant pathogens it is not possible, or very hard, to purify DNA/RNA for sequencing from just the specimen of interest. Instead you will isolate DNA that is contaminated, sometimes heavily, with DNA/RNA of a different origin (host, other microbes, etc.). This is the case for CLas samples, which we can not culture. Such contamination can pose an issue with genome assembly.

In this practical you will learn assemble an HLB genome with preprocessing to remove host and other contaminant genome reads.


Map Reads to Target Genome
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Map Reads to HLB Genome

    1. In tools menu, search for 'bowtie2' and click on it.

    2. Run bowtie2 tool with the following parameters

    * “Is this single or paired library”: ``Paired-end Dataset Collection``

    * “FASTQ Paired Dataset”: the collection of preprocessed Illumina-sequenced reads, output of trimmomatic tool  (names end in R1_paired and R2_paired).

    *  “Write unaligned reads (in fastq format) to separate file(s)”: ``No``

    *  “Write unaligned reads (in fastq format) to separate file(s)”: ``No``

    Activating this option may seem attractive since the aligned reads are what we are interested in, but filtering for those reads with a dedicated tool in a separate step allows us to filter on the properties of the read pairs instead of those of individual reads.

    * “Do you want to set paired-end options?”: ``No``

    * “Will you select a reference genome from your history or use a built-in index?”: ``Use a genome from the history and build index``

    * “Select reference genome”: ``psy62``

    * “Set read groups information?”: ``Do not set``

    * “Select analysis mode”: ``Default setting only``

    * “Do you want to tweak SAM/BAM Options?”: ``No``

    * “Save the bowtie2 mapping statistics to the history”: ``Yes``
        
The tool run should produce two output datasets. One with the actual mapped reads of the three samples and one with the corresponding mapping statistics for each sample, which we want to have a brief look at next.

Inspect the mapping stats of each sample by clicking on the corresponding collection, then on the galaxy-eye (eye) icon of each individual sample data

-------------------------

.. container:: toggle

    .. container:: header

        **What percentage of reads mapped to the HLB genome?**

    This sample should have ~99% of the reads mapped to the HLB psy62 genome.

----------------------------


HLB Read Extraction
^^^^^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Map Reads to HLB Genome

    1. In the tools menu seach for 'samtools view' tool to filter the Illumina-sequenced reads mapped with Bowtie2.

    2. Set the following parameters for samtools view:

    * param-collection “SAM/BAM/CRAM data set”: ``the collection of mapped Illumina-sequenced reads, output of Bowtie2 tool``

    * “What would you like to look at?”: ``A filtered/subsampled selection of reads``

    * in “Configure filters”

        * “Require that these flags are set”: ``Read is unmapped and Mate is unmapped``

        ..image:: _static/samtoolsfilter.png

    * “What would you like to have reported?”: ``All reads retained after filtering and subsampling``

    * “Produce extra dataset with dropped reads?”: ``No``

    * “Output format”: ``BAM (-b)``

    3. Click 'Execute'


Conversion to fastq format
^^^^^^^^^^^^^^^^^^^^^^^^^^

Assembly tools, typically, expect their input data to be fastq-formatted, but what we have after mapping and filtering is data in BAM format. Hence, we need to convert the retained Illumina-sequenced reads back into their original format before proceeding to assembly.

.. admonition:: Hands-On: Convert BAM to fastq.gz

    1. In the tools menu search for 'samtools fastx' tool and click on it. 

    2. Set the following parameters for samtools fastx:
        
        * param-collection “BAM or SAM file to convert”: ``the collection of filtered Illumina-sequenced reads, output of first Samtools view tool run``
                
        * “Output format”: ``compressed FASTQ``

        * “outputs”: ``READ1 and READ2``

    3. Click 'Execute'

Stop here and we will talk about how to do the actual assembly together. The assembly is the most time consuming part, and can easily take 10+ hours. Therefore, we will not perform the assembly now. We will work with the genome that has already been assembled from there reads. If you would like to perform the assembly on your own time, there are many assembly tutroials available for Galaxy here : https://training.galaxyproject.org/training-material/topics/assembly/ 
