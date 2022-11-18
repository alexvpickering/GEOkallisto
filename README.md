# Installation

`rkal` can be installed as follows:

```{r, eval = FALSE}
remotes::install.packages('alexvpickering/rkal')
```

# rkal

`rkal` requires that `kallisto` is installed and available on the command
line. Please see [here](https://pachterlab.github.io/kallisto/download.html)
for instructions. To verify that `kallisto` is available, the following can be
run from R:

```R
system('kallisto')
```

# Getting Started using rkal

Prior to pseudoalignment, an index of the transcriptome must first be built:


```{r}
#This will build the human Ensembl94 index for kallisto in the working directory
#this only needs to be run once
indices_dir <- getwd()
build_kallisto_index(indices_dir = indices_dir,
                     species = 'homo_sapiens', release = '94')
```

Next, we download an example fastq file with [`GEOfastq`](https://github.com/alexvpickering/GEOfastq).
Skip this step if you have your own fastq files:

```{r}
library(GEOfastq)
data_dir <- tempdir()

# first get metadata needed and download example fastq file
srp_meta <- crawl_gsms('GSM4875733')
res <- get_fastqs(srp_meta, data_dir)
```

Next we collect fastq file metadata needed to run pseudoalignement (are fastq 
files paired or single-end? Are there any replicates aka samples split across multiple files?):

```{r}
# we can get the necessary metadata data.frame for fastqs from GEOfastq
quant_meta <- get_quant_meta(srp_meta, data_dir)

# for personal fastq files, a GUI will request this info in the next step
# you can also create it programatically
# (see required columns in `quant_meta` description of `?run_kallisto_bulk`)

```


We are now ready to run pseudoalignment:

```{r}
# can exclude quant_meta for personal fastqs (will invoke GUI)
res <- run_kallisto_bulk(indices_dir, data_dir, quant_meta)
```
If you plan to use `crossmeta` or `dseqr`, you can easily generate a suitably
annotated `ExpressionSet`:

```{r}
eset <- load_seq(data_dir)
```
