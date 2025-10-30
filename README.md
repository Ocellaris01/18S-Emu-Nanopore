# 18S-Emu-Nanopore
Taxonomic assignment and abundance counts using the Emu pipeline and SILVA 18S SSU rRNA database

#### EMU database creation
first step SILVA 138.2 db was created using .....

```
 head -n100 SILVA_138.2_SSURef_tax_silva.fasta | sed -E 's/^>[^[:space:]]+[[:space:]]+/>/'
```
