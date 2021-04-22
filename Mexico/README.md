# Workshop of Human Cell Atlas

## Download Data

### SELECTING DATASETS

We will analize single-cell RNA-seq (scRNA-seq) data from [Chen, J., Tan, Y., Sun, F., Hou, L., Zhang, C., Ge, T., ... & Zhang, P. (2020). Single-cell transcriptome and antigen-immunoglobin analysis reveals the diversity of B cells in non-small cell lung cancer. Genome biology, 21(1), 1-21.](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-02064-6). It is a from lung cancer patients. 

Raw data from the 11 scRNA-seq datasets can be downloaded from SRA under [PRJNA634159](https://www.ncbi.nlm.nih.gov/sra/PRJNA634159).


### DOWNLOAD FASTQ

#### **Option 1**: *SRA toolkit*
 
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

#### **Option 2**: *ENA*

> We can also download `fastq` files from [ENA repoitory](https://www.ebi.ac.uk/ena/browser/view/PRJNA634159). 



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


---

## Cell - Cell communication Pipeline 

https://github.com/earmingol/singlecell-ccc



