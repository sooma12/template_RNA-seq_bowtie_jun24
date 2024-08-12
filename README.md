# template_RNA-seq_bowtie_jun24
Template repo for RNA-seq analysis with Bowtie2 using Northeastern University's Discovery cluster

Written by Mark Soo

## Copying this template repository to Discovery



## Setting up config.cfg

Open config.cfg in a text editor.

Under `# Main directory`:

Set BASE_DIR to the main project directory (replace <>).  In general, this should be the template directory that you copied and renamed above.

Under `# Location of untrimmed fastq files`:

Set FASTQDIR_UNTRIMMED to the location of the raw, untrimmed fastq files received from the sequencing company.

Other settings don't need to be changed if analyzing using the 17978-mff annotations.  If you're using another strain, do the following:

1. Change REF_CHR_FA and subsequent files to the fasta files for the organism's genome.  If using more or fewer plasmids/reference sequences, you'll need to change bowtie2_build script as well. 
2. Set BT2_OUT_BASE to a shorthand for the species you're using (this is just to name the bowtie2 index files, not super important)
3. Under `# featureCounts`, change GENOME_GTF to a gtf file containing annotations for the target organism

## Setting up script files

The sbatch scripts (0, 1, 3, 4, and 5) have headers starting with #SBATCH that provides instructions to the slurm management software that runs on Discovery.
The #SBATCH headers will need to be adjusted for your project.

For ALL sbatch scripts:

1. Set --output to a file path for saving the log file.  Example: /work/geisingerlab/Mark/rnaSeq/2024-06-12_sRNAs_eg-palethorpe/logs/%x-%j.log
2. Set --error to a file path to save the standard error file (this just refers to a place where some messages are sent; not always errors).  Example: --error=/work/geisingerlab/Mark/rnaSeq/2024-06-12_sRNAs_eg-palethorpe/logs/%x-%j.err
3. Set --mail-user to your email address

The array scripts (scripts 3 bowtie2 align and 4 samtools) run multiple processes simultaneously so that we can process multiple files at the same time.  These require some additional settings tweaks.

Make a note of the number of samples you have (i.e. 12 if you sent 4 strains in triplicate for RNA-seq).  Let's call that N.

1. Set --array to 1-N%(N+1).  For example, `#SBATCH --array=1-12%13`.
This tells the program to create separate subprocesses numbered from 1 to the total number of samples, and allows it to run N+1 processes at the same time (i.e. all of them).
NOTE: If you have >20 samples, instead of N+1, use 20.
2. Set --ntasks to the number of samples, N.  Example: `#SBATCH --ntasks=12`
3. For both --output and --error, I like to change the file names to `%x-%A-%a` instead of `%x-%j`. `%x-%A-%a` uses the array number.
