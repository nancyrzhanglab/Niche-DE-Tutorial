# Running Niche-DE
Once you have set up your niche_DE object, you can run niche-DE using the function 'niche_DE'. This function takes 5 arguments
+ object: A niche-DE object
+ C: The minimum total expression of a gene across observations needed for the niche-DE model to run. The default value is 150.
+ M: Minimum number of spots containing the index cell type with the niche cell type in its effective niche for (index,niche) niche patterns to be investigated. The default value is 10
+ Gamma: Percentile a gene needs to be with respect to expression in the index cell type in order for the model to investigate niche patterns for that gene in the index cell. The default value is 0.8 (80th percentile)
```{r,warning = F}
NDE_obj = niche_DE(NDE_obj)
```

<details>
  <summary>What does the output look like?</summary>
  
 After running niche-DE, the 'niche-DE' slot in your niche-DE object will be populated. It will be a list with length equal to the length of sigma. Each item of the list contains a sublist with 4 items.
+ T-stat: An array of dimension #cell types by #cell types by #genes. Index (i,j,k) represents the T_statistic corresponding to the hypothesis test of testing whether gene k is an (index cell type i, niche cell type j) niche gene. 
+ Beta: An array of dimension #cell types by #cell types by #genes. Index (i,j,k) represents the beta coefficient corresponding to the niche effect of niche cell type j on index cell type i for gene k.
+ var-cov: An array of dimension (#cell types) squared by (#cell types) squared by #genes. The matrix corresponding to indices (:,:,k) gives the variance covariance matrix of the beta coefficients of the nicheDE model for gene k.
+ log-lik: A vector of length #genes. Index k gives the log-likelihood of the nicheDE model for gene k.
  
Note that each item in the niche-DE list is named based on an element of sigma and the T-stat,beta,var-cov,log-lik items for that list are based on an effective niche calculated using a kernel bandwidth equal to that element of sigma.

 </details>
