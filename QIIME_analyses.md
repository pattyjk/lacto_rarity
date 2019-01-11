## QIIME analyses
```
QIIME v. 1.9.1

#filter out all non-lactobacillales taxa
filter_taxa_from_otu_table.py -i Desktop/emp_cr_silva_16S_123.qc_filtered.rare_10000.biom -o Desktop/emp_lacto.biom -p D_3__Lactobacillales

#summarize taxonomic composition as absolute abundance
summarize_taxa.py -i emp_lacto.biom -o lacto_sum -a --suppress_biom_table_output
```
