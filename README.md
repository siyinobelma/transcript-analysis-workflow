# transcript-analysis-workflow
1. Installation
1.1 Install Docker
Since this workflow is based on the workflow management system Snakemake and Docker, you need to install Docker on your server first.

1.2 Start the Container Containing the Workflow
Pull the Docker image:

docker pull siyinobelma/hisat-snake:2.0
Create and run the container in the background:

docker run -itd --name RNA_snakemake siyinobelma/hisat-snake:2.0 bash
# RNA_snakemake is the container name, which can be changed according to user preferences.
Enter the container:

docker exec -it RNA_snakemake bash
2. Prepare Input Files
Running the workflow requires multiple input files: a genome file (.fa), an annotation file (.gtf), compressed sequencing files (.fastq.gz), a workflow configuration file (.yaml), and a sample information file (.csv).

File Type	Description
genome.fa	Genome file containing genomic sequences provided by the user
annotation.gtf	Genome annotation file provided by the user
sequence.fastq.gz	Compressed sequencing file provided by the user
config.yaml	Configuration file for customizing the workflow
info.csv	Sample information file

2.1 annotation.gtf and genome.fa
It is recommended to retrieve the genome and annotation files for the organism from the National Center for Biotechnology Information (NCBI). Note: If you are using a custom annotation file, please ensure it complies with the GTF standard. If it is in GFF format, please convert it to GTF format.

2.2 Input Files (.fastq.gz)
These are the input files provided by the user, supporting paired-end sequencing data. Note: Please ensure that the files are compressed in .gz format and have the .fastq.gz suffix.

2.3 Configure the Configuration File
Modify the sample information table (/home/configs/info.csv):

id	condition
SRR23999230,eco
SRR23999231,eco
SRR23999232,endo
SRR23999233,endo

Customize the workflow in /home/configs/config.yaml according to your needs. It contains the following variables:

PROJECT: Project name
SAMPLES: [Names of sequencing files]
GENOME: Path to the genome file
ANNOTATION: Path to the annotation file
INPUTPATH: Path to the input file directory
OUTPUTPATH: Path to the output file directory
THREAD: Number of threads to run the software
INFO: Sample information table
CONTROL: [Control group condition]
TREAT: [Treatment group condition]
SPECIES: Species name
3. Run the Workflow
Run in the /home/run directory:
snakemake -s run.py -j 6          # The -j parameter indicates the number of tasks to run simultaneously, which can be modified according to the situation.
# Run in the background:
nohup snakemake -s run.py -j 6 &
Note: Running run.py directly yields gene expression levels (FPKM, TPM, count matrices), and results of differential gene analysis from sequencing data; running exp.py directly yields gene expression levels from sequencing data without differential analysis; running deg.py requires prior execution of either run.py or exp.py. Running deg.py will perform differential analysis on the count matrices obtained from run.py or exp.py based on the treatment and control groups set in the config.yaml configuration file.

4. Example Data
Example sequencing data source: Persea americana (ID 3435) - BioProject - NCBI (nih.gov) with the SRR numbers: "SRR23999230", "SRR23999231", "SRR23999232", "SRR23999233".

Genome and annotation file source: bioinfor.kib.ac.cn/RPGD/download_genome.html for Persea americana Download Version T2T.
