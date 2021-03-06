# DeltaMP
 **A flexible, reproducible and resource efficient metabarcoding amplicon pipeline for HPC**

DeltaMP is a command line tool for high performance computer taking advantage of queueing systems to parallelize and speed-up bioinformatic processing of metabarcoding raw read libraries.

DeltaMP is initially developed to process **16S, 18S, ITS** raw read libraries with the most up-to-date bioinformatic workflows, but can also handle any other barcoding targets (e.g. 23/28S, COI, rbcL).

DeltaMP is intended to be accessible for non-bioinformatician users with its fully tunable workflows based on a TAB-separated configuration file.

DeltaMP integrates a checkpointing feature which enables easy and efficient comparisons between different workflows applied on the same set of read libraries.

Last but not least, DeltaMP produces version controlled, reproducible and fully documented OTU tables in TAB-separated and BIOM formats readily usable for downstream taxonomic and OTU diversity analyses.

## Table of Contents
 - [Installation](#installation)
 - [Dependencies](#dependencies)
 - [Quick start](#quick-start)
 - [Usage instructions](#usage-instructions)
 - [Configuration file](#configuration-file)
   * [PROJECT section](#project-section)
   * [LIBRARIES section](#libraries-section)
   * [TARGET section](#target-section)
   * [PAIR-END section](#pair-end-section)
   * [TRIMMING section](#trimming-section)
   * [PIPELINE section](#pipeline-section)
   * [SAMPLES section](#samples-section)
 - [Pipeline execution](#pipeline-execution)
   * [Quick start](#quick-start)
   * [best practices](#best-practices)
   * [pipeline execution options](#pipeline-execution-options)
   * [Checkpointing](#checkpointing)
 - [Pipeline analysis steps](#pipeline-analysis-steps)
 - [Outputs](#outputs)
 - [Troubleshooting](#troubleshooting)
 - [References](#references)


## Installation

Source code of DeltaMP version 0.2 is available at <https://github.com/lentendu/DeltaMP/releases/tag/v0.2>

The repository could also be cloned using for example:
```
git clone https://github.com/lentendu/DeltaMP.git
```

Then install following the [installation instructions](INSTALL).


## Dependencies

DeltaMP is intended to be used on a HPC with a job scheduler (i.e. batch-queuing system).

### supported job schedulers:

**[SLURM](https://slurm.schedmd.com)**

**Grid Engine**
+ [Oracle Grid Engine](http://www.oracle.com/technetwork/oem/grid-engine-166852.html)
+ [Open Grid Scheduler](http://gridscheduler.sourceforge.net)
+ [Univa Grid Engine](http://www.univa.com/products)


### compulsory softwares:

+ [MOTHUR v1.37+](http://www.mothur.org) (Schloss et al., 2009)
+ [OBITools v1.2+](https://git.metabarcoding.org/obitools/obitools) (Boyer et al., 2016)
+ [cutadapt v1.10+](https://cutadapt.readthedocs.io/en/stable/) (Martin, 2011)
+ [PANDAseq v2.10+](https://github.com/neufeld/pandaseq) (Masella et al., 2012)
+ [biom-format](http://biom-format.org) (McDonald et al., 2012)
+ [GNU parallel](http://savannah.gnu.org/projects/parallel)
+ [R v3](https://cran.r-project.org/) with the [plyr](https://cran.r-project.org/web/packages/plyr) package
+ [Ghostscript](https://www.ghostscript.com/)
+ [WebLogo](https://github.com/WebLogo/weblogo)
+ [archive-sum](https://github.com/idiv-biodiversity/archive-sum)


### optional softwares:

+ [QIIME v1.8+](http://qiime.org) for 454 demultiplexing ([minimal install](http://qiime.org/1.9.0/install/install.html#native-base) is enough; Caporaso et al., 2010)
+ [FlowClus](https://github.com/jsh58/FlowClus) for 454 denoising (Gaspar and Thomas, 2015)
+ [swarm v2](https://github.com/torognes/swarm) (MahéO et al., 2015)
+ [vsearch v2](https://github.com/torognes/vsearch) (Rognes et al., 2016)
+ [sumatra](https://git.metabarcoding.org/obitools/sumatra) and [sumaclust](https://git.metabarcoding.org/obitools/sumaclust) (Mercier et al., 2013)
+ [MCL](http://micans.org/mcl) (van Dongen, 2000)
+ [CD-HIT & cd-hit-454](http://weizhongli-lab.org/cd-hit/) for (pre-)clustering (Fu et al., 2012)
+ [ITSx](http://microbiology.se/software/itsx/) (Bengtsson‐Palme Johan et al. 2013)

All these dependencies need to be available through the $PATH environmental variable or need to be loaded by the DeltaMP module file.


## Quick start




## Usage instructions

Usage could be accessed via the command line:

```
deltamp -h
```

```
NAME
	DeltaMP version 0.2 - a flexible, reproducible and resource efficient metabarcoding amplicon pipeline for HPC

SYNOPSIS
	Usage: deltamp [-a account_or_project_name] [-cdfhnqtx] [-m max_running_tasks] [-p reference_subproject] [-r step] configuration_file

DESCRIPTION
	-h	display this help and exit

	-a ACCOUNT
		account or project name for the job queuing system

	-c	check dry run (-d option) by preserving the SUBPROJECT and PROJECT directories in the output directory

	-d	dry run avoid any submission to the queuing system and only output submission informations

	-f	display default values for optional configuration parameters

	-n	avoid checkpointing (i.e. searching for previous SUBPROJECT) and run the pipeline from the beginning

	-m max_running_tasks
		fix a maximum number of concurrently running tasks per array jobs, default is 400

	-p SUBRPOJECT
		only execute additional jobs or with different input variables compared to the reference SUBPROJECT. This will only work if both configuration files have the same input libraries and
		output directory.

	-q	proceed until quality step only

	-r STEP
		restart pipeline computation from STEP. Replace STEP by 'list' to list all available steps of the subproject associated with the provided configuration file.

	-t	proceed until demultiplexing step only

	-x	delete the subproject associated with the provided configuration file

AUTHOR
	Guillaume Lentendu, Christina Weißbecker, Anna Heintz-Buschart and Tesfaye Wubet

REPORTING BUGS
	Submit suggestions and bug-reports at <https://github.com/lentendu/DeltaMP/issues>, send a pull request on <https://github.com/lentendu/DeltaMP>, or compose an e-mail to Guillaume Lentendu <lentendu@rhrk.uni-kl.de>.

COPYRIGHT
	Copyright (C) 2018 Guillaume Lentendu, Christina Weißbecker, Anna Heintz-Buschart and Tesfaye Wubet

	This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of
	the License, or (at your option) any later version.

	This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
	GNU General Public License for more details.

	You should have received a copy of the GNU General Public License along with this program. If not, see <http://www.gnu.org/licenses/>.
```

## Configuration file

To run, DeltaMP always required a configuration file and, optionaly, local copies of raw sequence libraries.

The configuration is encoded in UTF8 and the fields are TAB-separated.

If the file is edited under Windows, pay attention to use Unix compliant end of line (\n).

The following sections describe the parameters to provide in the configuration file.

Only the project name parameter and a sample list (see [SAMPLE section](#sample-section)) are compulsory.

A parameter is recognize by its exact definition in the first column of the configuration file.

If an optional parameter is let empty or if its definition is not found in the first column, its default value will be used instead.

The default values for the optional parameters could be displayed by using the -f option (see the [usage instructions](#usage-instructions)).

### PROJECT section

+ __Project name__: name of your project. This can be shared among multiple datasets and targets.

+ __User name__: full name of the person in charge of the analysis. The default is $USER.

+ __Path to output location__: full path to the output location. All final outputs will be copied into sub-directories of that directory. The default is /home/$USER .

+ __Path to execution location__: full path of the working directory. All computations will take place in that directory. The default is /work/$USER . 


### LIBRARIES section

+ __Sequencing technology__: “Illumina” or “454”. The default is Illumina.

+ __Directory path to archives or libraries OR BioProject accession__: full path of the directory where to find the sequence libraries or archives. If the raw sequences are publicly available at the European Nucleotide Archive (https://www.ebi.ac.uk/ena), only provide the BioProject accession number. The libraries will then automatically be retrieved from the provided URLs in the BARCODE section. The default is /home/$USER.

+ __Tab separated list of archives__: filename of archives containing raw libraries, one per column. The default is “no” for no archive.

+ __Libraries already demultiplexed__: “yes” or “no”. If the libraries are not demultiplexed, one fastq/sff file per sample in each library will be produced. An archive containing all demultiplexed libraries and labeled with "raw_fastq"/“raw_sff” will be created in the output directory. The default is “yes” for libraries already demultiplexed.


### TARGET section

+ __Target organisms__: Latin name of the targeted group as labelled in the reference sequence database (eg. Fungi, Glomeromycota, Bacteria, Archea, Eukaryota). The OTUs assigned to the target group will be extracted from the main OTU table and copied into an additional OTU table labeled with the provided Latin name. The default is “Fungi”.

+ __Target region__: any name is accepted here, but specific workflows will be allowed with “16S”, “18S”, “ITS” or “COI” respectively for prokaryotes, eukaryotes, Fungi or Metazoa workflows. The default is “ITS”.

+ __Forward primer name__: the biological forward primer. The 5'-3' orientation of the forward primer must be the same than the reference database sequence orientation. The default is “fITS7”.

+ __Forward primer sequence (5' to 3')__: the sequence of the biological forward primer. The default is “GTGARTCATCGAATCTTTG”.

+ __Reverse primer name__: the biological reverse primer. The default is “ITS4”.

+ __Reverse primer sequence (5' to 3')__: the sequence of the biological reverse primer. The default is “TCCTCCGCTTATTGATATGC”.

+ __Sequencing direction__: *454 specific parameter*, “forward” or “reverse” for forward libraries (column 3 of [SAMPLES section](#samples-section)) predominently orientated in the forward or reverse primer direction, respectively. The default is “forward”.


### PAIR-END section
*all Illumina-specific parameters*

+ __Remove primers at reads ends__: "no", "3prime", "5prime" or "both". Using cutadapt, "3prime" remove non-anchored reverse-complemented reverse and forward primers from 3'-end of forward and reverse libraries, respectively; "5prime" remove non-anchored forward and reverse primers from 5'-end of forward and reverse libraries, respectively; "both" combined both previous removal using the [linked adapter strategy](https://cutadapt.readthedocs.io/en/stable/guide.html#linked-adapters-combined-5-and-3-adapter) with anchored 5' adapter; value "no" avoid primer clipping. For "3prime", "5prime" and "both" values, unmatched reads are checked against the opposite primer/linked-adapter to allow for biderectionnal sequencing strategy detection, and the reads matched in this second step are reverse-complemented and append to the reads match at the first step. The default is "5prime".

+ __PandaSeq algorithm__: "simple_bayesian", "ea_util", "flash", "pear", "rdp_mle", "stitch" or "uparse" . See the [PandaSeq manual](https://storage.googleapis.com/pandaseq/pandaseq.html) (or `man pandaseq`) for algorithm descriptions. The default is “simple_bayesian”.

+ __PandaSeq threshold__: a number between 0 and 1 provided to the -t option of pandaseq. The default is 0.6.

+ __Minimum overlap__: a number between 0 and ∞ provided to the -o option of pandaseq. The default is 1.


### TRIMMING section
+ __Denoising__: *454 specific parameter*, "yes" or "no", whether to perform flowgram denoising using FlowClus or not. The default is "no".

+ __Minimum number of flows__: *454 specific parameter*, a number between 300 and 600, minimum length of flowgrams to be kept for denoising. The default is 360.

+ __Number of mismatches allowed on the barcode sequence__: a number between 0 and 2, or 'a' for automatic detection of maximum allowed mismatch on a barcode to avoid mislabeling of sequence inside one library. This value is used for demultiplexing only. The default is 1.

+ __Number of mismatches allowed on the primer sequence__: a number between 0 and 10. For Illumina libraries, this number will only be used to calculate allowed dissimilarity (number of mismatch / primer length) for primer detection and removal by cutadapt. The default is 6.

+ __Maximum number of ambiguities allowed in the sequence__: a number between 0 and ∞. The default is 0.

+ __Maximum homopolymer length allowed__: a number between 0 and 20. The default is 10.

+ __Minimum sequence length__: a number between 50 and the set Maximum sequence length. This parameter is used for pair-end assembled reads only in the case of Illumina libraries. The default is 50.

+ __Maximum sequence length__: a number between the set Minimum sequence length and 1000. This parameter is used for pair-end assembled reads only in the case of Illumina libraries. The default is 600.

+ __Minimum average quality on the trimmed sequence length__: a number between 20 and 30 (Phred score). The read quality average is calculated after optimal sequence length trimming. The default is 20.


### PIPELINE section

+ __Minimum number of trimmed reads per sample__: a number between 0 and ∞. If the minimum of raw, pair-end or trimmed reads is not reached in any of the samples, the pipeline will stop after the quality step report the reads numbers in the quality step .out logfile. The default is 2000.

+ __ITSx region to extract__: "no", “ITS1” or “ITS2” to skip or to extract either regions from fungal ITS reads using ITSx. The default is “no”.

+ __Pre-clustering__: "no", "cdhit454" or "mothur" to skip, to use [cd-hit-454](http://weizhong-lab.ucsd.edu/public/?q=softwares/cd-hit-454) or [mothur pre.cluster]() algorithms to pre-cluster reads after trimming and before chimera removal. For the mothur based pre-clustering, and aligned version of a reference database is required (see below at + Database prefix name). The default is "no".

+ __Chimera removal__: "before", "after" or "both", to check for chimera before OTU clustering only (in each sample separetedly), after OTU clustering only (among OTU representative sequences), or at both moments. *De-novo* chimera are detected with UCHIME and removed. The default is "after".

+ __Subsampling__: “yes” or “no”. Activating subsampling will randomly select the same number of reads in all samples according to the read count in the sample with the lowest number of reads, after the trimming step. The default is “no”.

+ __Clustering algorithm__: "cd-hit-est”, “vsearch”, “mcl”, “sumaclust” or “swarm”. The default is “mcl”.

+ __Clustering similarity threshold__: a number between 80 and 100 (percent of similarity). Disregarded if the “swarm” clustering algorithm is used. The default is 97.

+ __Cluster with previous subproject reference sequences__: the accepted values are a full path or “no”, If the full path to a previous subproject output directory is provided, the reference sequences of this previous subproject will be used as seeds to cluster the sequences from the current subproject. Sequences with similarity to previous subproject reference sequences less than the provided similarity threshold will be clustered de novo. This is only possible with the “cd-hit-est” clustering algorithm. The default is “no”.

+ __Remove singletons before chimera re-check__: the accepted values are “yes” or “no”. This option allow to remove singleton OTUs from sub-sequent analyses in order to reduce computation time and memory footprints, as high number of singletons could have critical effects on grid job with limited resources for both following chimera re-check and taxonomic identification steps. The default is “no”.

+ __Taxonomic classifier__: "bayesian" or "vsearch", to use the RDP naive bayesian classifier (Wang et al., 2007) as implemented in mothur or the usearch global alignment method (Edgard, 2010) as implemented in vsearch. The default is "bayesian".

+ __Directory path to database__: the full path to the directory containing reference sequence database files. The default is /home/$USER .

+ __Database prefix name__: filename prefix identifying database files. A database has to be composed from a TAB-separated "prefix".txt file in which the second column describe the version, citation and reference of the database with the first column containing VERSION, CITATION and FULLCITATION, respectively (see e.g. <test/pr2_4.10.0.txt>) and either from a pair of "prefix".fasta and "prefix".taxonomy mothur's style database format for "bayesian" classifier, or from a "prefix".udb binary file produced by the command `vsearch --makeudb_usearch` for "vsearch" classifier. Optionnaly, a "prefix".align.fasta, containing an aligned version of the "prefix".fasta sequences, is needed for pre-clustering with "mothur". See the <test/> directory for example files in each format. You can use the "update_xxx.sh" scripts in <auxillary_scripts/> to automatically download the PR2, SILVA and UNITE databases and to format them into the previously described formats. The default is "pr2_4.10.0".

+ __Reduce database to amplified fragment__: "yes" or "no", to cut the database reference sequences using either the provided primers or ITSx with the provided 'ITSx region to extract' or do nothing, respectively. If set to "yes", this also uses the cut database for taxonomic assignment, while if set to "no" this will use the full database sequences for taxonomic assignment. If the same cut read is produced from multiple accessions with different taxonomic paths, the cut read will be annotated with the least common ancestor. If set to "yes" and the cut database is already present in the "Directory path to database", the database cutting is skipped. The default is "no".

+ __Consensus assignment threshold__: a number between 50 and 100. Consensus threshold percent to assign a taxonomic rank among the matches. For "bayesian" taxonomic classifier the matches are the 100 bootstrap matches, for "vsearch" the matches are the best match(es) plus the match(es) in a 1 % similarity range below the best match(es) similarity. The default is 60.

+ __Assign all reads__: the accepted values are “yes” or “no”. Activating this option will assign all dereplicated or pre-clustered reads to a taxonomy. A consensus assignment is thenetermined for each OTU at a threshold of 60 %. If set to “no”, only the most abundant read per OTU will be assigned to taxonomy. The default is “no”.

+ __Assign putative function__: "yes" or "no" to assign putative function to Fungi using the FUNGuild database (Nguyen et al., 2016).

+ __Minimum number of sample for abundant OTUs__: a number between 0 and ∞ (see [Outputs](#outputs)). The default is 1.

+ __Minimum number of reads for abundant OTUs__: a number between 0 and ∞ (see [Outputs](#outputs)). The default is 4.


### SAMPLES section

+ __454 libraries__: the columns 1 to 3 have to be filled with barcode sequences, sample names and library filenames or URLs, respectively. If the same barcode was used to sequence in both forward and reverse directions, the library containing the reverse primer at its 5'-end have to be provided in the fourth column.

+ __Illumina libraries__: the columns 2 to 4 have to be filled with sample names, filename or URL of the forward libraries and the filename or URL of the reverse libraries, respectively. The column 1 have to remain empty has only delmultiplexed Illumina libraries can currently be processed by DeltaMP.

Columns 3 and 4 accept libraies in fastq or sff format with most kinds of compression (.gz, .tar, .tar.gz, .tgz, .bz2, .tar.bz2, .tbz2, .zip).

For ENA libraries, the column 2 have to match ENA "Submitter's sample name" field and column 3 to 4 have to match ENA full ftp URLs of a run accession as listed in ENA fields “Submitted files (FTP)” or "FASTQ files (FTP)".

Example configuration files 'configuration_xxx.tsv' are available in the test/ directory after installation with `make`.


## Pipeline execution

### Quick start

To execute the full pipeline, run the following command in a terminal:
```
deltamp [path/]configuration_file
```
replacing [path/] by the path to your configuration file, and “configuration_file” by its filename. Avoid “path/” if you are already in the right directory.

For each DeltaMP execution, a "PROJECT” directory labeled with the project name will be created into the "Path to output location" (nothing done if this directory already exist). A “SUBPROJECT” directory will be created inside the PROJECT directory and will be labeled as follow: “Project name”\_”Sequencing technology”\_”Target region”\_”unique identifier”. The unique identifier (cksum of date, time and configuration file) enables differentiation between each instance of the pipeline execution for the same project. 

The SUBPROJECT directory is copied into the "Path to execution location" and all necessary jobs are submited to the queue.

Once all step's jobs are completed, the outputs are copied back into the "Path to output location"/PROJECT/SUBPROJECT directory.

### Directories structure

+ output SUBPROJECT directory:
   * from startup: directory config/ containing the initial configuration file renamed configuration.SUBPROJECT.tsv and internal configuration text files
   * at execution end: files SUBPROJECT.documentation.txt and archives SUBPROJECT.outputs.tar.gz and SUBPROJECT.processing.files.tar.gz (and respective md5sum)
   
+ execution SUBPROJECT directory:
   * archives/: store outputs before copying them back to output directory
   * config/: same as in output directory with additionnaly "jobid" list of jobids and jobnames of queued jobs and lists of files ("xxx.files") and directories ("xxx.dir") in the execution directory after each job step "xxx"
   * libraries/: for raw libraries and reads filtering until quality step
   * log/: output "xxx.out" and error "xxx.err" logfiles of each job step "xxx", split among tasks for array job (e.g. "xxx.1.out")
   * processing/: contains files and directories produced from trim step to the end
   * quality_check/: used for the quality step

### best practices

+ Begin with a dry run to check for correct configuration
```
deltamp -d [path/]configuration_file
```

+ start analyses with a "quality_only" workflow in order to first control read counts and quality:
```
deltamp -q [path/]configuration_file
```
The output statistics will be then useful to check the proper handling of raw reads until trimming step (e.g. demultiplexing of 454 libraries or pair-end assembly of Illumina libraries), before starting more computational intensive OTU clustering and taxonomic identification steps.

+ test DeltaMP behaviors for checkpointing with multiple dry run on a continuously modified configuration files, avoiding the cleaning of the dry run directories:
```
deltamp -cd [path/]configuration_file
```
To run the pipeline normally after such a dry checkpointing, first delete all directories created for dry runs from the output directory, or change the output directory in the configuration file. 

### Checkpointing

In order to avoid to run the pipeline from the beginning if only one parameter is modified in the configuration file (e.g. clustering algorithm), DeltaMP allow re-use of previously produced files in the execution directory by [checkpointing](https://en.wikipedia.org/wiki/Application_checkpointing).

Checkpointing is turn on by default, which means that if any SUBPROJECT with the same target, same sequencing technology and the same list of samples is found in the "Path to output location/PROJECT" directory, all configuration parameters and option values will be compared.

Then come different situations:
+ if a previous SUBPROJECT has the same set of configuration parameters and option values as the current deltamp call, an error message is issued and nothing happens to avoid any useless re-calculation. If the users really want to re-compute, he/she will have to first delete the previous SUBPROJECT, to change the "Path to output location" or to disable checkpointing with option -n.

+ if a previous SUBPROJECT has the same set of configuration parameters and option values as the current deltamp call, but the SAMPLES section differs, an error message is issued and nothing happens to avoid checkpointing for different sets of samples/libraries. If the users really want to re-compute, he/she will have to first delete the previous SUBPROJECT, to change the "Path to output location" or to disable checkpointing with option -n.

+ if at least one configuration parameter or option value differs from any previous SUBPROJECT, a new SUBPROJECT will be created and the computation will start directly after the last common step, while all files and directories produced during the common steps of the previous SUBPROJECT are symlinked to the execution directory or hard copied to the output directory of the new SUBPROJECT. If multiple previous SUBPROJECT exist, the previous SUBPROJECT is the one with the highest amount of common steps (then the oldest if still multiple previous SUBPROJECT). 

Relationship tree among successive checkpointed SUBPROJECTs are documented in the output directory of SUBPROJECTs with at least one previous SUBPROJECT at config/tree.summary.

Checkpointing could be repeated multiple times, as long as at least one parameter differs from any of the previous SUBPROJECTs.

The previous SUBPROJECT could be hard set with the -p option, otherwise the previous SUBPROJECT with highest number of common steps will be taken as reference.

Checkpointing can be turn off by using the -n option.


## Pipeline analysis steps

Running the DeltaMP command will generate the directories and configuration files necessary to conduct the pipeline analysis as well as submitting the required steps to the queuing system.

The following steps are bash scripts available in the bin directory after build and are named following the scheme “xxx.sh”, xxx being the name of the step.

For 454 or Illumina specific steps, step script filenames follow “454_xxx.sh” and “Illumina_xxx.sh” schemes, respectively.

+ init: create symlink to files of previous SUBPROJECT if checkpointing; one serial job
+ get: raw data copy/download and oligo file(s) creation; one serial job
+ 454 libraries specific steps
   * demulti: demultiplex sff based on barcodes; target raw sequences in libraries containing multiple target primers are identified when holding the expected forward sequencing primer with a maximum number of mismatches equal to one third of the primer length; one parallel array job per run/lane library
   * sff: sff to fasta + qual and raw reads statistics; one parallel array job per group of 10 libraries
   * raw_stat: summarize raw statistics; one serial array job per library
   * opt: sequence count for incremented length and quality values; one parallel array job per group of 10 libraries
   * quality: control sequencing depth for raw and trimmed reads; optimize the length and quality trimming parameters; one serial job
+ Illumina libraries specific steps
   * demulti: demultiplex fastq based on barcodes using cutadapt; one serial job per run/lane library
   * fastq: cut primers; raw reads quality and length statistics; one serial array job per library
   * pair_end: pair-end assembly; convert to fasta + qual; cut and pair-end reads counts and pair-end reads quality and length statistics; one serial array job per library
   * raw_stat: combine length, average quality, average base position quality and overlap length statistics for all libraries; one serial job
   * opt: optimization of reads average quality for trimming; one serial array job per library
   * quality: control sequencing depth for raw, cut, pair-end and trimmed reads; optimize the quality trimming parameters; one serial job
+ cut_db: reduce database reference sequences to amplified or ITS-covered region; format to mothur and vsearch udb; only one time per database version and pair of primer names; one serial job
+ trim: group each sample reads if multiple libraries for one sample; denoise; trim; dereplicate; subsample; align and remove badly aligned sequences if SSU gene and SILVA database; precluster at maximum 1 % similarity if Illumina or 454 SSU SILVA; remove chimeras; one serial array job per sample
+ merge: merge fasta and mothur names files and dereplicate; one serial job
+ OTU: cluster sequences into OTUs; pick representative sequences; remove singletons (optional); chimera check; one parallel job
+ id: classify all or only OTU representative sequences against a reference database; create OTU consensus assignment if needed; one parallel job
+ end: create OTU tables, extract representative sequences and count reads; combine each logs from all steps; add sample's metadata from ENA BioProject to BIOM file; add functional annotation; one serial job
+ archiving: compress raw demultiplexed reads (if demultiplexing), pipeline outputs and processing file in separated tar.gz archives; copy to the output directory
+ doc: create the complete documentation of the pipeline processes; one serial job

Each step job is waiting in the queue as long as its preceeding step job is not completed, except for the cut_db step which does not have to wait for any job completion. For checkpointing, the init step job will wait for the completion of the last common step job from the previous SUBPROJECT.


## Outputs

### documentation:

SUBPROJECT.documentation.txt: The file describes the whole processing of the libraries through the pipeline in a human readable format. This file is not archived and will be directly outputted in the SUBPROJECT output directory.

### files in SUBPROJECT.outputs.tar.gz at the end of a full analyses :

+ TAB-separated OTU matrices ("_OTUS.tsv") and representative sequences ("_repseq.fasta") in four different flavors:
   * “SUBPROJECT.all” for all OTUs
   * “SUBPROJECT.abundant” dominant OTUs as defined by the parameters “Minimum number of reads for dominant OTUs” and "Minimum number of sample for dominant OTUs"
   * “SUBPROJECT.all_TARGET” for all OTUs assigned to the "TARGET" target group as defined by the parameter “Target organisms”
   * “SUBPROJECT.abundant_TARGET” for the dominant OTUs assigned to the "TARGET" target group.

The TAB-separated OTU matrices each contains a dense matrix of read counts per OTUs in each sample, each row corresponding to an OTU and each column to a sample, the second to last column containing the consensus taxonomic assignment (labeled as “taxonomy”), and the last column listing the representative sequence identifiers (labeled as “repseq”). Taxonomic rank with no consensus assignment are labeled with “unidentified”.

+ SUBPROJECT.json.biom: biom sparse OTU matrix for all OTUs, JSON encoded, with BioProject metadata annotation for samples and taxonomy, assignment bootstraps and function annotations for observations
+ SUBPROJECT.read_counts.tsv: tab separated table of read counts in each sample for each step of the pipeline
+ SUBPROJECT.log: contains the compilation of all logs (standard output and standard error normally printed in the terminal) outputted by the different steps in their called order
+ SUBPROJECT.raw_and_pair-end_reads_statistics.pdf: contains graphical representations of length and quality distributions as well as the average quality over the raw sequences for each sequence library, for both the full library and for each sample in the library.
+ configuration.SUBPROJECT.tsv: the original configuration file
+ demultiplexing_check.csv: Informations on demultiplexing efficiency and potential barcode mismatch for each run/lane library (only for originally non-demultiplexed libraries)


## Troubleshooting

Job execution could be visualized with the grid engine native tool `qstat` (grid Engine) or `squeue` (SLURM).

If a job is waiting in error state or if it dependencies is never satisfied, it means that the job or its preceding job exit due to an error detected inside of the job.

If it concerns the quality step, the most common error is a number of reads below the set limit.

For all other jobs, it means that the previous step unexpectedly terminated before its end or that an error was issued by mothur in the previous step.

In all those cases, check the standard output (.out) and standard error (.err) log files of the respective step(s) and array(s), which are situated in the "Path to execution location"/SUBPROJECT/log directory.

To detect jobs terminated due to overpassing requested memory and/or time, compare the requested memory/time in the problematic step's script with the maximum used memory/runtime during job execution. To print job's record after execution, use `qacct` (grid Engine) or `sacct` (SLURM).

For unsolved issues, send an email to lentendu@rhrk.uni-kl.de, including the .out and .err log files as well as the output of the qacct command for the problematic job.


## References
+ Bengtsson‐Palme Johan, Ryberg Martin, Hartmann Martin, Branco Sara, Wang Zheng, Godhe Anna, Wit Pierre, Sánchez‐García Marisol, Ebersberger Ingo, Sousa Filipe, Amend Anthony, Jumpponen Ari, Unterseher Martin, Kristiansson Erik, Abarenkov Kessy, Bertrand Yann J. K., Sanli Kemal, Eriksson K. Martin, Vik Unni, Veldre Vilmar, Nilsson R. Henrik, Bunce Michael, 2013. Improved software detection and extraction of ITS1 and ITS2 from ribosomal ITS sequences of fungi and other eukaryotes for analysis of environmental sequencing data. Methods in Ecology and Evolution 4, 914–919. doi:[10.1111/2041-210X.12073](http://dx.doi.org/10.1111/2041-210X.12073)
+ Boyer, F., Mercier, C., Bonin, A., Le Bras, Y., Taberlet, P., Coissac, E., 2016. obitools: a unix-inspired software package for DNA metabarcoding. Molecular Ecology Resources 16, 176–182. doi:[10.1111/1755-0998.12428](http://dx.doi.org/10.1111/1755-0998.12428)
+ Edgar, R.C., 2010. Search and clustering orders of magnitude faster than BLAST. Bioinformatics 26, 2460–2461. doi:[10.1093/bioinformatics/btq461](http://dx.doi.org/10.1093/bioinformatics/btq461)
+ Fu, L., Niu, B., Zhu, Z., Wu, S., Li, W., 2012. CD-HIT: accelerated for clustering the next-generation sequencing data. Bioinformatics 28, 3150–3152. doi:[10.1093/bioinformatics/bts565](http://dx.doi.org/10.1093/bioinformatics/bts565)
+ Gaspar, J.M., Thomas, W.K., 2015. FlowClus: efficiently filtering and denoising pyrosequenced amplicons. BMC Bioinformatics 16. doi:[10.1186/s12859-015-0532-1](http://dx.doi.org/10.1186/s12859-015-0532-1)
+ Mahé, F., Rognes, T., Quince, C., de Vargas, C., Dunthorn, M., 2015. Swarm v2: highly-scalable and high-resolution amplicon clustering. PeerJ 3, e1420. doi:[10.7717/peerj.1420](http://dx.doi.org/10.7717/peerj.1420)
+ Martin, M., 2011. Cutadapt removes adapter sequences from high-throughput sequencing reads. EMBnet.Journal 17, 10–12. doi:[10.14806/ej.17.1.200](http://dx.doi.org/10.14806/ej.17.1.200)
+ Masella, A.P., Bartram, A.K., Truszkowski, J.M., Brown, D.G., Neufeld, J.D., 2012. PANDAseq: paired-end assembler for illumina sequences. BMC Bioinformatics 13, 31. doi:[10.1186/1471-2105-13-31](http://dx.doi.org/10.1186/1471-2105-13-31)
+ McDonald, D., Clemente, J.C., Kuczynski, J., Rideout, J.R., Stombaugh, J., Wendel, D., Wilke, A., Huse, S., Hufnagle, J., Meyer, F., Knight, R., Caporaso, J.G., 2012. The Biological Observation Matrix (BIOM) format or: how I learned to stop worrying and love the ome-ome. GigaScience 1. doi:[10.1186/2047-217X-1-7](http://dx.doi.org/10.1186/2047-217X-1-7)
+ Mercier, C., Boyer, F., Bonin, A., Coissac, É., 2013. SUMATRA and SUMACLUST: fast and exact comparison and clustering of sequences.
+ Nguyen, N.H., Song, Z., Bates, S.T., Branco, S., Tedersoo, L., Menke, J., Schilling, J.S., Kennedy, P.G., 2016. FUNGuild: An open annotation tool for parsing fungal community datasets by ecological guild. Fungal Ecology 20, 241–248. doi:[10.1016/j.funeco.2015.06.006](http://dx.doi.org/10.1016/j.funeco.2015.06.006)
+ Rognes, T., Flouri, T., Nichols, B., Quince, C., Mahé, F., 2016. VSEARCH: a versatile open source tool for metagenomics. PeerJ. doi:[10.7717/peerj.2584](http://dx.doi.org/10.7717/peerj.2584)
+ Schloss, P.D., Westcott, S.L., Ryabin, T., Hall, J.R., Hartmann, M., Hollister, E.B., Lesniewski, R.A., Oakley, B.B., Parks, D.H., Robinson, C.J., Sahl, J.W., Stres, B., Thallinger, G.G., Van Horn, D.J., Weber, C.F., 2009. Introducing mothur: Open-Source, Platform-Independent, Community-Supported Software for Describing and Comparing Microbial Communities. Applied and Environmental Microbiology 75, 7537–7541. doi:[10.1128/AEM.01541-09](http://dx.doi.org/10.1128/AEM.01541-09)
+ Wang, Q., Garrity, G., Tiedje, J., Cole, J., 2007. Naive Bayesian classifier for rapid assignment of rRNA sequences into the new bacterial taxonomy. Applied and Environmental Microbiology 73, 5261–5267. doi:[10.1128/AEM.00062-07](http://dx.doi.org/10.1128/AEM.00062-07)
