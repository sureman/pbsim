# 1. About PBSIM #

PacBio sequencers produce two types of characteristic reads as below.

  * Continuous Long Read (CLR)    : long and high error rate.
  * Circular consensus Read (CCS) : short and low error rate.

We have developed a PacBio reads simulater (called PBSIM) in which sampling-based and model-based simulations are implemented.
<br><br>

<h1>2. Run PBSIM with sample data</h1>

To run model-based simulation:<br>
<br>
<blockquote><i>pbsim  --data-type CLR  --depth 20  --model_qc  data/model_qc_clr   sample/sample.fasta</i> <br></blockquote>

In the example above, simulated read sequences are randomly sampled from<br>
a reference sequence ("sample/sample.fasta") and differences (errors) of<br>
the sampled reads are introduced.Data type is CLR, and coverage depth is 20.If the reference sequence is multi-FASTA file, the simulated data is created for each FASTA. Three output files are created for each FASTA.<br>
"sd_0001.ref" is a single-FASTA file which is copied from the reference<br>
sequence.<br>
"sd_0001.fastq" is a simulated read dataset in the FASTQ format.<br>
"sd_0001.maf" is a list of alignments between reference sequence and<br>
simulated reads in the MAF format.<br>
The length and accuracy of reads are simulated based on our model of PacBio read.<br>
<br>
To run sampling-based simulation:<br>
<br>
<blockquote><i>pbsim --data-type CLR  --depth 20 --sample-fastq sample/sample.fastq<br>
sample/sample.fasta</i><br></blockquote>

In the sampling-based simulation, read length and quality score are<br>
the same as those of a read taken randomly in the sample PacBio dataset<br>
("sample/sample.fastq").<br>
<br>
If you want to create several simulated data with different coverage depths using the same PacBio sample, you would be better to use --sample-profile-id option as below. You can save time to parse "sample/sample.fastq".<br>
<br>
<blockquote>(1) storing profile</blockquote>

<blockquote><i>pbsim --data-type CLR --depth 20 --prefix depth20 --sample-fastq sample/sample.fastq --sample-profile-id pf1  sample/sample.fasta</i><br></blockquote>

<blockquote>(2) reusing profile</blockquote>

<blockquote><i>pbsim --data-type CLR  --depth 30  --prefix depth30 --sample-profile-id pf1 sample/sample.fasta</i><br></blockquote>

<blockquote><i>pbsim --data-type CLR  --depth 40 --prefix depth40 --sample-profile-id pf1 sample/sample.fasta</i><br></blockquote>

<br>

<h1>3. Model-based simulation</h1>

For each read, the length is randomly drawn from the log-normal distribution with given mean and standard deviation.<br>
<br>
How to simulate the accuracy of each read is different between CLR and CCS read. For CLR reads, the accuracy is randomly drawn from the normal<br>
distribution with given mean and standard deviation. For CCS reads,<br>
an exponential function which is fit to the the real distribution is<br>
utilized to simulate with fixed mean and standard deviation.<br>
<br>
Errors from single molecule sequencing which generates PacBio reads are<br>
considered to be stochastical, therefore quality scores are randomly chosen from a frequency table of quality scores (named "quality profile") for each accuracy of a read. For accuracies of 0-59% and 86-100% of CLR readsi and 0-84% of CCS reads, uniform distributions are used because real PacBio datasets are not sufficiently large.<br>
"data/model_qc_clr" is quality profile for CLR and "data/model_qc_ccs" is for CCS.<br>
<br>
Simulated read sequences are randomly sampled from a reference sequence.<br>
The percentage of both directions of reads is same. Differences (errors)<br>
of the sampled reads are introduced as follows. The substitutions and insertions are introduced according to the quality scores. Their probabilities are computed for each positions of a simulated read from the error probability of the position (computed from the quality score of the position) and the ratios of differences given by the user. Patterns of substitutions are randomly sampled. We observed that inserted nucleotides are often the same as their following nucleotides. According to the observed bias, half of inserted nucleotides are chosen to be the same as their following nucleotides, and the other half are randomly chosen. The deletion probability is uniform for all positions of all simulated reads, which is computed from the mean error probability of the read set and the ratios of differeces.<br>
<br>
By setting minimum and maximum of the length, the range of length chosen<br>
from the distribution model can be restricted. Note that mean and standard deviation of the chosen length are influenced by this restriction. The accuracy can be restricted in the same way, however unlike the length, the restriction of accuracy is not strict, and can be used in only case of CLR reads.<br>
<br><br>

<h1>4. Sampling-based simulation</h1>

The lengths and quality scores of reads  are simulated by randomly sampling them in a real library of PacBio reads provided by the user.<br>
randomly in a real PacBio dataset given by user. Subsequently, their<br>
nucleotide sequences are simulated by the same method with the model-<br>
based simulation. The restriction of length and accuracy are also<br>
the same as model-based simulation.<br>
<br><br>

<h1>5. Input files</h1>

PBSIM requires reference sequences in the single- or multi-FASTA Format.<br>
<br>
A real PacBio read data is required for sampling-based simulation,<br>
specified with the --sample-fastq option.<br>
FASTQ format must be Sanger standard (fastq-sanger).<br>
<br><br>

<h1>6. Output files</h1>

If a reference sequence file is multi-FASTA format, simulated datasets<br>
are generated for each reference sequence numbered sequentially. Three output files are created for each reference sequence. "sd_NUM.ref" is a single-FASTA file which is copied from the reference sequence. "sd_NUM.fastq" is a simulated read dataset in the FASTQ format. "sd_NUM.maf" is a list of alignments between reference sequence and simulated reads in the MAF format.<br>
<br>
"sd" is prefix which can be specified with the --prefix option.<br>
<br><br>

<h1>7. Quality profile</h1>

Quality profiles are derived from frequencies of real quality scores<br>
for each accuracy of a read.<br>
"data/model_qc_clr" is quality profile for CLR, "data/model_qc_ccs" is for CCS.<br>
In "data/model_qc_clr", 1st column is accuracies of a read, and 2nd-23th<br>
columns are proportions of phred quality scores (0-21).<br>
In "data/model_qc_ccs", 1st column is accuracies of a read, and 2nd-95th<br>
columns are proportions of phred quality scores (0-93).<br>
<br><br>

<h1>8. Runtime and memory</h1>

When coverage depth is 100X and reference length is about 10M, PBSIM generates simulated data in several minutes. The runtime is roughly proportional to coverage depth and reference length.<br>
<br>
PBSIM requires memory of reference length plus several mega bytes.<br>
<br><br>