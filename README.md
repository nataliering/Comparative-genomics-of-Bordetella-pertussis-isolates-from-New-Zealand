# Comparative-genomics-of-Bordetella-pertussis-isolates-from-New-Zealand
Code and commands used in our manuscript, "Comparative genomcis of Bordetella pertussis isolates from New Zealand, a country with an uncommonly high incidence of whooping cough"

## Abstract
Whooping cough, the respiratory disease caused by *Bordetella pertussis*, has seen a wide-spread resurgence over the last several decades. Previously, we developed a pipeline to assemble the repetitive *B. pertussis* genome into closed sequences using hybrid nanopore and Illumina sequencing. Here, this sequencing pipeline was used to conduct a more high-throughput, longitudinal screen of 66 strains isolated between 1982 and 2018 in New Zealand. New Zealand suffers from a higher incidence of whooping cough than almost any other vaccine-using country, usually at least twice as many cases per 100,000 people than the USA and UK and often even higher, despite similar rates of vaccine uptake. We believe these strains represent the first New Zealand *B. pertussis* isolates ever sequenced. The analyses here show that, on the whole, genomic trends in New Zealand *B. pertussis* isolates, such as changing allelic profile in vaccine-related genes and increasing pertactin deficiency, have paralleled those seen elsewhere in the world. At the same time, phylogenetic comparisons of the New Zealand isolates with global isolates suggest that a number of strains are circulating in New Zealand which are closely related to each other, and which cluster separately from other global strains. Whilst in apparent continuous circulation, these strains appear to be more highly represented during outbreak periods. The results of this study add to a growing body of knowledge regarding recent changes to the *B. pertussis* genome, and are the first genetic investigation into New Zealand’s high incidence of whooping cough.


## Commands for tools mentioned in manuscript
Each of the tools we used can be further optimised; we tended to use the default settings in most cases, often exactly as recommended in the tool's README.
### Preparing Illumina reads and assembling Illumina-only references
**[fasterq-dump (download of reads from SRA)](https://github.com/ncbi/sra-tools)**  
`fasterq-dump ACCESSION_NUMBER --split-files --gzip`

**[Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)**  
`trimmomatic PE input_1.fastq input_2.fastq output_1_PE.fastq output_1_SE.fastq output_2_PE.fastq output_2_SE.fastq HEADCROP:10 SLIDINGWINDOW:4:25 MINLEN:100`

**[ABySS](https://github.com/bcgsc/abyss)**  
`abyss-pe name=output_name k=63 in='input_1_PE.fastq input_2_PE.fastq' t=8` 

### Preparing Nanopore reads
**[Deepbinner](https://github.com/rrwick/Deepbinner)**  
`deepbinner realtime --in_dir fast5_dir --out_dir demultiplexed_fast5s --rapid`

**[Guppy](https://community.nanoporetech.com/protocols/Guppy-protocol/v/gpb_2003_v1_revs_14dec2018/linux-guppy)** (Nanopore community account needed)  
`guppy_basecaller --input_path directory_of_fast5_files --save_path directory_for_output --config dna_r9.4.1_450bps_fast.cfg --barcode_kits SQK_RBK004 --num_callers 8`

**[Porechop](https://github.com/rrwick/Porechop)**  
`porechop -i input_directory -b output-directory --threads 8`

**[Canu correct](https://github.com/marbl/canu)**  
`canu -correct -p output_prefix -d output_directory genomeSize=4.1m -nanopore-raw input_reads.fastq`

### Short-read-centric hybrid assembly
**[Unicycler (hybrid mode)](https://github.com/rrwick/Unicycler)**  
`unicycler unicycler -1 output_1_pe.fastq -2 output_2_pe.fastq -l corrected_reads.fasta -o output_directory -t 8`

### Long-read-centric hybrid assembly
**[Flye](https://github.com/fenderglass/Flye)**  
`flye --nano-corr corrected_reads.fasta --genome-size 4.1m --out-dir output_directory --threads 8` 

### Polishing (long and short reads)
**[Racon](https://github.com/isovic/racon)**  
We used Minimap2 to map the Nanopore reads to each long-read-centric draft, then used the alignment file to run Racon. This was repeated 4 times before Medaka polishing. [racon_runner](https://github.com/nataliering/Resolving-the-complex-Bordetella-pertussis-genome-using-barcoded-nanopore-sequencing/blob/master/racon_runner) automatically carries out the required steps.

**[Medaka](https://github.com/nanoporetech/medaka)**  
We included Medaka in our long-read-centric pipeline. 
`medaka_consensus -i basecalled_reads.fastq -d unpolished_genome.fasta -o output_directory -t 8 -m r941_min_fast_g303`

**[Pilon](https://github.com/broadinstitute/pilon)**  
We used bwa mem to produce map the Illumina reads to each draft, processed the output alignment using samtools, then used the processed alignment file to run Pilon. [pilon_runner](https://github.com/nataliering/Resolving-the-complex-Bordetella-pertussis-genome-using-barcoded-nanopore-sequencing/blob/master/pilon_runner) automatically carries out the required steps.

### Assessing assembly completeness
**[BUSCO](https://busco.ezlab.org/)**  
`BUSCO.py -i assembly.fasta -o output_name -l bacteria_odb10 -m genome`

### Comparing genomic arrangement
**[progressiveMauve](http://darlinglab.org/mauve/)**  
`progressiveMauve --output=output_name.mauve /path/to/genome/directory/*.fasta`

### Assigning allele type for ACV genes (using custom-made bpertussis ACV genes mlst scheme)
**[tfa_prepper](https://github.com/nataliering/Comparative-genomics-of-Bordetella-pertussis-isolates-from-New-Zealand/blob/master/tfa_prepper)**
`tfa_prepper BORD00XXXX gene_name`

**[mlst](https://github.com/tseemann/mlst)**  
`mlst --legacy --csv --scheme bpertussis input_genomes.fasta > mlst_results.csv `

The ready-made custom scheme can also be downloaded from **[Figshare](https://doi.org/10.6084/m9.figshare.12628223)**.

### Draft annotation
**[Prokka](https://github.com/tseemann/prokka)**  
`prokka --prefix prefix --addgenes --genus Bordetella --species pertussis --kingdom Bacteria --usegenus --proteins reference_proteins.faa --evalue 1e-10 --rfam --cpus 8  assembly.fasta`


                                 

