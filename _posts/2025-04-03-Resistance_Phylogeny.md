---
layout: post
title: "Activity 3 - Outbreak Chronicles"
categories: misc
---

# Identifying and Tracing Antibiotic Resistance: An Evolutionary Genomics Investigation of Antibiotic Resistance

## Context 

A hospital is experiencing an outbreak of infections caused (most
likely) by some bacterial pathogen. Some patients recover quickly with
standard antibiotics, while others do not respond to treatment, leading
doctors to suspect antibiotic resistance.

![](https://github.com/cb3017/cb3017.github.io/blob/master/figs/activity_01.jpeg?raw=true)

The hospital's microbiology team collects bacterial isolates from
infected patients through clinical samples such as blood, urine, sputum,
and wound swabs. After culturing the bacteria, DNA is extracted and sent
to the vendor. The vendor then sequences the whole-genome (WGS),
typically with Illumina for high-accuracy short reads or Oxford
Nanopore/PacBio for long-read sequencing to detect plasmids and
structural variants. The choice of the sequencing platform varies
depending on various constraints. However, once the sequencing is
complete, the raw sequencing data undergoes quality control and is
shared with the computational biologists (CB) team at the hospital.

The CB team usually performs quality control on their end followed by
constructing a genome assembly, and annotation to identify antibiotic
resistance genes, mutations, and mobile genetic elements. They then
perform comparative genomic analysis for SNP calling, phylogenetics, and
plasmid detection to determine whether resistance is due to genetic
mutations or horizontal gene transfer. This information is crucial for
guiding treatment decisions, infection control, and preventing further
spread of resistant bacteria.

Your task as an intern with the CB team is to analyze the genetic
differences between these bacterial isolates to understand how
resistance emerged and spread. By comparing their whole-genome
sequences, you will:

1.  Identify mutations or resistance genes -- Are there specific genetic
    changes in the resistant strains that are absent in the susceptible
    ones?

2.  Trace the evolutionary relationships -- Did all resistant strains
    originate from a single ancestor, or did resistance evolve multiple
    times independently?

3.  Determine how resistance spreads -- Is it due to genetic mutations
    within the bacteria, or is resistance being transferred between
    bacteria through mobile genetic elements like plasmids?

### Example Paper 

Here is [a research article](https://www.nature.com/articles/s41598-022-11287-5.pdf) that was the inspiration for this
activity.

## Instructions 

### Evolution of Resistance - Introduction 

The importance of antibiotics in treating bacterial infections and the
growing threat of antibiotic resistance. [News article](https://www.theguardian.com/society/2025/mar/10/us-aid-cuts-tuberculosis-tb-untreatable-bug-drug-resistance-stop-tb-partnership-who) and [short videos](https://www.youtube.com/watch?v=G3CqsRzuBaQ)
highlighting the issue. Mechanism of Antibiotic Action: Think about how
[different classes of antibiotics work](https://i0.wp.com/www.compoundchem.com/wp-content/uploads/2024/01/Classes-of-antibotics-2024.png?fit=2646%2C1871&ssl=1) (e.g., targeting cell wall
synthesis, protein synthesis, DNA replication). Mechanisms of
Resistance: Explore [how bacteria develop resistance through various
mechanisms](https://www.mdpi.com/antibiotics/antibiotics-11-01215/article_deploy/html/images/antibiotics-11-01215-g001.png), emphasizing the role of genetic mutations leading to altered
target sites, enzymatic inactivation of antibiotics, and efflux pumps.
Evolutionary Lens on Resistance: [Antibiotic resistance as a prime
example of evolution in action](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41396-020-00832-7/MediaObjects/41396_2020_832_Fig2_HTML.png?as=webp). Apply concepts like natural selection,
mutation, and horizontal gene transfer in the context of bacterial
populations exposed to antibiotics. Genomic Insights into Evolution: How
comparing the genomes of different bacterial strains can reveal the
evolutionary history of resistance, including the origin of resistance
genes and the accumulation of resistance-conferring mutations.

![](https://github.com/cb3017/cb3017.github.io/blob/master/figs/activity_02.jpeg?raw=true)

**Aim:** <ins>You will investigate the evolutionary relationships among
several isolates of a specific bacterial species, some known to be
antibiotic-resistant and others susceptible. You will use genomic data
to trace the genetic changes associated with the development of
resistance.</ins>

### Analysis to identify the mutation and to suggest effective alternative antibiotic 

<!-- 1.  You will receive a set of pathogen genome sequences (or accession
    numbers).

2.  You can use CARD to identify resistance genes.

3.  Depending on the mutations in genes like rpoB (rifampicin resistance
    in TB) or gyrA (fluoroquinolone resistance in *E. coli*), suggest an
    effective alternative from with the list of antibiotics approved in
    India from CDSCO (pull this from the csv file).
 -->

<details>
  <summary><strong>1. You will receive a set of pathogen genome sequences (protein fasta files). SCP Files To/From Cluster</strong></summary>
  <br>
  The fasta files are generated from the raw reads that is processed through a pipeline like the [bacass](https://nf-co.re/bacass/2.4.0/) to get the genome and proteome assembly. Given genome assembly and annotation is out of scope for this course, we have proteome assembly directly from the literature. To securely copy files between your local machine and a DGX cluster, use <code>scp</code>:

  <details>
    <summary><strong>Copy files from local to cluster</strong></summary>
    <br>
    <pre><code>scp /path/to/local/file username@10.59.121.172:/path/to/destination/</code></pre>
    For multiple files or directories:
    <pre><code>scp -r /path/to/local/directory username@10.59.121.172:/path/to/destination/</code></pre>
  </details>

  <details>
    <summary><strong>Copy files from cluster to local</strong></summary>
    <br>
    <pre><code>scp username@10.59.121.172:/path/to/dgx/file /path/to/local/destination/</code></pre>
  </details>

  <details>
    <summary><strong>Example</strong></summary>
    <br>
    If you want to transfer <code>samples.fasta</code> to the cluster:
    <pre><code>scp samples.fasta username@10.59.121.172:/home/user/project/</code></pre>
  </details>
</details>

<details>
  <summary><strong>2. Run RGI in CARD using the FASTA files (6)</strong></summary>
  <br>
  The FASTA files are generated from raw reads processed through a pipeline like 
  <a href="https://nf-co.re/bacass/2.4.0/" target="_blank">Bacass</a> to get genome and proteome assembly.

  RGI (Resistance Gene Identifier) is used to analyze antimicrobial resistance (AMR) genes.

  <br><br>We will use the online version of the RGI tool for simplicity.
</details>

<details>
  <summary><strong>3. Get the Sequence of the Top Hit from CARD</strong></summary>
  <br>
  After running RGI, the results contain gene predictions with scores. Extract the best hit:

  <details>
    <summary><strong>Download the sequence from CARD</strong></summary>
    <br>
    1. Go to <a href="https://card.mcmaster.ca/" target="_blank">CARD website</a>.<br>
    2. Search for the <strong>ARO number</strong> from the top hit.<br>
    3. Download the reference gene sequence in FASTA format (choose protein).
  </details>
</details>

<details>
  <summary><strong>4. Run BLAST to Identify the Target Gene Across All FASTA Files</strong></summary>
  <br>
  Now, compare your FASTA files against the CARD reference sequence.

  <details>
    <summary><strong>Step 1: Install BLAST</strong></summary>
    <br>
    Install BLAST and Biopython if not already installed:
    <pre><code>conda install -c bioconda -c conda-forge blast biopython</code></pre>
  </details>

  <details>
    <summary><strong>Step 2: Identify the Target Gene in All Samples and Reference</strong></summary>
    <br>
    Use the provided script:
    <pre><code>python blast_and_extract.py</code></pre>
    The <code>extracted_hits.faa</code> file contains the target gene across all the samples and the reference.
  </details>
</details>

<details>
  <summary><strong>5. Run Multiple Sequence Alignment (MSA) Using Clustal Omega</strong></summary>
  <br>
  Now, align all sequences in <code>extracted_hits.faa</code> to analyze their similarity.

  <br><br>We will use the online version of Clustal Omega for simplicity.
</details>

<details>
  <summary><strong>6. Visualize the Phylogenetic Tree and Interpret Resistance Origin</strong></summary>
  <br>
  - <strong>Clustered sequences:</strong> Similar resistance genes are evolving together.<br>
  - <strong>Distant sequences:</strong> Resistance may have arisen independently or through a single ancestor.<br>
  - <strong>Reference alignment:</strong> Compare known resistance genes with your unknown sequences.
</details>


<details>
  <summary><strong>Identifying Resistance Genes, Mutations, and Phylogenetic Analysis</strong></summary>
  <br>

  <h3>Identifying Resistance Genes and Analyzing Mutations using CARD</h3>

  <details>
    <summary><strong>BLASTing against CARD Database</strong></summary>
    <br>

    <strong>1. Accessing the CARD Database:</strong><br>
    - Open a web browser and navigate to the official CARD website: <a href="https://card.mcmaster.ca/" target="_blank">CARD</a>.

    <strong>2. Preparing Protein Sequences:</strong><br>
    - Obtain protein sequences of potential genes from bacterial genomes.
    - Save sequences in FASTA format.

    <strong>3. Performing BLAST Search on CARD:</strong><br>
    - Locate the "BLAST" or "Sequence Analysis" tool.
    - Select "Protein BLAST."
    - Paste sequences in the input box.
    - Choose the "CARD Protein Homologs" database.
    - Click "Run BLAST."

    <strong>4. Interpreting CARD BLAST Results:</strong><br>
    - Look at Percent Identity, E-value, ARO term, Resistance Mechanism, and Confidence Score.
  </details>

  <details>
    <summary><strong>Analyzing Mutations with CARD</strong></summary>
    <br>

    <strong>1. Focusing on Identified Resistance Genes:</strong><br>
    - Select genes found in resistant and susceptible strains.

    <strong>2. Obtaining Protein Sequences for Comparison:</strong><br>
    - Retrieve sequences from all strains.

    <strong>3. Using CARD for Mutation Information:</strong><br>
    - Search for genes in CARD.
    - Look for known mutations.

    <strong>4. Comparing Sequences and Identifying Mutations:</strong><br>
    - Use multiple sequence alignment tools.

    <strong>5. Correlating Mutations with CARD Data:</strong><br>
    - Compare with CARD mutation data.
  </details>

  <details>
    <summary><strong>Phylogenetic Analysis and Evolutionary Inference</strong></summary>
    <br>

    <strong>1. Selecting a Suitable Gene for Phylogeny:</strong><br>
    - Use housekeeping genes (*recA, rpoB, gyrB, 16S rRNA*) or resistance genes.

    <strong>2. Multiple Sequence Alignment:</strong><br>
    - Use Clustal Omega to align sequences.

    <strong>3. Phylogenetic Tree Construction:</strong><br>
    - Use MEGA or Phylogeny.fr.
    - Choose between Distance-based and Character-based methods.

    <strong>4. Interpreting the Phylogenetic Tree Using CARD Data:</strong><br>
    - Map resistance genes on the tree.
    - Identify evolutionary patterns of resistance spread.
  </details>
</details>

<details>
  <summary><strong>Using TB-Profiler for Drug Resistance Prediction</strong></summary>
  <br>

  <h3>Introduction to TB-Profiler</h3>
  TB-Profiler predicts drug resistance profiles of <i>Mycobacterium tuberculosis</i> (MTB) from sequencing data.

  <details>
    <summary><strong>Accessing TB-Profiler Online Tool</strong></summary>
    <br>
    - Available at <a href="https://tbdr.lshtm.ac.uk/" target="_blank">TB-Profiler</a>.
  </details>

  <details>
    <summary><strong>Uploading Your FASTQ File</strong></summary>
    <br>
    - Upload sequencing data to TB-Profiler.
    - Paired-end reads require two FASTQ files.
  </details>

  <details>
    <summary><strong>Understanding the Resistance Associated Mutations</strong></summary>
    <br>
    <strong>1. Gene Name:</strong> Lists resistance genes like <code>rpoB</code>, <code>katG</code>, and <code>gyrA</code>.<br>
    <strong>2. Mutation:</strong> Describes nucleotide changes (e.g., <code>S531L</code> in <code>rpoB</code>).<br>
    <strong>3. Drug Association:</strong> Lists drugs affected.<br>
    <strong>4. Confidence Score:</strong> Indicates reliability.<br>
    <strong>5. Resistance Prediction:</strong> Summarizes resistance profile.
  </details>

  <details>
    <summary><strong>Mutation Visualization</strong></summary>
    <br>
    <img src="https://github.com/cb3017/cb3017.github.io/blob/master/figs/rpoB_mutation.png?raw=true" alt="Mutation Visualization">
  </details>

  <details>
    <summary><strong>Important Considerations</strong></summary>
    <br>
    - Ensure good data quality for accuracy.<br>
    - Database updates impact results.<br>
    - Further analysis may be needed for novel mutations.
  </details>
</details>


<!-- <details>
<summary>Tools and Deets</summary>

### Identifying Resistance Genes and Analyzing Mutations using CARD 

### BLASTing against CARD Database 

1.  **Accessing the CARD Database:**

    -   Open a web browser and navigate to the official CARD website:
        <https://card.mcmaster.ca/>.

2.  **Preparing Protein Sequences:**

    -   You will need the protein sequences of potential genes from the
        bacterial genomes you are investigating. These can be obtained
        from the GenBank annotations of the genomes you downloaded in
        Phase 2.

    -   Save the protein sequences in FASTA format. Each sequence should
        start with a "\>" followed by a unique identifier and
        description, and then the amino acid sequence on subsequent
        lines.

3.  **Performing BLAST Search on CARD:**

    -   On the CARD website, locate the "BLAST" or "Sequence Analysis"
        tool.

    -   Select "Protein BLAST" as the search type.

    -   Paste your protein sequence(s) in the input box. You can paste
        multiple sequences at once.

    -   Ensure the database selected for BLAST is the "CARD Protein
        Homologs" database.

    -   Adjust other BLAST parameters if needed (e.g., E-value
        threshold), but the defaults are often sufficient for initial
        analysis.

    -   Click "Run BLAST".

4.  **Interpreting CARD BLAST Results:**

    -   Examine the BLAST results for significant hits to known
        antibiotic resistance proteins in CARD.

    -   Pay attention to the following information for each hit:

        -   **Percent Identity:** The percentage of identical amino
            acids between your sequence and the CARD sequence. Higher
            identity generally suggests a closer relationship.

        -   **E-value:** The expectation value, which estimates the
            number of hits with a similar score that would be expected
            by chance. Lower E-values indicate more significant hits. A
            threshold of $1e-5$ or lower is often used.

        -   **ARO Term (Antibiotic Resistance Ontology Term):** This
            provides a standardized description of the resistance
            mechanism associated with the CARD hit.

        -   **Resistance Mechanism(s):** Describes how the identified
            protein confers resistance (e.g., antibiotic inactivation,
            target alteration).

        -   **Antibiotics Resisted:** Lists the antibiotics that the
            CARD protein is known to confer resistance to.

        -   **Confidence Score/Level:** CARD assigns confidence levels
            to its annotations. Look for hits with high confidence
            scores.

    -   Record the names of potential resistance genes identified based
        on significant BLAST hits to CARD, along with the relevant
        information (ARO term, resistance mechanism, antibiotics
        resisted, confidence score).

### Analyzing Mutations with CARD 

1.  **Focusing on Identified Resistance Genes:**

    -   Select the resistance genes you identified in the previous step
        that are present in both resistant and susceptible strains. This
        will allow you to compare sequences and identify potential
        resistance-conferring mutations.

2.  **Obtaining Protein Sequences for Comparison:**

    -   Retrieve the protein sequences for the selected resistance genes
        from all the bacterial strains you are analyzing (both resistant
        and susceptible).

3.  **Using CARD for Mutation Information**

    -   On the CARD website, search for the specific resistance gene you
        are investigating (e.g., by gene name or protein name).

    -   Navigate to the gene's information page. Look for sections
        related to "Variants", "Mutations", or similar terms.

    -   CARD may provide information about known mutations in this gene
        and their association with antibiotic resistance. This
        information might include the amino acid change (e.g.,
        Gly105Ser) and the effect on resistance.

4.  **Comparing Sequences and Identifying Potential Mutations:**

    -   Use a multiple sequence alignment tool (as described in Phase 4)
        to align the protein sequences of the resistance gene from all
        your strains.

    -   Identify any amino acid differences (mutations) between the
        sequences from resistant strains and the sequences from
        susceptible strains.

5.  **Correlating Mutations with CARD Data**

    -   Compare the mutations you identified in the sequences with the
        information available in CARD about known resistance-conferring
        mutations in that gene.

    -   Record any mutations that match the information in CARD and note
        the associated resistance profile.

    -   If you find mutations not listed in CARD, discuss their
        potential impact based on the protein structure or known
        functional domains (this may require further research).

### Phylogenetic Analysis and Evolutionary Inference 

In this phase, your goal is to understand the evolutionary relationships
between your bacterial strains and explore how these relationships
connect to the presence and evolution of antibiotic resistance. You will
use the genetic sequences of your chosen strains to construct a
phylogenetic tree and overlay the resistance information you gathered
from CARD to draw (evolutionary) conclusions.

### Selecting a Suitable Gene for Phylogeny 

The choice of gene is critical for meaningful phylogenetic analysis.
When guiding your selection, you should consider the following:

-   **Understanding the Purpose of the Phylogeny:**

    -   If you want to explore overall strain relationships, you should
        choose a housekeeping gene. These genes, like *recA* (DNA
        repair), *rpoB* (RNA polymerase subunit), *gyrB* (DNA gyrase),
        or *16S rRNA*, are highly conserved and allow you to identify
        evolutionary branching points. They're less likely to be
        horizontally transferred, making them better markers of vertical
        descent.

    -   If your focus is on resistance evolution, you would use a
        resistance gene. This will help you trace how the resistance
        mechanism emerged, either independently in various lineages or
        through horizontal gene transfer (HGT).

-   **Considering Gene Characteristics:**

    -   Make sure the gene is universally present in all your bacterial
        strains to avoid complications in your analysis.

    -   Aim for a balance in conservation: you need conserved regions
        for alignment and variable regions for evolutionary signals.

    -   Avoid genes that are too conserved or too variable, as they can
        obscure fine details or introduce noise into your analysis.

    -   For housekeeping genes, you should also consider avoiding those
        subject to HGT, as they distort vertical evolutionary insights.

### Multiple Sequence Alignment 

Using a tool like Clustal Omega, you'll align the nucleotide sequences
of your chosen gene across all selected isolates. Alignment is essential
for identifying homologous positions in the sequences and detecting
variations that can inform your phylogenetic tree. Make sure you
generate a clean alignment, introducing gaps appropriately to handle
insertions and deletions.

### Phylogenetic Tree Construction 

To construct your tree, follow these steps:

-   Familiarize yourself with tree components, including branches,
    nodes, tips (leaves), the root, and the scale bar.

-   Use tools like MEGA or Phylogeny.fr to upload your aligned sequences
    and configure tree-building settings.

-   Understand basic tree-building methods:

    -   Distance-based methods (e.g., Neighbor-Joining) are fast and
        straightforward.

    -   Character-based methods (e.g., Maximum Likelihood or Bayesian
        Inference) are more precise but computationally demanding.

-   Select appropriate parameters, such as a simple evolutionary model
    like Kimura-2-parameter and bootstrap analysis to validate the tree.

-   Save your tree in Newick format and visualize it using built-in
    tools from MEGA or Phylogeny.fr.

### Interpreting the Phylogenetic Tree Using CARD Data 

This phase is where you integrate resistance data:

-   **Mapping Resistance Genes on the Tree:** Annotate your tree to
    indicate which strains possess the resistance genes identified in
    Step 3. You can use distinct colors or symbols to represent
    different genes.

-   **Tracing the Acquisition of Resistance Genes:**

    -   Clustered resistance indicates inheritance from a common
        ancestor.

    -   Dispersed resistance suggests independent acquisition through
        HGT.

-   **Correlating Mutations with Evolutionary Branches:** Map
    resistance-conferring mutations to specific branches and explore
    their relationship to resistant phenotypes.

-   **Identifying Evolutionary Pathways to Multidrug Resistance:** Look
    for lineages that accumulate multiple resistance genes or mutations,
    and determine if resistance evolved sequentially or in significant
    events.

By analyzing phylogenetic data alongside resistance information from
CARD, you can uncover how antibiotic resistance arises, spreads, and
evolves. This deeper understanding is essential for combating the spread
of resistance.
<br>
</details>

<details>
<summary>So maybe this?</summary>

## Introduction to TB-Profiler 

TB-Profiler is a web-based/command-line tool designed to predict drug
resistance profiles of *Mycobacterium tuberculosis* (MTB) based on
sequencing data. It analyzes your sequencing reads and identifies
genetic mutations known to be associated with resistance to various
anti-tuberculosis drugs. This tool is particularly useful for
researchers and clinicians in understanding drug resistance patterns in
MTB isolates.

### Accessing TB-Profiler Online Tool 

The TB-Profiler online tool is accessible through a web browser. You can
find it by searching for \"TB-Profiler\" or by directly accessing the
following link: <https://tbdr.lshtm.ac.uk/>.

### Uploading Your FASTQ File 

FASTQ files are a standard format for storing sequencing reads. Each
read typically includes a sequence and a quality score. To analyze your
data, you will need to upload your FASTQ file(s) to the TB-Profiler
website. Follow these steps:

1.  Go to the TB-Profiler website (<https://tbdr.lshtm.ac.uk/>).

2.  On the homepage, you will find an option to upload your sequencing
    data. Look for a button or section labeled \"Upload Data\" or
    similar.

3.  Click on the upload button. You will be prompted to select your
    FASTQ file(s) from your computer.

4.  If your sequencing data consists of paired-end reads, you will need
    to upload two FASTQ files, one for each read direction (e.g.,
    `read_1.fastq.gz` and `read_2.fastq.gz`).

5.  Once you have selected your file(s), click \"Upload\". The upload
    time will depend on the size of your file(s) and your internet
    connection speed.

6.  And now we wait for the results.

### Understanding the Resistance Associated Mutations 

After your FASTQ file(s) are successfully uploaded and processed,
TB-Profiler will generate a report detailing the identified mutations
and their association with drug resistance. Here's how to interpret the
key information in the report:

1.  **Gene Name:** The report will list the genes in which mutations
    were found. These genes are known to be involved in drug resistance
    mechanisms in MTB. Common examples include `rpoB` (associated with
    rifampicin resistance), `katG` and `inhA` (associated with isoniazid
    resistance), and `gyrA` (associated with fluoroquinolone
    resistance).

2.  **Mutation:** This column describes the specific genetic change that
    was detected. Mutations are often described by indicating the
    original nucleotide and its position in the gene, followed by the
    new nucleotide. For example, a mutation might be listed as \"S531L\"
    in the `rpoB` gene. This indicates that at amino acid position 531,
    the amino acid serine (S) has been replaced by leucine (L).
    Sometimes, mutations are described at the nucleotide level, e.g.,
    \"c.1352C\>T\", indicating a change from cytosine (C) to thymine (T)
    at nucleotide position 1352.

3.  **Drug Association:** This column will specify the drug(s) that the
    identified mutation is known to confer resistance to. For example, a
    mutation in the `rpoB` gene is likely to be associated with
    rifampicin resistance.

4.  **Confidence/Evidence Level:** Some reports might include a
    confidence score or evidence level associated with each mutation.
    This indicates how well-established the link between the mutation
    and drug resistance is. Higher confidence scores generally mean a
    stronger association.

5.  **Resistance Prediction:** Based on the identified mutations,
    TB-Profiler will provide an overall prediction of resistance to
    specific drugs. This summary is crucial for understanding the
    potential drug resistance profile of the MTB isolate.

### Example of a TB-Profiler Report Snippet 

Let's consider a hypothetical example of a snippet from a TB-Profiler
report:

    Gene    | Mutation          | Drug Association | Confidence
    --------|-------------------|-------------------|------------
    rpoB    | p.Ser450Leu       | Rifampicin        | High
    katG    | p.Ser315Thr       | Isoniazid         | High
    inhA    | c.-777C>T         | Isoniazid         | Medium

In this example:

-   A mutation (p.Ser450Leu) in the `rpoB` gene is detected, which is
    strongly associated with rifampicin resistance.

-   A mutation (p.Ser315Thr) in the `katG` gene and a mutation
    (c.-777C\>T) in the `inhA` gene are detected, both associated with
    isoniazid resistance.

Based on this example, the MTB isolate is likely resistant to rifampicin
and isoniazid. Therefore, fluoroquinolones have a better chance of being
effective.

### Mutation Visualization 

While the tool primarily provides text-based reports, understanding the
location of mutations within genes can be helpful. Here's an example
image illustrating a gene and a potential mutation site:

![](https://github.com/cb3017/cb3017.github.io/blob/master/figs/rpoB_mutation.png?raw=true)

**Note:** The figure depicts a linear representation of the `rpoB` gene,
showing the DNA sequence with exons and introns. A specific location
(around 751kb) within the `rpoB` gene is highlighted to represent the
position of a mutation. A red box indicates the nucleotide change that
results in the amino acid substitution.

### Important Considerations 

-   **Data Quality:** The accuracy of TB-Profiler's predictions depends
    heavily on the quality of your sequencing data. Ensure your FASTQ
    files are of good quality and have sufficient coverage of the MTB
    genome.

-   **Interpretation:** While TB-Profiler provides valuable information,
    the interpretation of the results should be done carefully and in
    the context of other clinical and laboratory findings.

-   **Database Updates:** The database used by TB-Profiler is regularly
    updated with new information on resistance-associated mutations.
    Ensure you are using the latest version of the tool or database for
    the most accurate results.

-   **Further Analysis:** TB-Profiler primarily focuses on known
    resistance mutations. Further analysis might be needed to
    investigate novel mutations or other factors contributing to drug
    resistance.
<br>
</details> -->