#!/usr/bin/perl

use warnings;
use strict;
use Data::Dumper;
use Getopt::Long qw(GetOptions);
use File::Temp qw/ tempfile tempdir /;

my $input_file;
my $file_with_groups;
my $help;

GetOptions(
	'input=s' 		=> \$input_file,
	'file_groups=s'		=> \$file_with_groups,
	'help' 			=> \$help,
) or die "Help: $0 --help\n";

if ($help){
	print "\nAuthor: Maria Beatriz Walter Costa (bia.walter\@gmail.com)\n";
	print "\nThis script filters files that were produced by Kraken2 (with the --report argument). It will print out all lines that match to the groups specified in FILE_WITH_GROUPS_TO_FILTER and all their lower ranks. E. g. if you specify ``Bacteria'', this Domain and all its lower ranks will be kept.\n";
	print "FILE_WITH_GROUPS_TO_FILTER must have a specific format: one group per line, with two columns per line, with the first being the group and the second the rank in one letter format (e. g. file groups.txt of the GITHub Repository).\n";
	print "E. g. if \$GROUP_TO_FILTER=Bacteria, all lines that match this pattern will be printed out, along with all subsequent lines, until another RANK=D is reached (rank would be equal to the one determined by the pattern, in the case of Bacteria, RANK=D)\n\n";
	print "Mandatory arguments:\n\n";
	print "--input KRAKEN_INPUT\n";
	print "--file_groups FILE_WITH_GROUPS_TO_FILTER\n\n";
	print "\nUsage: $0 --input KRAKEN_INPUT --file_groups FILE_WITH_GROUPS_TO_FILTER\n";
	print "Example: $0 --input kraken2_report_examples/mgm4529964_kraken.report --file_groups groups.txt\n\n";	
	print "ATTENTION: PERCENTAGES ARE MAINTAINED EXACTLY AS THEY HAVEN BEEN REPORTED BY KRAKEN2\n";
	die "\n";
}

#################### Main Program ####################### 

#Open file FILE_WITH_GROUPS_TO_FILTER with the taxonomic groups that shall be filtered
open ("input_groups", $file_with_groups) || die "It was not possible to open file $file_with_groups\n";

#This hash will contain the groups
my %groups_to_filter_in;

#Store all groups into a hash structure
while (<input_groups>) {
	chomp;

	#Split line and get the first and second fields
	my @rows = split (/\t/);
	my $group = $rows[0];
	my $rank = $rows[1];

	#Store all groups into a hash structure
	$groups_to_filter_in{$group} = $rank;
}

close input_groups;

#Open file of Kraken to process it
open ("input_file", $input_file) || die "It was not possible to open file $input_file\n";

#$rank variable will be defined, when the pattern is found in the input file
my $rank;

#Marker will be zero if the line is to be printed, one otherwise
my $marker_of_filter = 0;
my $line;
my @rows;

#According to the manual of the Kraken2: http://ccb.jhu.edu/software/kraken/MANUAL.html#sample-report-output-format The row 1 of the --report output is: 1. Percentage of fragments covered by the clade rooted at this taxon

#ATTENTION: The percentages will be mantained in the output of this perl script exactly as they have been reported by Kraken2

#Go through all lines of the Kraken2 file
while(<input_file>) {

	chomp;

	#Set current line to variable	
	$line = $_;	

	#Separate row fields
	@rows = split(/\t/, $line);
	#print Dumper @rows;

	#Keeping elements of the current line's into two strings
	my $current_line_rank = $rows[3];
	my $current_line_group = $rows[5];

	#Check for the pattern designed by the user (e. g. Bacteria)
	#If there is a match, print all subsequent lower ranks until another $rank is found in subsequent lines
	#The following foreach loop will work through all the groups that the user wishes to keep in the output 
	foreach my $group_to_filter (keys %groups_to_filter_in) {

		#Get the rank of the group
		my $rank_to_filter = $groups_to_filter_in{$group_to_filter};

		#If both patterns of the input file match the correct rows of rank and group, then mark the line as desired
		if ($current_line_group =~ /^\s*$group_to_filter$/ && $current_line_rank =~ /^$rank_to_filter$/){

			#Define the rank variable with the fourth column of the line in which the pattern was found
			$rank = $rows[3];

			#Set marker to one, indicating all further lines are to be filtered in (printed), if they are lower ranks to this one
			$marker_of_filter = 1;

			#Print desired line
			print "$line\n";

			#Go to nextline, to avoid printing the current line twice
        	        $line = <input_file>;
                	chomp ($line);
                	@rows = split(/\t/, $line);
		}
	}

	#Check if the marker is set to 1, if so, print the line
	if ($marker_of_filter == 1) {

		#Check if the current rank is equal to the rank of the group (if so, you have e. g. another Domain, therefore, don't print it)
		my $rank_of_comparison = $rows[3];

		#Check if the rank of the current line is equal to the rank we were printing the lines		
		if ($rank_of_comparison eq $rank) {

			#Set marker to zero 
			$marker_of_filter = 0;

			#Check if the current line has a group that the user wants to print
			#Check for the pattern designed by the user (e. g. Archaea), if there is a match, print all subsequent lower ranks until another $rank is found in subsequent lines
			foreach my $group_to_filter (keys %groups_to_filter_in) {

				my $rank_to_filter = $groups_to_filter_in{$group_to_filter};

				#If both patterns of the input file match the correct rows of rank and group, then mark the line as desired
				if ($current_line_group =~ /^$group_to_filter$/ && $current_line_rank =~ /^$rank_to_filter$/){

					#Define the rank variable with the fourth column of the line in which the pattern was found
					$rank = $rows[3];

					#Set marker to one, indicating all further lines are to be filtered
					$marker_of_filter = 1;

					#Print the current line
					print "$line\n";
				}
			}
		} else {
			
			#print "$rank_of_comparison was reached\t$line\n";
			#Print desired line
			print "$line\n";

		}
	}

}

close input_file;	

