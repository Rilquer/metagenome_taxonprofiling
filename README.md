# Scripts to perform taxonomic cleaning and profiling of metagenomic samples

**Requirements:**
- All the scripts in this repository must be in /bin directory, with chmod 755 permissions
- BIOM software: https://biom-format.org/#installing-the-biom-format-python-package
- kraken-biom software: https://github.com/smdabdoub/kraken-biom


Theses steps cames after other pipeline (soon it will be here in github) to download, apply quality filter and performs taxonomic annotation in metagenomical samples.
We use *kraken_report* to generate abundance matrix from Bacteria and Archaea groups.


*biom_matrix*, is a short script made by Rilquer Mascarenhas, who was designed to process report files outputted by kraken into abundance matrix for a specific metagenomic sample or a set of metagenomic samples.
This script performs:
1. Apply a taxonomic filter using the script *selectGroups*, based on the groups files, to filter out any
annotation not referred to Bacteria or Archaea.
2. For each taxonomic rank defined by the user, uses the selgroups_out file (output from the previous script) into the program kraken-biom, which converts the file to a table in the biom format. Then, uses the biom command from the same program to convert the biom format into a readable tsv format table.
3. The tsv format is read into the R script *modify_tsv*, to filter out the results for the specified rank. This script retrieves all reads annotated to a specific taxonomic rank, as well as all reads that were annotated to a higher rank but could not be annotated for the desire rank (e.g., when the desired rank is genus, a number of reads can be annotated only until the family level, and those will be outputted as Unclassified genus in family XXX).

After this pipeline, we have obtained “.matrix” files that will be used in a R script to give our matrix the format required to other analysis.
