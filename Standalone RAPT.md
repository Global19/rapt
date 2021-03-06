# Standalone RAPT – Documentation

This page contains instruction and examples for running RAPT.`run_rapt.py` is a python script that provides an interface to run the *Standalone RAPT* on a local machine. "Local" means the same machine as where `run_rapt.py` is executed. It could be a physical machine on premise, or more conveniently, a cloud VM instance.
Some basic knowledge of Unix/Linux commands, [SKESA](https://github.com/ncbi/SKESA), and [PGAP](https://github.com/ncbi/pgap) is useful in completing this tutorial.
Please see our [wiki page](https://github.com/ncbi/rapt/wiki) for References, Licenses, FAQs, and In-depth Documentation and Examples. 


## System requirements

The machine must satisfy the following prerequisites: 

•	At least 4GB memory per CPU core  
•	At least 8 CPU cores and 32 GB memory  
•	Linux OS preferred, Windows 10 (pro or enterprise version) will also work but extra configuration is required  
•	Internet connection  
•	Container runner installed (currently supports Docker/Podman/Singularity), Docker is recommended  
•	Python installed  
•	100GB free storage space on disk  


### Additional tips if using Windows 10 (pro/enterprise version)
1.	Right now it seems to only work on a real physical machine (L0, metal) with CPUs support virtualization (Like INTEL VT-x technology); Make sure this feature is enabled in BIOS
2.	Windows 10 only, must be at least Professional or Enterprise version (hypervisor capability)
3.	Install python and Docker Desktop
4.	Start Docker service with hyper-V enabled
5.	Make sure Docker has switched to 'Linux containers'. It should do so by default if hyper-V is up and running.

## Quick start
Here are instructions to execute RAPT once your system is set up. Additional instructions are available on our [wiki page](https://github.com/ncbi/rapt/wiki/Standalone%20RAPT%20In-depth%20Documentation%20and%20Recommendations.md). 
1.	Go to your machine or instance command line
2.	Download the latest release by executing the following commands:

```
~$ curl -sSLo rapt.tar.gz https://github.com/ncbi/rapt/releases/download/v0.2.0/rapt-v0.2.0.tar.gz
~$ tar -xzf rapt.tar.gz && rm -f rapt.tar.gz
```
3.	Run `./run_rapt.py -h` to see the *Stand-alone RAPT* usage information


### Try an example 
To run RAPT, you need Illumina-sequenced reads for the genome you wish to assemble and annotate. These can be in a fasta file on the machine where you wish to run RAPT, or they can be in a run in the NCBI Sequence Read Archive (SRA).  
Important: Only reads sequenced on **Illumina machines** can be used by RAPT. 

#### Starting from an SRA run   
To demonstrate how to run RAPT, we are going to use SRR3496277, a set of reads available in SRA for *Mycoplasma pirum*.  
This example takes about 1 hour to complete (time may vary depends on the configuration of the computer).

Run the following command, the outputs and logs will be located in the current directory when the job finishes.

```
~$ ./run_rapt.py -a srr3496277
RAPT is now running, it may take a long time to finish. To see the progress, track the verbose log file /path/to/current_dir/raptout_e26d552147/verbose.log.
~$ 
```

All output files and logs will be located in a subdirectory named `raptout_xxxxxxxxxx` under current directory. `xxxxxxxxxx` is the RUNID generated by `run_rapt.py`, unique to each time it is launched. Please note that some runs may take up to 24 hours.

#### Starting from fastq or fasta file   
You can use a fastq or a fasta file produced by Illumina sequencers as input to RAPT. This file can contain paired-end reads, with the two reads of a pair adjacent to each other in the file or single-end reads. Note that the quality scores are not necessary. The file needs to be on the local file system.
The genus species of the sequenced organism needs to be provided on the command line. The strain is optional.
Here is an example command using a file already on your computer:

```bash
~$ ./run_rapt.py -q path/to/srr3496277.fastq --organism "Mycoplasma pirum" --strain "ATCC 25960"
RAPT is now running, it may take a long time to finish. To see the progress, track the verbose log file /home/username/raptout_d3e7956148/verbose.log.
~$ 
```
 
To get more execution details and examples, see our [wiki page](https://github.com/ncbi/rapt/wiki/Standalone%20RAPT%20In-depth%20Documentation%20and%20Recommendations.md). 
- Help Documentation  
- Reference data location  
- Advanced Options

If you have other questions, please visit our [FAQs page](https://github.com/ncbi/rapt/wiki/FAQ.md).

### Review the output  

RAPT generates 11 output files if completes normally without error.  The default location of result output is in the current directory. Each run of RAPT will create a subdirectory bearing the name raptout_<RUNID> where <RUNID> is a random 10-character string. The --tag JOBID switch can be used to specify a human-readable job id which will be appended after the random RUNID for easy recognition.

To store the output in location other than the current directory, use the -o or --output-dir switch to specify the desired location:
```bash
~$ ./run_rapt.py -q path/to/srr3496277.fastq --organism "Mycoplasma pirum" --strain "ATCC 25960" --output-dir path/to/output-dir
```

All messages from RAPT execution are logged, with time stamps, in a file named `verbose.log` in the output directory. A simpler version log file, `concise.log`, is also created with only entries mark the main stages and status. Below is the list of expected output files:

1. concise.log  
2. verbose.log
3. skesa.out.fa: multifasta files of the assembled contigs produced by SKESA   
4. ani-tax-report.txt and ani-tax-report.xml: Taxonomy verification results in text or XML format   
5. PGAP annotation results in multiple formats:   
   * annot.gbk: annotated genome in GenBank flat file format     
   * annot.gff: annotated genome in GFF3 format     
   * annot.sqn: annotated genome in ASN format     
   * annot.faa: multifasta file of the proteins annotated on the genome   
   * annot.fna: multifasta file of the trancripts annotated on the genome   
   * calls.tab: tab-delimited file of the coordinates of detected foreign sequence. Empty if no foreign contaminant was found.

See a [detailed description of the annotation output files](https://github.com/ncbi/pgap/wiki/Output-Files) for more information.