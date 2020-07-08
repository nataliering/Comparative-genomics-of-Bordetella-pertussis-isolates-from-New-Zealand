# Comparative-genomics-of-Bordetella-pertussis-isolates-from-New-Zealand
Code and commands used in our manuscript, "Comparative genomcis of Bordetella pertussis isolates from New Zealand, a country with an uncommonly high incidence of whooping cough"


## Commands for tools mentioned in manuscript

### Assigning allele type for ACV genes (using custom-made bpertussis ACV genes mlst scheme)
**[mlst](https://github.com/tseemann/mlst)**  
`mlst --legacy --csv --scheme bpertussis input_genomes.fasta > mlst_results.csv `

**[tfa_prepper](https://github.com/nataliering/Comparative-genomics-of-Bordetella-pertussis-isolates-from-New-Zealand/blob/master/tfa_prepper)**                                 
`tfa_prepper BORD00XXXX gene_name`
