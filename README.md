## RVDB - A Reference Viral Database (RVDB)

The RVDB (Reference Virus DataBase) is a collection of all known viral, viral-related, and viral-like sequences, except bacterial viruses. The RVDB is updated directly from the NCBI RefSeq, NCBI GenBank and NCBI Third-Party Annotation (TPA) sequences collections. The files contained in this directory contain instructions for performing an update of the RVDB, as well as characterizing the resulting sequences by the following categories: exogenous viral, endogenous nonretroviral, endogenous retroviral, LTR-retrotransposon, and unassigned viral genes / fragments. Further instructions for performing the RVDB update are provided in the "Instructions for Updating RVDB.docx" document, while instructions for characterization are provided in the "Instructions for Characterizing RVDB Sequences.docx". The “RVDB_characterization.py” python script is used for the characterization; all other python scripts are used for updating RVDB. The paper describing RVDB is published (Goodacre N, Aljanahi A, Nandakumar S, Mikailov M, Khan AS. 2018. A reference viral database (RVDB) to enhance bioinformatics analysis of high-throughput sequencing for novel virus detection. mSphere 3:e00069-18. https://doi.org/10.1128/mSphereDirect.00069-18).


## Instructions for Updating RVDB.docx

This contains instructions for running the semi-automated RVDB update pipeline. This pipeline consists of 3 main parts: 1) downloading raw entries from GenBank, RefSeq, and TPA ftp sites, 2) parsing the files, assimilating meta-data, and running filters and semantic screens to select viral, viral-related, and viral-like sequences and filter out host cellular and phage sequences, 3) manual review, clustering, and database (i.e. .fasta) file generation. 


## Instructions for Characterizing RVDB Sequences.docx

This contains instructions for characterizing sequences in an RVDB .fasta file, using the RVDB_characterization.py script. 


## parse_raw_refseq_PIPE.py 

Takes the two RefSeq viral files and outputs a eukaryotic viral fasta file formatted with two lines per entry (header and sequences), as well as a phage file (same format. Only the eukaryotic viral file will be incorporated into the RVDB update.

## multiple_gzunzip_PIPE.py

Unzips the two GenBank-format flat files (.gbff) for refseq viral and combines them into a single file. 

## fileops_PIPE.py 

Splits the combined GenBank-format flat file into multiple files, so that each can be read into Python efficiently. 

## rs_acc_mapping_PIPE.py 

Using the GenBank-format flat file metadata for RefSeq viral, finds the duplicate entries’ accessions (original entries, upon which RefSeq viral entries were based. During the unzipping and reformatting of GenBank division files, these duplicates are not included. Also identifies which sequences are RefSeq viral neighbors, so that this can be incorporated into the headers of sequences from GenBank division files as they are unzipped. 

## VDBunzip_reformat_gb_to_fasta_PIPE.py 

Unzips the GenBank division files, labels sequences that are RefSeq viral neighbors during the unzipping. Please note that a modified form of the GenBank Scanner.py script (found in Biopython, typically in Python sub-folder: Lib/site-packages/Bio/GenBank) should be used, in place of the original. This is described below (last .py file). 

## VDBunzip_tpa_PIPE.py 

Unzips the TPA files. TPA files are simply zipped .fasta files. TPA files do not have any associated metadata, so the Scanner.py script is not needed to reformat them. 

## VDBupdate_checkpoint2_PIPE.py 

Runs checkpoint2, which verifies that all GenBank division files were unzipped and reformatted as .fasta files successfully, using the most recent official GenBank release notes as a reference (see "Instructions for Updating RVDB.docx" for information on where to access these release notes.

## SEM-R_PIPE.py 

Semantic screening for the RVDB. Contains three parts: a positive semantic screen to pull in potentially-viral sequences, a size/mirna screen to remove mirnas and sequences <50 b.p. in length, and a negative semantic screen to remove all false-positive sequences pulled in by the positive screen. 

## SEM-R_june62018_PIPE.py

The revised version of SEM-R_PIPE.py with updated keyword list to improve the accuracy of semantic pipeline. 


## Scanner.py

This is a modified version of the Scanner.py script that can simply be copied and pasted into the same directory as the original Scanner.py script (overwrite previous). To avoid having to change additional lines in calling scripts to accommodate a different name for Scanner.py, the name of Scanner.py was not changed. The modifications should not hinder any existing functionality of the script, so it can safely be used in place of the original. The modified Scanner.py contains a try/except block in two places, to correct for an error that occurred in a small number of entries, related to the ‘Structured Comments’ metadata. In a very small number of cases, it was noted that the standard Scanner.py script tries to extract the ‘Structured Comments’ metadata, when this metadata is in fact not present. 

## create_U-RVDB.py

Takes all .fasta files containing sequences that are either RefSeq eukaryotic viral or GenBank/TPA that have passed all three parts of the SEM-R screen, and combines them into one file - the unclustered RVDB, named U-RVDBv$version.fasta. 

## prep_manual_review.py

Collects all sequences that have passed all three parts of the SEM-R_PIPE.py screen and takes accessions of these “passing” sequences and compares them to accessions from the previous version of the U-RVDB, and finally outputs two sheets: one with headers of “missing” sequences in the update, or those that were present in the previous version but not the update, and one with headers of “new” sequences in the update, or those that are present in the update but were not present in the previous version.

## edit_rawcdhit_output.py

Re-organizes the clustered (.clstr) output file from CD-HIT-EST to serve as the input of create_C-RVDB.py

## create_C-RVDB.py

Takes the cluster repressentatives from the CD-HIT-EST clustering output (.clstr file) and uses them as a filter to extract a subset of the unclustered RVDB, resulting in the clustered RVDB, named C-RVDBv$version.fasta. 

## make_alter_build_sqlite3db_v2.py
Takes C-RVDBv$version.fasta file and converts to Structure Query Language (SQL) format for the convenience of SQL database construction. 

## create_relnotes.py

Takes the C-RVDBv$version.fasta or U-RVDBv$version.fasta file to generate summary statistics as well as update information in a simple but standardized format. These statistics include total counts of sequences in each category (RefSeq Viral, RefSeq Viral neighbor, GenBank division, or TPA) as well as date (month and year) information for the download of RefSeq and GenBank flat files.

## RVDB_characterization.py

Divides an input RVDB .fasta file's sequences into 5 groups: exogenous viral, endogenous nonretroviral, endogenous retroviral, LTR-retrotransposon, and viral gene/fragment. 



## Questions/Comments

If you have any questions or comments regarding RVDB nucleotidic databases, please contact Arifa Khan (Arifa.Khan@fda.hhs.gov)
