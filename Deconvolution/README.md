# Deconvolution using RCTD
Here we show how to perform cell type deconvolution using RCTD (Robust Cell Type Decomposition).\
The first step is to read in the reference dataset and create a reference object

<details>
  <summary>Step 1: Reading in a Reference Dataset </summary>
  
```
###Step 0: Packages
library(spacexr)
library(Matrix)
library(Seurat) 
##download spacexr is not installed
#library(devtools)
#devtools::install_github("dmcable/spacexr", build_vignettes = FALSE)
###STEP 1: Read in reference dataset
#Read in reference
refr = readRDS("liver_met_ref.rds")
#get cell types of reference dataset
cell_types = Idents(refr)
#drop levels 
cell_types = droplevels(cell_types)
#get raw data matrix
count_raw <- refr@assays$RNA@counts
# make reference dataset
reference <- Reference(count_raw, cell_types = cell_types)
```
</details>

The second step is to read in the spatial data.
<details>
  <summary>Step 2: Reading in Spatial Data </summary>
  
```
###STEP 2: Read in spatial data
#Read in seurat data. In practice, use seurat function to read in data
seurat_object = readRDS("liver_met_seurat.rds")
#get counts matrix 
counts = seurat_object@assays$Spatial@counts
#save gene and cell names for later
genes = colnames(counts)
spots = rownames(counts)
#reformat counts matrix to sparse matrix 
counts = as(counts,'sparseMatrix')
#name column and row names
colnames(counts) = genes
rownames(counts) = spots
#get coordinate matrix
coord = GetTissueCoordinates(seurat_object)
#make spatial puck
puck <- SpatialRNA(coord, counts)
```

</details>

The third step is to create an RCTD object and perform RCTD. Here we set the max number of cell types in a spot to be 4. Please refer to the documentation for information on other parameters

<details>
  <summary>Step 3: Creating an RCTD Object </summary>
  
```
###STEP 3: create an RCTD object
#create an RCTD object. Here we set the max number of cell types in a spot to be 4.
#see documentation for other parameter choices
myRCTD <- create.RCTD(puck, reference, max_cores = 1, UMI_min = 0,MAX_MULTI_TYPES = 4)
#Run RCTD
myRCTD <- run.RCTD(myRCTD, doublet_mode = "multi")
```
</details>

The fourth step is to reformat your RCTD output into a matrix. This matrix will be of dimension #spots by #cell types. Each row will contain the deconvolution estimate for the corresponding spot.

<details>
  <summary>Step 4: Making a Deconvolution Matrix </summary>
  
``` 
###Step 4: Reformat results into a matrix 
#get unique cell types
CT = unique(cell_types)
#initialize the deconvolution matrix 
deconv_est = matrix(0,nrow(coord),length(CT))
#Column names will be cell types
colnames(deconv_est) = CT
#rownames will be spot names
rownames(deconv_est) = rownames(coord)
#iterate over deconvolution results 
for(j in c(1:length(myRCTD@results))){
  #match cell types found to index of unique cell type vector
  fills = match(myRCTD@results[[j]]$cell_type_list,CT)
  #fill in matrix 
  deconv_est[j,fills] = myRCTD@results[[j]]$sub_weights
  #normalize so that rows sum to 1
  deconv_est[j,] = deconv_est[j,]/sum(deconv_est[j,])
}
#final output
deconv_est
```

</details>


For more information, visit the [spacexr](https://github.com/dmcable/spacexr) github page.

# Single Cell Data
If your data is of the single cell resolution, then the deconvolution matrix is a dummy variable matrix. See [this link](https://www.geeksforgeeks.org/dummy-variables-in-r-programming/) for a tutorial on how to make dummy variable matrices. Note that your deconvolution matrix and your average expression profile matrix must have the cell types in the same order.
