# Workshop of Human Cell Atlas

## Schedule 

| Title                                                     | Speaker          | Day    | Time (GMT-5) | Notes |
|-----------------------------------------------------------|------------------|--------|--------------|-------|
| [Getting started with scRNA-seq analyses with Bioconductor](https://lcolladotor.github.io/HCA_LA_2021/) | [Leonardo Collado](https://lcolladotor.github.io/es/) | Monday | 9:00 am      |  [Additional slides](https://docs.google.com/presentation/d/1CgBtdoE5GAhbqAaGzxR5HEkUTbXNZsKEfym90996vWQ/edit#slide=id.gd6ceee5608_0_1874)     |
|                                                           |                  |        |              |       |
|                                                           |                  |        |              |       |

## Setup Workplace Server 

This workshop will be run in a UNAM server. Please check [additional information](https://github.com/HCA-LatAm/Workshop-2021/tree/main/Mexico/docs/HCA-Accesos.pdf)

## Download Data

### SELECTING DATASETS

We will analize single-cell RNA-seq (scRNA-seq) data from [Lambrechts D, Wauters E, Boeckx B, Aibar S, Nittner D, Burton O, Bassez A, Decaluwé H, Pircher A, Van den Eynde K, Weynand B. Phenotype molding of stromal cells in the lung tumor microenvironment. Nature medicine. 2018 Aug;24(8):1277-89.](https://www.nature.com/articles/s41591-018-0096-5#MOESM1). It is a from lung cancer patients. 

Raw data from the 11 scRNA-seq datasets can be downloaded from SRA under [E-MTAB-6149](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-6149/).


### DOWNLOAD FASTQ

#### **Option 1**: *ArrayExpress*

> We can also download `fastq` files from [ArrayExpress repoitory](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-6149/). 


#### **Option 2**:  *SRA toolkit*


> #### 1. SRA toolkit installation 
> 
> We used [SRA toolkit](https://www.ncbi.nlm.nih.gov/sra/docs/sradownload/) > following the [wiki docs](https://github.com/ncbi/sra-tools/wiki).
> 
> ##### 2. Download samples  
> 
> 
> - We obtained the `SRR_Acc_List.txt` file which contians the samples ids to download from [SRA Run Selector](https://www.ncbi.nlm.nih.gov/Traces/study/?query_key=3&WebEnv=MCID_6081c0ecac031921e63d4573&o=acc_s%3Aa). 
> Download `SRR_Acc_List.txt` by clicking > "Accession List".
> 
> - We downloaded samples using `prefetch` following [this tutorial](https://erilu.github.io/python-fastq-downloader/) recommendations  about `--max-size` parameter. 
> 
> ```bash
> prefetch --option-file SRR_Acc_List.txt --max-size 44G
> ```
> 
> - We then used `fastq-dump` using paralelization following [bioinformatics workbook tutorial](https://bioinformaticsworkbook.org/dataAcquisition/fileTran> sfer/sra.html#gsc.tab=0) and [EdwarsLab tutorial](https://edwards.sdsu.edu/research/fastq-dump/). 
> 
> ```bash
> cat SRR_Acc_List.txt | parallel -j 80 fastq-dump -I --split-files --skip-technical > --readids --dumpbase --clip --split-3 --gzip --outdir out/ {}
> ```
>
> - We checked the file size. `ls -l --block-size=G`


---

## CellL Ranger

### INSTALL CELL RANGER

We followed the installation process for [`Cell Ranger`](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation).

#### 1. Download Cell Ranger

We downloaded `cellranger-6.0.1.tar.gz` from the [webpage](https://support.10xgenomics.com/single-cell-gene-expression/software/downloads/latest).

#### 2. Setup

- We moved to the path where `cellranger-6.0.1.tar.gz` was downloaded and unpacked it. 

```bash
cd $DIR

curl -o cellranger-6.0.1.tar.gz "https://cf.10xgenomics.com/releases/cell-exp/cellranger-6.0.1.tar.gz?Expires=1619089443&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZi4xMHhnZW5vbWljcy5jb20vcmVsZWFzZXMvY2VsbC1leHAvY2VsbHJhbmdlci02LjAuMS50YXIuZ3oiLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE2MTkwODk0NDN9fX1dfQ__&Signature=b8LF05IxnJuHM-vX5Ljv7QTUApgW~MvXtABCH8DpuATXkyVsLuQCVqAYKBS5wFZpZ5o2KPFtRhSv63~X7vJfnMTGsynLHUC3A-KEQQBnert8bKCMQqoUUMbsC3wz8LFRB3NYyFvbdRYrPnrPSwhbtozDbrMb3DYlZr9ZuCTIqNYSkXzASsKkoBbwBR-Jc9rGZsDpAH1tfVa~02pRus4~K3CGR-UHkQxhhSoadjjyH3C2kNU-NVEGA~D5moos9v3iUX28H72sq0Q5atXJIkynyzoQE0M-rRbbZbY6CHAV0Ld0DGcMMZmpPUqEOoD9zcD7cXi12ncWcV0-NkZgEDluNA__&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA"

tar -xzvf cellranger-6.0.1.tar.gz
```

- We added Cell Ranger to our `$PATH` which allows us to invoke `cellranger`. Check your `.bashrc` to verify the `$PATH` was added. 

```bash
export PATH=/"$DIR"/cellranger-6.0.1:$PATH
```

- We moved to the reference directory, downloaded and unpacked `refdata-gex-GRCh38-2020-A.tar.gz´.

```bash
curl -O https://cf.10xgenomics.com/supp/cell-exp/refdata-gex-GRCh38-2020-A.tar.gz

tar -xzvf refdata-gex-GRCh38-2020-A.tar.gz

```

### RUN CELL RANGER

#### 1. Rename files

Option 1:

```bash
mv $SAMPLE'.R1.fastq.gz'  $SAMPLE'_S1_L001_R1_001.fastq.gz'
mv $SAMPLE'.R2.fastq.gz'  $SAMPLE'_S1_L001_I1_001.fastq.gz'
mv $SAMPLE'.R3.fastq.gz'  $SAMPLE'_S1_L001_R2_001.fastq.gz'
````

Option 2:

```bash
mv $SAMPLE'_R1.fastq.gz'  $SAMPLE'_S1_L001_R1_001.fastq.gz'
mv $SAMPLE'_R2.fastq.gz'  $SAMPLE'_S1_L001_R2_001.fastq.gz'
````


#### 2. Run Cell Ranger

--id = Sample name and output directory
--sample = Sample name
--transcriptome = Path to reference transcriptome
--fastqs = Path to fastq directory
--localcores = Number of cores to use
--localmem = Amount of memory to use

```bash
cellranger count --id=$SAMPLE --sample=$SAMPLE --transcriptome=/opt/refdata-gex-GRCh38-2020-A --fastqs=fastqs/$SAMPLE --localcores=50 --localmem=300
````

---

## Cell - Cell communication Pipeline 

https://github.com/earmingol/singlecell-ccc



