## In this folder we provide the results of the analysis performed with the m6ABasecaller: 

* PerPosition folder contains tables with information about m6A position and modification frequency in:

  - mouse ES cells (data downloaded from ELIGOS publication) upon METTL3 and METTL14 KO (mESC_ELIGOS_source_data.txt)
  
  - mouse ES cells with METTL3 KO induced with 6 days or 14 days of tamoxifen treatment (mESC_tam6d_source_data.txt and mESC_tam14d_source_data.txt
 
  - mouse ES cells treated with increasing concentration of METTL3 inhibitor STM2457
 
* PerRead folder contains tables with information about m6A frequency calculated at
  - per gene level, in 3 replicates (*_PerGene.tsv)
    
  - per isoform level, in 3 replicates (*_PerIsoform.tsv)
    
  - for each position shared by two or more isoforms, difference in modification frequency between each pair of isoforms, in 3 replicates (DiffModFreq_PerIsoform_*)

  
