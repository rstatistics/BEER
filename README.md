<img src="https://github.com/jumphone/BEER/raw/master/DATA/BEER_LOGO.png" width="200">

### BEER: Batch EffEct Remover for single-cell data

Author: Feng Zhang

Date: Mar. 7, 2019


# Requirement:

    #R >=3.5
    install.packages('Seurat')
    install.packages('pcaPP')

# Usage:

### Step1. Load Data

    library(Seurat)
    source('https://raw.githubusercontent.com/jumphone/BEER/master/BEER.R')
    
    #Load Demo Data (subset of GSE70630: MGH53 & MGH54)
    #Download: https://github.com/jumphone/BEER/raw/master/DATA/demodata.zip
    
    D1 <- read.table(unz("demodata.zip","DATA1_MAT.txt"), sep='\t', row.names=1, header=T)
    D2 <- read.table(unz("demodata.zip","DATA2_MAT.txt"), sep='\t', row.names=1, header=T)

    # "D1" & "D2" are UMI matrix (or FPKM, RPKM, TPM, PKM ...; Should not be gene-centric scaled data)
    # Rownames of "D1" & "D2" are gene names
    # Colnames of "D1" & "D2" are cell names 

### Step2. Detect Batch Effect

    mybeer <- BEER(D1, D2, CNUM=10, PCNUM=50, CPU=2)
    
    par(mfrow=c(1,2))
    plot(mybeer$cor, xlab='PCs', ylab='PCC', pch=16)
    plot(-log(mybeer$fdr,10), xlab='PCs', ylab='-log10(FDR)', pch=16)
    
### Step3. Visualization 
    
    pbmc <- mybeer$seurat
    
### Keep batch effect:
  
<img src="https://github.com/jumphone/BEER/raw/master/DATA/KeepBatchEffect.png" width="500">
    
    ALLPC <- 1:length(mybeer$cor)
    
    # UMAP:
    pbmc <- RunUMAP(object = pbmc, reduction.use='pca',dims.use = ALLPC, check_duplicates=FALSE)
    DimPlot(pbmc, reduction.use='umap', group.by='batch', pt.size=0.1)
    #DimPlot(pbmc, reduction.use='umap', group.by='map', pt.size=0.1)
    
    # tSNE:
    pbmc <- RunTSNE(object = pbmc, reduction.use='pca',dims.use = ALLPC, do.fast = TRUE, check_duplicates=FALSE)
    DimPlot(pbmc, reduction.use='tsne', group.by='batch', pt.size=0.1)
    #DimPlot(pbmc, reduction.use='tsne', group.by='map', pt.size=0.1)

#### Remove batch effect:

<img src="https://github.com/jumphone/BEER/raw/master/DATA/RemoveBatchEffect.png" width="500">
  
    PCUSE <- which(mybeer$cor> min(0.7, median(mybeer$cor))  & mybeer$fdr<0.05)
    # Users can set the cutoff of "mybeer$cor" based on the distribution of "mybeer$cor".
    
    # UMAP:
    pbmc <- RunUMAP(object = pbmc, reduction.use='pca',dims.use = PCUSE, check_duplicates=FALSE)
    DimPlot(pbmc, reduction.use='umap', group.by='batch', pt.size=0.1)
    #DimPlot(pbmc, reduction.use='umap', group.by='map', pt.size=0.1)
    
    # tSNE:
    pbmc <- RunTSNE(object = pbmc, reduction.use='pca',dims.use = PCUSE, do.fast = TRUE, check_duplicates=FALSE)
    DimPlot(pbmc, reduction.use='tsne', group.by='batch', pt.size=0.1)
    #DimPlot(pbmc, reduction.use='tsne', group.by='map', pt.size=0.1)
  

# License
    
    MIT License
    
    Copyright (c) 2019 Zhang, Feng

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.

  
