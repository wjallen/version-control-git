# Life Sciences Workflows

### Module Objectives

* Use the Linux shell (Bash) to automate a multi-step workflow
* Use Bash if and for loops for flow control
* Use variables to make your workflow more generic
* Turn your multistep workflow into a single Bash command

### Introduction

Research is all about discovering something new, which usually means the computational side of research also includes something novel: new datasets, new code, new procedure, etc.  It is enough that you frequently cannot perform the work efficiently by just using other people's software and tools, and much of this course gives you a skillset to approach new tasks with confidence.

Once you know how to do something new, though, you typically have to do it a lot of times.  It becomes important to automate your computation so that it is easy for you to reproduce results, do the same computation on control and experimental datasets, etc.  This session will focus on basic skills for encapsulating a workflow in a reusable script.

## Background for our example

### Protein Blast

BLASTP programs search protein databases using a protein query. In our case, it will utilize a peptide database generated by the Transcript decoder 1.0 and generates a set of search matches that provide a view onto the genes represented in the transcriptome of our query. (Basic documentation: http://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=ProgSelectionGuide)

This example use case is adapted from a Cyverse tutorial written by Uwe Hilgert: [https://pods.iplantcollaborative.org/wiki/pages/viewpage.action?pageId=12224155](https://pods.iplantcollaborative.org/wiki/pages/viewpage.action?pageId=12224155)

### Test Data

This tutorial uses Illumina sequencing data from a study in Belgica antarctica (Teets et al., 2012*) that are stored in the Data Store in the iPlant Discovery Environment. They were retrieved from the NCBI Sequence Read Archive (SRA) at http://www.ncbi.nlm.nih.gov/sra/?term=Belgica%20antarctica. To reduce processing times, the tutorial sample data set was reduced from the original number of 45,065 contigs to 200 contigs. (Conducting a BLASTP search using the original data set and searching RefSeq Protein would take approximately 9 days... and about six weeks using BLASTX.) A FASTQC conducted on these sequences indicated prior trimming and the data were not subjected to additional trimming but used as is.

The sample BLAST search database consists of NCBI's RefSeq protein, excluding human and mouse sequences. The data were downloaded in July 2013 (so it is a little outdated, but perfectly suitable for our example exercises). RefSeq Protein is a comprehensive, integrated, non-redundant, well-annotated set of reference sequences derived from genomic, transcript, and protein data. For further information visit NCBI's RefSeq pages.


## The first run

### Input data

If this were a real research problem, the first thing we should do is organize our input data, run our analysis, and document our results.  Let's go through these motions first.

**Create a directory on your $WORK filesystem where you will store this data and run your analysis.**  

Once you have done that, copy in the query dataset.  For the reference database, it is about 14GB in size, and it is okay for you to leave it in place (and put a note somewhere about where it is).

```
# copy the query
cp /work/01114/jfonner/public/ctls2017/query.pep .

# look at the database
ls /work/01114/jfonner/public/ctls2017/refseq_protein/refseq_protein*
du -sh /work/01114/jfonner/public/ctls2017/refseq_protein/

# copy a script we will use later:
cp /work/01114/jfonner/public/ctls2017/splitfasta.pl .
```

For keeping notes about what you do, it is common to name a file **README**, but feel free to adopt your own convention.  Using .txt as a file extension for your notes will let future you know that your notes file is meant for human reading.

### Protein Blast software

Is the software we need already available on the system?

```
type blastp
```

Hmmm... how can we check if it is available but just not loaded?

**Once you find the package and have it loaded, put a brief note in your README**  Be sure to note the version we will use!

### Running blastp interactively

Next, we would normally try running the software on our dataset of interest.  We normally wouldn't know how long it will take, so lets try starting an interactive session on a compute node and running blastp on a subset of our data.

```
idev -m 180 -r
```

**How do we know what commandline arguments `blastp` needs?**

Take a little time to look at the help for blastp.  There are a lot of options that may be important.  For our test example, we can leave the various thresholds at the default.  We just need to give blastp the query and the database.

For our query, let's try just using the first few sequences of our query.

```
mkdir blastp_test
cp query.pep blastp_test/first10.pep

#now edit down first10.pep to only use the first 10 sequences. There are many ways to do this.

#check that your files is in fact shorter
grep -c "^>" query.pep blastp_test/first10.pep

cd blastp_test
```

Time to try it.  It will take a couple of minutes, so for the sake of time, let's run it in the background and then take a closer look at the command line while we wait:

```
(time blastp -num_threads 1 -query first10.pep -db /work/01114/jfonner/public/ctls2017/refseq_protein/refseq_protein) > test.log 2>&1 &
```

Do you understand everything happening in that command?  While this is running, take a look at [explainshell.com](https://explainshell.com/) and paste in this command.  It won't like the `time` part of this command, but you can just delete that part.

What happens if we don't put parenthesis around the command?

Previous: [Agenda](../../index.md) | Next: [Automating Workflows 1](workflows1_2.md)