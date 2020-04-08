---
title: "Create Seurat Object for test"
author: 'User ID: ay1392'
date: "2020-04-08"
output:
  html_document:
    fig_height: 7
    fig_width: 9
    keep_md: yes
    toc: yes
    df_print: paged
params:
  data_path: "~/aifantislab/scooterRMD/data" # path to the directory with the outs file
  out_path: "." # output path where the outputs will be stored
  sample_name: "test"   # name of the current sample 
  hashtag_ID: NULL # names of the hashtags (if the hashtags are NOT separate from the gene expression data)
  hashtags_to_keep: NULL # names of the hashtags to keep (if the hashtags are NOT separate from the gene expression data)
  hashtag_file: NULL # path to hashtag file (if it is separate from the gene expression data)
  ADT_file: NULL # path to ADT file if (it is separate from the gene expression data)
  sample_names: 
    value:
      # Hashtag1-GTCAACTCTTTAGCG: "hash1"
  ADT: FALSE   # logical value. TRUE if there are ADTs in the 10x outs file, FALSE if there are not
  assay: "RNA"   # Assay we are working with
  grouping: "orig.ident" # grouping for plots
  hash_grouping: "hash.ID" # grouping for plots hashtags but doesnt work with anythin else right now
  min_genes: 50 # minimum number of genes per cell
  max_genes: 6000 # maximum number of genes per cell 
  max_mt: 15  # maximum percent mitochondrial reads per cell 
  normalize_method: "log_norm" # normalization method. One of log_norm or sct
  nfeatures: 2000 # number of variable features to extract
  num_pcs: 30 # number of PCs to calculate
  num_dim: [20, 25] # number of PCs to use in UMAP
  num_neighbors: [20, 10] # number of neighbors to use in UMAP
  prefix: "lognorm"   # prefix to add to UMAP. Useful if you are doing different normalizations, or using different subsets of the data
  log_file: "log" # log file
---



# Preprocessing single cell RNA sequencing data

This markdown file contains the code to download and preprocess single cell RNA sequencing data, mostly using Seurat. To complement the code and my comments, I have compiled material from other single cell RNA sequencing resources.






# Create RMD

The output of this file is a directory called Create-test within which there will be a params.yml that saves the parameters from this file, and a log file. 

If you choose to use the cache = TRUE parameter, rendering this file may be quicker the second time around, but please be aware that when you run the file a second time, the cached code chunk will not run again unless you change something *within* the code chunk. So, if you make changes *upstream* of the cached code chunk, the cached code chunk will not take that into account and output the cached result. 

# Read in 10x data



Curently, features, i.e genes, hashtags, and CITE-Seq antibodies, are labelled as "Gene Expression" or "Antibody Capture". We collect all of the data corresponding to Gene Expression and put it in one matrix, and all of the data corresponding to Antibody Capture, and put it in another matrix. We then store these two matrices in the list `counts_mat`. 

We select the "Gene Expression" matrix from  our list `counts_mat` and use it to create a Seurat object. Seurat objects are data containers with a specific structure. @@@ DRAW HERE@@ 



Some experiments use hashtags to multiplex the data. The following code will be run if `hashtag_ID`s were specified in the parameter section. ADT data will also be processed if `ADT` is set to TRUE. 









The seurat object (no filtering) was saved as ./Create-test//seurat_obj_raw.rds

# Demultiplex data 

If there are hashtags, we demultiplex the cells, and plot the hashtag data. We also remove cells that are labeled "Singlets" or "Doublets"



If there are hashtags, and `hashtags_t_keep` was set, we only use these selected hashtags downstream: 



Here we have the opportunity to rename our hashtags to cleaner sample names using the list `sample_names` set above.



# Quality control

We use the number of unique molecular counts (UMIs), number of expressed genes, and the percent of counts that are from mitochondrial RNA to filter low quality cells. A cell with few UMIs and number of expressed genes but high percent of mitochondrial counts indicates that the  cell might be sheared, causing the cytoplasm to leak out of the cell leaving only the protected mRNA in the mitochondria. On the other hand, cells with high number of UMIs and high number of genes expressed may indicate a doublet [@Luecken2019].

To vizualize the quality of the cells, we plot violin plots of the number of genes, number of UMIs, and percent mitochondrial reads per cell. 

![](./Create-test/unfiltered_violin_qc-1.png)<!-- -->



The violin plots are great for getting an idea about what is going on for each of these variables on their own. Now we take a look at the relationship between these variables. 

We plot the number of UMIs, the number of genes, adn the percent mitochondria against each other. This is one way we can start to see if higher mitochondrial percentage is biological or technical. 

![](./Create-test/unfiltered_paired_qc-1.png)<!-- -->

We also plot the number of expressed genes per cell in relation to the the total number of aggregated counts per cell. This plot sheds light on the spread of the counts per cell. Are there few genes that have many counts? Are there many genes that have few counts?

![](./Create-test/cell-activity-1.png)<!-- -->

We are also curious about the quality of each gene. By plotting the percent of cells that express a certain gene against the mean of that gene we can see whether a gene is expressed in many cells or in few cells. 

![](./Create-test/freq-mean-gene-1.png)<!-- -->

# Filtering

We filter the data using the hard cutoffs set in the parameters for minimum and maximum number of genes a cell may express, as well as the maximum mitochondrial percentage a cell can express. 
If the thresholds are not provided, cells with the most number of genes expressed (98%) and the least number of genes expressed (2%), and cells with greater that 10% mitochondrial counts will be removed.



![](./Create-test/filtered_qc-1.png)<!-- -->



![](./Create-test/filtered_paired_qc-1.png)<!-- -->

![](./Create-test/filtered-cell-activity-1.png)<!-- -->

![](./Create-test/filtred-freq_mean-1.png)<!-- -->


If there are CITE-Seq antibodies, we also plot the percent of cells that express a certain ADT against the mean of that ADT we can see whether a ADT is expressed in many cells or in few cells. 



# Normalizing the data

We normalize the data using log_norm. The point of normalizing the data is to make the data less skewed.



# Dimensionality Reduction

We run PCA using the most variable genes. 



We run UMAP using the PCs calculated by PCA and parameters specified in the parameter section.



We save metadata as a csv to ./Create-test//metadata_create_test.csv


We plot dimensionality reductions that we calculated.
![](./Create-test/plotdr-1.png)<!-- -->![](./Create-test/plotdr-2.png)<!-- -->![](./Create-test/plotdr-3.png)<!-- -->![](./Create-test/plotdr-4.png)<!-- -->![](./Create-test/plotdr-5.png)<!-- -->![](./Create-test/plotdr-6.png)<!-- -->![](./Create-test/plotdr-7.png)<!-- -->![](./Create-test/plotdr-8.png)<!-- -->![](./Create-test/plotdr-9.png)<!-- -->![](./Create-test/plotdr-10.png)<!-- -->![](./Create-test/plotdr-11.png)<!-- -->![](./Create-test/plotdr-12.png)<!-- -->![](./Create-test/plotdr-13.png)<!-- -->![](./Create-test/plotdr-14.png)<!-- -->![](./Create-test/plotdr-15.png)<!-- -->![](./Create-test/plotdr-16.png)<!-- -->![](./Create-test/plotdr-17.png)<!-- -->![](./Create-test/plotdr-18.png)<!-- -->![](./Create-test/plotdr-19.png)<!-- -->![](./Create-test/plotdr-20.png)<!-- -->


```
##  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## [13] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## [25] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## [37] FALSE FALSE FALSE FALSE
```

Save rds to ./Create-test//seurat_obj.rds


# References

---
references:
- id: Luecken2019
  title: Current best practices in single‐cell RNA‐seq analysis: a tutorial
  author:
  - family: Luecken
    given: Malte
  container-title: Mol Sys Biol
  volume: 15
  URL: 'https://doi.org/10.15252/msb.20188746'
  DOI: 10.1038/nmat3283
  issue: 6
  publisher: EMBO Press
  type: article-journal
  issued:
    year: 2019
    month: 06
---
