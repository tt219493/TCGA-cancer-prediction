# Predicting Cancer Type from TCGA RNASeq Data
Selecting TCGA data from [ISB-CGC](https://portal.isb-cgc.org/) through Google BigQuery and using neural networks to predict cancer type resulting in 90+% accuracy.

**Main Purposes**: 
* Gain experience with SQL queries on large real-world dataset
* Gain experience with pre-processing large amounts of data and feeding it to `Pytorch Lightning` module
* Replicate research paper methodologies

Workflow available in `notebooks/*.ipynb` done in Google Colab
___
Methodology and Pipeline
---
[Research](https://pmc.ncbi.nlm.nih.gov/articles/PMC8909043/) by Divate et al. (2022) was used as a baseline reference as they worked with data I was interested in.

### Data and Querying
**Data:** ISB-CGC   
* RNASeq Data with gene expression levels from The Cancer Genome Atlas (TCGA)  
* Exceptional Responders (ER) RNASeq Data  was used first as practice on a small dataset.

**Querying:** SQL in BigQuery  
* Filtering for only protein-coding genes
* Filtering for genes that expressed $\geq$ 5 FPKM across at least 50% of samples in any cancer type
* Pivoting table so each sample was a row and each column was the gene and its FPKM expression value   
    * Implemented with `Pandas` since BigQuery doesn't allow tables with >10,000 columns
* Splitting up data into 4 separate tables (for TCGA)
    * Size of full table was too large for Colab

Ultimately resulted in 33 cancer types, 11,499 samples, and 12,555 genes

### Pre-Processing
**Transforms:** 
* $Log_{10}(x+1)$ was applied to all FPKM values for normalization
* T-SNE was utilized as unsupervised training to show that there is underlying structure connecting cancer type and gene expression values
    * Log transformed data also had much better clusters 

### Model Training and Prediction
* A simple neural network model similar to the architecture from Divate et al. (2022) was implemented using `PyTorch Lightning` rather than `TensorFlow` like in the original paper
    * Created simple Logger to be able to graph losses and accuracy over time
* Similar hyperparameters to the paper were used as well.
* Model was trained sequentially on each of the 3 separate splits of data and the 4th was used as testing data. 

**Final accuracy:** 91.9% for TCGA testing data
___
Discussion
---
* Training on full set of data or different models on the different splits might improve model
* Data I gathered differed from the data gathered by the original paper which could explain differences in results
* Gene expression data for so many genes seems to be very informative about the cancer type
    * Thus, changes to model architecture are unlikely to improve predictions by too much, considering a simple model already has 90+% accuracy

* Can use gene expression data for other types of predictions or utilize only the most informative genes for cancer prediction

References
--- 
> Divate M, Tyagi A, Richard DJ, Prasad PA, Gowda H, Nagaraj SH. Deep Learning-Based Pan-Cancer Classification Model Reveals Tissue-of-Origin Specific Gene Expression Signatures. Cancers (Basel). 2022 Feb 24;14(5):1185. doi: 10.3390/cancers14051185. PMID: 35267493; PMCID: PMC8909043.

>  David Pot, Zelia Worman, Alexander Baumann, et al.
NCI Cancer Research Data Commons: Cloud-based Analytical Resources
_Cancer Res._ 2024 May;84(9):1396–1403 10.1158/0008-5472.CAN-23-2657 

>  Sheila M. Reynolds, Michael Miller, Phyliss Lee, et al.
The ISB Cancer Genomics Cloud: A Flexible Cloud-Based Platform for Cancer Genomics Research
_Cancer Res_ 2017 Nov;77(21): e7–10. 10.1158/0008-5472.CAN-17-0617 

