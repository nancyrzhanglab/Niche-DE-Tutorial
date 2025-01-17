
Starting with a spatial seurat object, we can make a niche-DE object with the function 'CreateNicheDEObjectFrom Seurat'. This function takes in 5 arguments
<details>
  <summary>Arguments</summary>
  
  + seurat object: A seurat object
  + assay: The assay from which to retrieve the counts matrix. Will be taken from slot 'counts'
  + library mat: The average expression profile matrix calculated from a reference dataset
  + deconv mat: The deconvolution matrix for the spatial dataset
  + sigma: A list of kernel bandwidths to use for effective niche calculation
  
  </details>

```
#read in seurat object
seurat_obj = readRDS('liver_met_seurat.rds')
#download average expression profile matrix 
data("vignette_library_matrix")
data("vignette_deconv_mat")
#make niche-DE object
NDE_obj = CreateNicheDEObject(seurat_obj,'Spatial',vignette_library_matrix,vignette_deconv_mat,sigma = c(1,15,40))

```

<details>
  <summary>What's in the object?</summary>

We see that their are 14 slots, 10 of which are populated when making the nicheDE object. Here we will explain what each slot should contain, except for the ones prefixed by niche_DE.
+ counts: The RNA count data of the spatial transcriptomics dataset. The dimension will be #cells/spots by #genes.Genes are filtered out if they do not exist within the scrna-seq reference dataset.
+ coord: The spatial coordinates matrix of the spatial transcriptomics dataset.
+ sigma: The kernel bandwidth(s) chosen for calculating the effective niche. Recommended values will be discussed shortly.
+ num_cells: A #cells/spots by #cell types matrix indicating the estimated number of cells of each cell type in each spot. 
+ effective_niche: A list whose length is equal to the length of sigma. Each element of the list is a matrix of dimension #cells/spots by #cell types that measures how many of each cell type is in a given cell/spot's neighborhood. For more information, please read the manuscript.
+ ref_expr: The average expression profile matrix. The dimension is #cell types by #genes. Each row gives the average expression of each gene for a given cell type based on the reference dataset supplied.
+ null_expected_expression: The expected expression profile for each cell/spot given its cell type deconvolution and library size. It is of dimension #cells/spots by #genes.
+ cell_names: The name of each cell. This will be used if the use wants to filter cells via the function 'Filter'
+ gene_names: The gene names.
+ batch_ID: The batch ID for each cell/spot. This will be used when merging objects.
+ spot_distance: The mean distance between a cell/spot and its nearest neighbor.This value can be used to inform the choice of sigma.

</details>
