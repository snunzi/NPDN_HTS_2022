Sequence QC/Intro to Galaxy
===========================

Lecture
^^^^^^^

.. slide:: https://docs.google.com/presentation/d/1UW-LtFjL-4pJANNzSwKvQC_IK8ILbLiw6K6ToYupZC8

Introduction
^^^^^^^^^^^^

In this practical you will learn to import and assess the quality of raw high throughput sequencing sequencing data in Galaxy.

The first dataset you will be working with is from an Illumina MiSeq dataset. The sequenced organism is the unculturable phloem-limited alphaproteobacterium “Candidatus Liberibacter
asiaticus”, which causes Huanglongbing (HLB) disease on citrus. The sequenced bacterium was obtained directly from the root of an infected pummelo sample in California. The sequencing was done as paired-end 2x300bp.

.. image:: _static/CitrusGreening1.jpg


Login to Galaxy
^^^^^^^^^^^^^^^
We will be using Galaxy for performing many of our bioinformatic analyses. This is a great free tool for performing bioinformatics, no coding necessary! There are lots of tutorials available online, both on how to use Galaxy and perform specific analysis. See training materials at link below on your own time.

https://training.galaxyproject.org/training-material/

.. admonition:: Hands-On: Login

    1. Open your favorite web browser (Chrome, Safari, or Firefox--not Internet Explorer) and login to Galaxy Europe. If not already registered for Galaxy Europe, use this link to register and login:

    https://usegalaxy.eu/

     .. image:: _static/galaxylogin.png

    2. Once logged in use the following link to access the training queue for this training. This link will only be available during the training, however your data and analysis results will still be available at usegalaxy.eu once training is over.

    https://usegalaxy.eu/join-training/npdn-hts


The Galaxy homepage is divided into three panels:

- Tools on the left
- Viewing panel in the middle
- History of analysis and files on the right

.. image:: _static/galaxy_interface.png


Name History
^^^^^^^^^^^^

.. admonition:: Hands-On: Name History

    1. Go to history panel (on the right)

    2. Click on the history name (which by default is “Unnamed history”)

    .. image:: _static/rename_history.png

    3. Type in a new name,  “NPDN Day 1”

    4. Press Enter on your keyboard to save it.


Import Sample Data
^^^^^^^^^^^^^^^^^^^
We will be working with reads as you would receive them off of a sequencer, or from a sequencing company. Let's import them into our Galaxy environment.

.. admonition:: Hands-On: Upload Data

    1. Copy the link locations below:

        https://zenodo.org/record/6110829/files/CA-Root_R1.fastq.gz
        https://zenodo.org/record/6110829/files/CA-Root_R2.fastq.gz

    2. Open the Galaxy Upload Manager ('Upload Data' on top-right of the tool panel)

    3. Select Paste/Fetch Data.

    4. Paste the links into the text field.

    5. Press start and close the window.

If you have any issues importing the files (slow network speed, etc.), please see the help page.

-------------------------

.. container:: toggle

    .. container:: header

        **We only sequenced this genome once, why do we have two files?**

    We performed paired-end sequencing, one set of reads is the forward read and the other is the reverse.

    .. image:: _static/pairedend.png

----------------------------

Assess Dataset Quality
^^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Hands-On: Assess Data Quality

    1. At the top of the Tools panel (on the left), search for 'fastqc' and click on it.

    2. Run fastqc with the following parameters:

		* Short read data from your current history: Click on the icon for 'multiple datasets' and highlight both ``CA-Root_R1.fastq.gz`` and ``CA-Root_R2.fastq.gz`` (Ctrl-click until they turn blue)

		* Leave everything else as defaults.

    .. image:: _static/fastqc_upload.png

    3. Scroll down and click 'Execute'


You will see four new files generated in your history, while the analysis is running you will see a spinning wheel next to these files. When analysis completes, those files turn green. You should have two history items ``FastQC on 1[2]: Webpage``, one for forward reads and one for reverse). Click on the eye icon next each of these files to examine the results.

FastQC provides various output statistics. Scroll through and examine them.

At what point in the read do quality scores start declining?

-------------------------

.. container:: toggle

    .. container:: header

        **Look at the GC content plot, there may be two peaks, why is this?**

    In metagenomic datasets, like this, you may get multiple GC peaks representing different GC content for the different taxa in the sample (i.e. one peak for host DNA and one for pathogen)

----------------------------

Improve Dataset Quality
^^^^^^^^^^^^^^^^^^^^^^^

Illumina sequencing technology requires us to ligate adapters to both ends of genomic material to facilitate binding and sequencing on the flowcell. Adapter sequences should be removed because they can interfere with genome assembly. We will use Trimmomatic for adapter trimming and quality filtering.

Read more about Trimmomatic here: http://www.usadellab.org/cms/?page=trimmomatic

.. admonition:: Hands-On: Improve Data Quality

    1. At the top of the Tools panel (on the left), search for 'trimmomatic' and click on it.

    2. Run trimmomatic with the following parameters:

		* Single-end or paired-end reads? ``Select 'Paired-end' (two separate input files)``

		* Input FASTQ file (R1\first pair of reads): Click on the down arrow and select ``CA_Root_R1.fastq.gz``

		* Input FASTQ file (R2\second pair of reads): Click on the down arrow and select ``CA_Root_R2.fastq.gz``

		* Perform initial ILLUMINACLIP step? ``Yes``

		* Leave all other parameters as default.

    .. image:: _static/trim.png


    3. Click 'Execute'

If you have time, please run FastQC again to see how trimming improved the dataset quality. If not, run this analysis later on your own time.
