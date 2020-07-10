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

Usage: $ biom_matrix -d kraken_standart_report -n bts_standart_db_ -o output_kraken_standart_db -r P,C,O,F,G

Options:
- *-s* Path to the  sample to be analyzed (when analysing only one sample, not be used with -d)
- *-d* Path to the directory containing all samples to be collapsed into a single matrix. (not to be used with -s)
- *-n* Expression used to name the analysis of a specific subset (when using the option -d). This will be the name of the final output abundance matrix.
- *-o* Path to the directory where to place final output files. Optional (default is current directory)
- *-r* Taxonomic rank (between Phyla and Species) in which output the final abundance matrix. A vector with one or more of the following: P (for Phyla), C (for Class), O (for Order), F (for Family),	G (for Genus) or S (for Species). When using more than one option, they MUST be separated by a comma. Mandatory.
- *--help* Shows this help information.

After this pipeline, we have obtained “.matrix” files that will be used in a R script to give our matrix the format required to other analysis.
