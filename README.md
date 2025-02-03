
# m6ABasecaller

<img align="right" height="170" src="/img/logo_m6ABC.jpg">

Welcome to the m6ABasecaller GitHub repo!

The m6ABasecaller allows to directly base-call m6A RNA modifications in **individual reads**, with **single nucleotide resolution**, from direct RNA sequencing nanopore raw FAST5 files without the need of paired 'control' conditions (knockout, knock down, etc). 


## Table of contents
- [General Description](#General-description)
- [Installation](#Installation)
- [Running the code](#Running-the-code)
	- [Option 1: m6A basecalling and modification encoding with Master of Pores](#option-1-m6a-basecalling-and-modification-encoding-with-master-of-pores)
	- [Option 2: Basecalling with m6A basecalling model and modification encoding with modPhred](#option-2-basecalling-with-m6A-basecalling-model-and-modification-encoding-with-modphred)
	- [Option 3: modPhred on-the-fly](#option-3-modphred-on-the-fly)
- [Expected Output](#Expected-output)
- [Citation](#Citation) 
- [Contact](#Contact)


## General Description
The m6ABasecaller is an modification-aware RNA basecalling model that predicts 5 nucleosides (A,C,G,U,m6A) compared to canonical RNA basecalling models, which give as output 4 nuclesodies (A,C,G,U). 

The RNA basecalling model can be used directly with Guppy. In order to extract the m6A modification sites and frequencies, it can be fed to [ModPhred](https://modphred.readthedocs.io/en/latest/), which facilitiates the storage and downstream analysis of RNA modification data from nanopore sequencing datasets. 

This repo includes command line examples and scripts to use the m6A-modification-aware RNA basecalling model (which we refer to as **m6ABasecaller** for simplicity).


## Installation 

Clone this repository in order to have the m6A basecalling model and the scripts for the m6A analysis at per position and per read level.

```
git clone https://github.com/novoalab/m6ABasecaller_dev.git
```

## Running the code
There are 3 possible options to run the code:

 - [Option 1 : Master of Pores Nextflow workflow](#option-1-m6a-basecalling-and-modification-encoding-with-master-of-pores). This is the **recommended** option, as it does not require any software/dependencies to be installed by the user.
- [Option 2: Basecalling with m6ABasecaller model + modification encoding with modPhred](#option-2-basecalling-with-m6A-basecalling-model-and-modification-encoding-with-modphred)
- [Option 3: ModPhred on the fly](#option-3-modphred-on-the-fly)

![scheme_m6ABC_v2-01-01](https://github.com/novoalab/m6ABasecaller_dev/assets/44866316/f4b78c87-9d8c-4d3c-b508-93ef428e2fdb)


### Option 1: m6A basecalling and modification encoding with Master of Pores
This is the **RECOMMENDED** option

All the software you need and the m6A basecalling model are in [Master of Pores](https://biocorecrg.github.io/MoP3/install.html), please follow the "Get Started" instructions to install Master of Pores and guppy (any guppy version between 3.4.5 and 6.0.6 worked in our hands - it does not really matter as you are providing the basecalling model).

#### Step I - Basecalling with mop_preprocess module

The m6A basecalling process will be performed by the mop_preprocess module (see documentation [here](https://biocorecrg.github.io/MoP3/mop_preprocess.html)). 

For m6A basecalling in your ``params.f5.yaml`` file you should specify ``basecalling: "guppy"`` and ``pars_tools: "tool_opts/drna_tool_m6A_splice_opt.tsv" `` so that guppy will use the m6A model. In your output folder you will have the ``fast5_files`` folder containing the m6A basecalled fast5 files for downstream analysis (see example file in Master_Of_Pores_params). Then run: 

```
cd mop_preprocess
nextflow run mop_preprocess.nf -params-file params.f5.yaml -with-singularity -bg > yourlog.txt
```

#### Step II - Modification encoding with mop_mod module

The encoding of modification information will be performed by the mop_mod module (see documentation [here](https://biocorecrg.github.io/MoP3/mop_mod.html)). In the ``params.yaml`` you should specify ``modphred: "YES"`` (see example file in Master_Of_Pores_params). Then run:


```
cd mop_mod
nextflow run mop_mod.nf -params-file params.yaml -with-singularity -bg > yourlog.txt
```

##### Dependencies and versions for Option 1:

Software | Version 
--- | ---
Nextflow | 23.04
Singularity | 3.2.1
Master of Pores | 3
Guppy | tested with 3.4.5 and 6.0.6


### Option 2: Basecalling with m6A basecalling model and modification encoding with modPhred

#### Step I - Basecalling with m6A basecalling model 

Download the basecalling model rna_r9.4.1_70bps_m6A_hac.cfg and place it in your guppy folder at ont-guppy/data/.
You may use this model standalone with Guppy, and then use megalodon or modPhred to extract and process the RNA modification information. Please note that if you use megalodon, your RNA modification information will be encoded in the form of SAM tags. If you use modPhred, your RNA modification information will be encoded in the quality of the FASTQ and BAM (future version of ModPhred will allow to encode modification information directly in SAM tags). In this work, all data was basecalled with Guppy 3.4.5, but other Guppy versions can work as well as the model provided is custom.

We suggest to run it with GPU (highly recommended for a faster computation).

Usage: 
```
guppy_basecaller –i path/to/sample/fast5 –s path/to/sample/output –c ont-guppy/data/rna_r9.4.1_70bps_m6A_hac.cfg
```

#### step II - Modification encoding with modPhred

Here we use the tool [ModPhred](https://modphred.readthedocs.io/en/latest) to encode m6A RNA modification and map the reads, please follow the instructions on how to install modPhred [here](https://modphred.readthedocs.io/en/latest/install.html).


modPhred will generate fastq and fastm files and align them to the reference with minimap2. It will store modification information both in the FASTQ and BAM files, in the QUALITY INFORMATION.

For this option you need to download a singularity image with all the dependecies you need for ModPhred, we suggest to download it inside modPhred folder:

```
cd modPhred
singularity pull docker://lpryszcz/modphred-3.6.1
```

To use modPhred on previously m6A-basecalled fast5 files you can do it as follows (no need for GPU - it is only used for the basecalling step). 

Usage: 
```
-f: your reference.fa file
-o: path to output folder
-i: path to input folders (containing the m6A basecalled fast5 files). You can provide >=1 input folders

singularity exec modPhred/modphred-3.6.1.sif modPhred/run -f reference.fa -o m6A_basecaller -i path/to/sample1_m6Abasecalled_fast5_files  path/to/sample2_m6Abasecalled_fast5_files   path/to/sample3_m6Abasecalled_fast5_files  

```

For more details on how to use ModPhred, please see the [GitHub](https://github.com/novoalab/modPhred) repository and the [ReadTheDocs](https://modphred.readthedocs.io/en/latest/install.html) manual.


##### Dependencies and versions for Option 2:

Software | Version 
--- | ---
Singularity | 3.2.1
Guppy | tested with 3.4.5 and 6.0.6
modPhred | 1.0b


### Option 3: ModPhred on-the-fly
Download the basecalling model rna_r9.4.1_70bps_m6A_hac.cfg and place it in your guppy folder at ont-guppy/data/.
This option is suggested to save space (does not write basecalled fast5 files) and time, but please consider that if you need to run other software that requires basecalled fast5 files (i.e. polyA tail estimation based on nanopolish, which you might want to run for further per read m6A-polyA tail length analysis) it would be better to use options 1/2. 

Here we use the tool [ModPhred](https://github.com/novoalab/modPhred) to base-call, encode m6A RNA modification and map the reads in a simple, and efficient manner, with a single command, making it very simple for the user to use alternative basecalling models. Please follow the instructions on how to install modPhred [here](https://modphred.readthedocs.io/en/latest/install.html).

modPhred performs the base-calling step using Guppy, and here we show how to run it with GPU (highly recommended for a faster computation). The data is stored both in the FASTQ and BAM files, in the QUALITY INFORMATION. Remember that with this option you are not going to save basecalled fast5 files! 

For this option you need to download a singularity image with everything you need for ModPhred, we suggest to download it inside modPhred folder:

```
cd modPhred
singularity pull docker://lpryszcz/modphred-3.6.1
```

Usage: 
```
-c: config for guppy, containing the m6A basecaller model. (for m6A basecaller: rna_r9.4.1_70bps_m6A_hac.cfg)
--host: path to your guppy_basecall_server
-f: your reference.fa file
-o: path to output folder
-i: path to input folders (containing the fast5 files). You can provide >=1 input folders
[--nv is to use singularity in a GPU node]

singularity exec --nv modPhred/modphred-3.6.1.sif modPhred/run -c ont-guppy/data/rna_r9.4.1_70bps_m6A_hac.cfg --host soft/ont-guppy/bin/guppy_basecall_server -f reference.fa -o m6A_basecaller -i path/to/sample1  path/to/sample2  path/to/sample3 

```

For more details on how to use ModPhred, please see the [GitHub](https://github.com/novoalab/modPhred) repository and the [ReadTheDocs](https://modphred.readthedocs.io/en/latest/install.html) manual.

##### Dependencies and versions for Option 3:

Software | Version 
--- | ---
Singularity | 3.2.1
Guppy | tested with 3.4.5 and 6.0.6
modPhred | 1.0b


## Expected Output

In all  3 options, ModPhred will generate fastq and fastm files and align them to the reference with minimap2. It will store modification information both in the FASTQ and BAM files, in the QUALITY INFORMATION.

modPhred will also produce a ``mod.gz`` output file that contains all the sites that were found with at least 25 reads of coverage and at least 5% modification frequency in one sample, with information about coverage, modification probability, modification frequency and basecalling accuracy for each sample. For more information on the output see [https://modphred.readthedocs.io/en/latest/output.html](https://modphred.readthedocs.io/en/latest/output.html)

Once you have the ``mod.gz file``, you can proceed to analysis at per-site and per-read level. 


## Citation
  
If you find this work useful, please cite: 

Cruciani S*, Delgado-Tejedor A*, Pryszcz LP*, Medina R, Llovera L and Novoa EM. De novo basecalling of m6A  modifications at single-molecule and single-nucleotide resolution. BioRxiv 2023 (under review). 
  
## Contact
If you have any issues running this code, please go first over previous [issues](https://github.com/novoalab/m6ABasecaller/issues). If you still can't figure it out based on the prior responses/issues raised, please open a new issue. Thanks!   
