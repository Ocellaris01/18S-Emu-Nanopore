# 18S-Emu-Nanopore

Emu (Curry et al., 2022) is a relative abundance estimator for 16S genomic sequences, it is optimized for error-prone full-length reads but can also be utilized for short-read data. While the pipeline's creators synthesized several pre-built datasets none can be used for eukaryotic 18S rRNA sequences. Following the Emu pipeline, a custom database was crafted for downstream analysis of protists from marine seawater samples.

## EMU Database Creation
Requires:
sequences.fastq -> Nucleotide sequences
seq2tax.tsv -> Headerless two column tab-separated values, where each row contains (1) sequence header in database.fasta and (2) corresponding tax id
taxonomy_list.tsv -> A .tsv file containing complete taxonomic lineages. The first column MUST be the taxonomy ids.

Taxonomic IDs, sequences, and assention numbers were downloaded from these files available from SILVA v138.2:
Seq2Tax & Taxonomy List
https://www.arb-silva.de/fileadmin/silva_databases/current/Exports/taxonomy/taxmap_slv_ssu_ref_138.2.txt.gz

Sequences
https://www.arb-silva.de/fileadmin/silva_databases/current/Exports/SILVA_138.2_SSURef_tax_silva.fasta.gz

#### Dataset Formatting
For the Emu pipeline's custom database creation three files are required:
##### Sequences
This code will of any extra characters, isolating sequence and accession number for each taxa.
```
head -n100 SILVA_138.2_SSURef_tax_silva.fasta | sed -E 's/^>[^[:space:]]+[[:space:]]+/>/'
```
Results in a .fasta file with:
">accession#
Sequence..."

##### Seq2Tax
Remove all columns except for the accession number and taxa ID, keeping them in the same order.
```
cut -f1,6 taxmap_slv_ssu_ref_138.2.txt > seq2tax.tsv
```

##### Taxonomy List
Remove all columns except for the taxa ID and path (i.e. lineage), in order to reverse the column order preform the manipulation below:
```
cut -f4 taxmap_slv_ssu_ref_138.2.txt > col2.txt
cut -f6 taxmap_slv_ssu_ref_138.2.txt > col1.txt
paste col1.txt col2.txt > taxonomy_list.tscv
```

### Database Creation
Run the Emu command to build the custom database (ensure you're working in the py37 environment)
```
emu build-database EukDB --sequences sequences.fasta --seq2tax seq2tax.txt --taxonomy-list taxonomy_list.tsv
```
Will generate EukDB directory, the path of which will be used for Emu abundance analysis following the typical protocol.


## Dealing with Duplicates
```
uniq FBD00841_pass_unclassified_3c2e19c5_42bde7a6_120.fastq_rel-abundance.tsv |  awk '{ sum += $2; } END {print sum; }'

```
