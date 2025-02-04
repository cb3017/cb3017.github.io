## **🔬 Dissecting Snakemake for Differential Expression Analysis**

### **Step 1: The Big Picture (What Are We Building?)**
We want to use **Snakemake** to automate a **RNA-seq Differential Expression Analysis** (DEA) workflow. This typically involves:
1. **Preprocessing** (Trimming, Quality Control)
2. **Alignment & Quantification** (Mapping reads, generating count matrices)
3. **Statistical Analysis** (Differential expression testing)
4. **Visualization & Reports** (MA plots, Volcano plots, PCA, etc.)

### **Step 2: Breaking It Into Modules**
Every workflow consists of a **DAG** (**Directed Acyclic Graph**) of rules. Let's break it into independent **Snakemake rules**:
1. **Raw Data Processing**  
   - Download/Extract raw FASTQ files  
   - Quality control with **FastQC**  
   - Trimming adapters using **Trimmomatic** or **Cutadapt**  

2. **Read Alignment & Quantification**  
   - Align reads to a reference genome using **STAR** or **HISAT2**  
   - Generate gene expression counts with **featureCounts** or **Salmon**  

3. **Statistical Analysis for DEA**  
   - Normalize counts (e.g., **DESeq2**, **edgeR**, **limma**)  
   - Identify differentially expressed genes  

4. **Visualization & Reports**  
   - Generate plots (PCA, heatmaps, volcano plots)  
   - Create an HTML report summarizing results  

---

### **Step 3: Writing the `Snakefile`**
Now, let's write a Snakemake workflow step by step.

#### **1️⃣ Define Input Files and Configurations**
Use a `config.yaml` to store parameters:
```yaml
samples: ["sample1", "sample2", "sample3"]
conditions: ["control", "treated"]
reference: "reference/genome.fa"
annotation: "reference/genes.gtf"
threads: 8
```
This keeps your workflow flexible!

#### **2️⃣ Create the First Rule: Quality Control**
```python
rule fastqc:
    input:
        "raw_reads/{sample}.fastq.gz"
    output:
        "qc/{sample}_fastqc.html",
        "qc/{sample}_fastqc.zip"
    conda:
        "envs/fastqc.yaml"
    shell:
        "fastqc -o qc/ {input}"
```
- This rule runs **FastQC** on each raw FASTQ file.
- Uses a **Conda environment** (`envs/fastqc.yaml`) for reproducibility.

#### **3️⃣ Read Alignment with STAR**
```python
rule align:
    input:
        "raw_reads/{sample}.fastq.gz"
    output:
        "alignments/{sample}.bam"
    params:
        index="reference/star_index"
    conda:
        "envs/star.yaml"
    shell:
        "STAR --genomeDir {params.index} --readFilesIn {input} --outFileNamePrefix alignments/{wildcards.sample}"
```
- Uses **STAR** for alignment.
- Reads the reference genome from the **params** section.

#### **4️⃣ Generate Gene Counts**
```python
rule count_reads:
    input:
        "alignments/{sample}.bam"
    output:
        "counts/{sample}.txt"
    conda:
        "envs/featurecounts.yaml"
    shell:
        "featureCounts -T {config[threads]} -a {config[annotation]} -o {output} {input}"
```
- Uses **featureCounts** to generate count matrices.

#### **5️⃣ Perform DEA with DESeq2**
```python
rule deseq2_analysis:
    input:
        expand("counts/{sample}.txt", sample=config["samples"])
    output:
        "results/deseq2_results.csv"
    conda:
        "envs/deseq2.yaml"
    script:
        "scripts/deseq2_analysis.R"
```
- Runs a **DESeq2 R script** for differential expression analysis.

---

### **Step 4: Stitching It All Together**
Now, define a final rule to generate a report:

```python
rule report:
    input:
        "results/deseq2_results.csv",
        "plots/volcano.png",
        "plots/pca.png"
    output:
        "final_report.html"
    conda:
        "envs/rmarkdown.yaml"
    script:
        "scripts/generate_report.R"
```

---

## **Final Breakdown of Snakemake Components**
1. **Config Files (`config.yaml`)** → Stores paths, parameters, sample lists.
2. **Rules (`Snakefile`)** → Defines workflow steps as modular rules.
3. **Wildcards (`{sample}`)** → Dynamic pattern matching.
4. **Dependencies (`envs/*.yaml`)** → Manages software environments.
5. **Scripts (`scripts/*.R`)** → Custom analysis scripts.
6. **Output Directories (`results/`, `plots/`, `counts/`)** → Organized data flow.

---

## **Running the Workflow**
After writing the `Snakefile`, run:
```bash
snakemake --cores 8
```
To create a DAG visualization:
```bash
snakemake --dag | dot -Tpng > workflow.png
```
To ensure reproducibility:
```bash
snakemake --use-conda --cores 8
```

---


```python
# config.yaml (store separately)
configfile: "config.yaml"

# Define the samples
SAMPLES = config["samples"]

# Rule: Quality Control with FastQC
rule fastqc:
    input: "raw_reads/{sample}.fastq.gz"
    output: "qc/{sample}_fastqc.html"
    conda: "envs/fastqc.yaml"
    shell: "fastqc -o qc/ {input}"

# Rule: Read Alignment with STAR
rule align:
    input: "raw_reads/{sample}.fastq.gz"
    output: "alignments/{sample}.bam"
    params: index="reference/star_index"
    conda: "envs/star.yaml"
    shell: "STAR --genomeDir {params.index} --readFilesIn {input} --outFileNamePrefix alignments/{wildcards.sample}"

# Rule: Generate Gene Counts
rule count_reads:
    input: "alignments/{sample}.bam"
    output: "counts/{sample}.txt"
    conda: "envs/featurecounts.yaml"
    shell: "featureCounts -T {config[threads]} -a {config[annotation]} -o {output} {input}"

# Rule: Differential Expression Analysis using DESeq2
rule deseq2_analysis:
    input: expand("counts/{sample}.txt", sample=SAMPLES)
    output: "results/deseq2_results.csv"
    conda: "envs/deseq2.yaml"
    script: "scripts/deseq2_analysis.R"

# Rule: Generate Final Report
rule report:
    input:
        "results/deseq2_results.csv",
        # "plots/volcano.png",
        # "plots/pca.png"
    output: "final_report.html"
    conda: "envs/rmarkdown.yaml"
    script: "scripts/generate_report.R"

# Default rule to run the full pipeline
rule all:
    input:
        expand("qc/{sample}_fastqc.html", sample=SAMPLES),
        expand("alignments/{sample}.bam", sample=SAMPLES),
        expand("counts/{sample}.txt", sample=SAMPLES),
        "results/deseq2_results.csv",
        "final_report.html"

```


```yaml
samples: ["sample1", "sample2", "sample3"]
conditions: ["control", "treated"]
reference: "reference/genome.fa"
annotation: "reference/genes.gtf"
threads: 8
```

```sh
snakemake --use-conda --cores 8
```

# **Reproducible and Scalable Workflows with Snakemake**  


## **Lesson Objectives**  
By the end of this lesson, students will be able to:  
✅ Connect to an **HPC cluster** and navigate the command line  
✅ Install **Miniconda** and configure **Micromamba** for package management  
✅ Set up a **Snakemake** environment and execute workflows  
✅ Develop a simple **differential expression (DE) analysis pipeline** using Snakemake  

---

## **Lesson Outline**  

### **🟢 Part 1: Logging into an HPC Cluster**  
⏳ **Time: 15 min**  
📌 **Goal**: Ensure students can access the HPC system  

1. **Accessing the cluster**  
   - Explain SSH and login process  
   - Example (replace `username` and `hpc.example.edu` accordingly):  
     ```bash
     ssh -X username@hpc.example.edu
     ```  
   - Troubleshooting SSH access  

2. **Basic commands for navigation**  
   - Checking **storage quota** (`du -sh`, `df -h`)  
   - Using **screen** or **tmux** to prevent session loss  

---

### **🟢 Part 2: Installing Miniconda and Configuring Micromamba**  
⏳ **Time: 20 min**  
📌 **Goal**: Set up an efficient package management system  

1. **Installing Miniconda (if not available on HPC)**  
   - Download and install Miniconda:  
     ```bash
     wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
     bash Miniconda3-latest-Linux-x86_64.sh
     ```  
   - Follow installation prompts and initialize Conda  

2. **Installing Micromamba for faster environment management**  
   - Install Micromamba:  
     ```bash
     conda install -c conda-forge micromamba
     ```  
   - Add Micromamba to shell profile:  
     ```bash
     echo 'export MAMBA_EXE=$(which micromamba)' >> ~/.bashrc
     echo 'export MAMBA_ROOT_PREFIX=~/miniconda3/bin/micromamba' >> ~/.bashrc
     source ~/.bashrc
     ```  
   - Test installation with `micromamba --help`  

---

### **🟢 Part 3: Creating a Snakemake Environment**  
⏳ **Time: 15 min**  
📌 **Goal**: Set up an isolated environment for Snakemake  

1. **Creating a new environment**  
   ```bash
   micromamba create -n snakemake -c conda-forge snakemake-minimal
   ```  
2. **Activating the environment**  
   ```bash
   micromamba activate snakemake
   ```  
3. **Testing installation**  
   ```bash
   snakemake --help
   ```

---

### **🟢 Part 4: Writing a Differential Expression (DE) Analysis Pipeline**  
⏳ **Time: 1.5 - 2 hours**  
📌 **Goal**: Develop a complete Snakemake workflow  

#### **Step 1: Define Inputs and Outputs**  
- Explain Snakemake workflow logic  
- Introduce **Snakefile** structure  
- Define required input files:  
  - RNA-seq FASTQ files  
  - Reference genome  
  - Sample metadata  

#### **Step 2: Create a Simple Snakefile**  
1. **Start with a rule to download sample data**  
   ```python
   rule download_data:
       output: "data/samples.fastq.gz"
       shell: "wget -O {output} https://example.com/sample_data.fastq.gz"
   ```  

2. **Add a rule for quality control using FastQC**  
   ```python
   rule fastqc:
       input: "data/samples.fastq.gz"
       output: "qc/samples_fastqc.html"
       shell: "fastqc -o qc {input}"
   ```  

3. **Align reads using STAR or Hisat2**  
   ```python
   rule align:
       input: "data/samples.fastq.gz"
       output: "mapped/sample.bam"
       shell: "hisat2 -x reference -U {input} -S {output}"
   ```  

4. **Quantify gene expression with featureCounts**  
   ```python
   rule count_reads:
       input: "mapped/sample.bam"
       output: "counts/sample_counts.txt"
       shell: "featureCounts -a annotation.gtf -o {output} {input}"
   ```  

5. **Perform differential expression analysis with DESeq2 (R script)**  
   ```python
   rule deseq2:
       input: "counts/sample_counts.txt"
       output: "results/differential_expression_results.csv"
       script: "scripts/deseq2_analysis.R"
   ```  

---

### **🟢 Part 5: Running the Workflow and Debugging**  
⏳ **Time: 30 min**  
📌 **Goal**: Run the pipeline and interpret results  

1. **Test workflow execution step-by-step**  
   ```bash
   snakemake -n
   ```  
   (Dry-run to check dependencies)  

2. **Run the full pipeline with HPC submission**  
   ```bash
   snakemake --use-conda --cores 4
   ```  
   OR submit as a **batch job** using SLURM:  
   ```bash
   sbatch --cpus-per-task=4 --wrap="snakemake --use-conda"
   ```  

3. **Troubleshooting errors**  
   - Check logs in `.snakemake/log/`  
   - Debugging with `snakemake -p --reason`  

---

### **🟢 Part 6: Scaling Up and Best Practices**  
⏳ **Time: 20 min**  
📌 **Goal**: Learn how to make workflows more scalable  

1. **Using configuration files (`config.yaml`)**  
   ```yaml
   samples: ["sample1", "sample2", "sample3"]
   reference: "reference.fasta"
   ```  

2. **Adding rule dependencies**  
   ```python
   rule all:
       input: expand("results/{sample}_deseq2.csv", sample=config["samples"])
   ```  

3. **Using cluster profiles for HPC submission**  
   ```bash
   snakemake --cluster "sbatch -c {threads}" --jobs 10
   ```  

### **Snakefile for Paired-End RNA-Seq Differential Expression Analysis**

---

### **`config.yaml`**
```yaml
samples: ["sample1", "sample2", "sample3"]
reference:
  index: "reference/index"  # Path to reference index directory
alignment_tool: "salmon"  # Options: "salmon" or "star"
read_type: "PE"  # Options: "SE" (Single-End) or "PE" (Paired-End)
```

---

### Snakefile

```python
configfile: "config.yaml"

rule all:
    input:
        expand("results/{sample}_deseq2.csv", sample=config["samples"]),
        "qc/multiqc_report.html"

rule fastqc:
    input:
        R1="data/{sample}_R1.fastq.gz",
        R2="data/{sample}_R2.fastq.gz" if config["read_type"] == "PE" else None
    output:
        "qc/{sample}_R1_fastqc.html",
        "qc/{sample}_R2_fastqc.html" if config["read_type"] == "PE" else None
    shell:
        """
        fastqc -o qc {input.R1} {input.R2} 2>/dev/null || fastqc -o qc {input.R1}
        """

rule multiqc:
    input:
        expand("qc/{sample}_R1_fastqc.html", sample=config["samples"]),
        expand("qc/{sample}_R2_fastqc.html", sample=config["samples"]) if config["read_type"] == "PE" else []
    output:
        "qc/multiqc_report.html"
    shell:
        "multiqc -o qc qc/"

rule align:
    input:
        R1="data/{sample}_R1.fastq.gz",
        R2="data/{sample}_R2.fastq.gz" if config["read_type"] == "PE" else None,
        index=config["reference"]["index"]
    output:
        "mapped/{sample}.bam"
    params:
        alignment_tool=config["alignment_tool"],
        read_type=config["read_type"]
    shell:
        """
        if [ "{params.alignment_tool}" == "salmon" ]; then
            if [ "{params.read_type}" == "PE" ]; then
                salmon quant -i {input.index} -l A -1 {input.R1} -2 {input.R2} -o {wildcards.sample}_salmon
            else
                salmon quant -i {input.index} -l A -r {input.R1} -o {wildcards.sample}_salmon
            fi
        elif [ "{params.alignment_tool}" == "star" ]; then
            if [ "{params.read_type}" == "PE" ]; then
                STAR --runThreadN 4 --genomeDir {input.index} --readFilesIn {input.R1} {input.R2} --outFileNamePrefix {wildcards.sample}_STAR
            else
                STAR --runThreadN 4 --genomeDir {input.index} --readFilesIn {input.R1} --outFileNamePrefix {wildcards.sample}_STAR
            fi
            samtools sort -o {output} {wildcards.sample}_STARAligned.out.sam
        else
            echo "Invalid alignment tool specified in config.yaml"
            exit 1
        fi
        """

rule count_reads:
    input:
        bam="mapped/{sample}.bam",
        annotation="annotation.gtf"
    output:
        "counts/{sample}_counts.txt"
    shell:
        "featureCounts -T 4 -p {'' if config['read_type'] == 'SE' else '-p'} -t exon -g gene_id -a {input.annotation} -o {output} {input.bam}"

rule deseq2:
    input:
        counts=expand("counts/{sample}_counts.txt", sample=config["samples"])
    output:
        "results/differential_expression_results.csv"
    script:
        "scripts/deseq2_analysis.R"
```

### **How to Run**
1. **Activate the environment**  
   ```bash
   micromamba activate snakemake
   ```
2. **Dry-run to check the workflow**  
   ```bash
   snakemake -n
   ```
3. **Execute with 4 cores**  
   ```bash
   snakemake --cores 4
   ```
4. **Submit to HPC with SLURM**  
   ```bash
   sbatch --cpus-per-task=4 --wrap="snakemake --use-conda"
   ```