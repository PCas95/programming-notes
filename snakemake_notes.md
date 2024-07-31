# Sakemake notes

## How to set up Snakemake (local, for the moment)

### Install Conda

Download installer for Miniconda3 from [Anaconda website](https://docs.anaconda.com/miniconda/#miniconda-latest-installer-links) and install Conda.

```sh
# check file integrity 
sha256sum Downloads/Miniconda3-latest-Linux-x86_64.sh

# run installer
bash Downloads/Miniconda3-latest-Linux-x86_64.sh
```

Follow the installer's instructions. If the default install directory is not the user's Home directory, change it to that.

```sh
# example of installation directory path
/home/IZSNT/p.castelli/miniconda3
```

Say 'yes' to 'Update Shell Profile (Automatic Initialization)'. This will automatically update the user's shell profile by adding Conda's lines to `~/.bashrc`. That entry is necessary for Conda to work, but it will also turn on the automatic activation of Conda's base environment when opening the Terminal. We don't want that: to avoid it, take note of the command suggested by the installer to reverse the auto-activation.

Restart the Terminal once installation is complete, then run the command previously noted down:

```sh
conda config --set auto_activate_base false
```

Then restart the Terminal again.

### Set up a Conda environment with Snakemake

Create a Conda environment for Snakemake (latest Snakemake version to 30/07/2024: 8.16; latest Python version to 30/07/2024: 3.12).

```sh
# create a conda environment with latest python version
conda create -n snakemake -c bioconda -c conda-forge python=3.12

# check available environments
conda info --env
```

Install Snakemake in Conda's new environment.

```sh
# activate conda environment for snakemake
conda activate snakemake

# install snakemake from bioconda channel (repository)
conda install -c bioconda snakemake=8.16

# check installation
snakemake --help | less -S

# deactivate environment
conda deactivate
```

## Running Snakefile workflows

Snakemake workflow's instructions are written in a `Snakefile` file. Examples of `Snakefile`:

```py
rule my_first_rule:
	output:
		'outfile.txt'
	shell:
		'''
		echo 'Hello world!!!' > {output}
		'''
```

```py
rule my_first_rule:
	input:
		'infile.txt'
	output:
		'outfile.txt'
	shell:
		'''
		python3 script.py {input} > {output}
		'''
```

### Rules

The basic building block of a Snakefile is the `rule`. A `rule` block will contain information about the "step" of a workflow or pipeline, which can be composed by many of such steps, connected to one another and able to use as inputs the outputs from other blocks.

Each `rule` can contain information like dedicated memory or CPUs (in specific blocks of code from keywords like `threads`), and is made of 3 other main blocks, with keywords `input`, `output` and `shell`.

The `input` and `output` blocks will list the files to take input from and the target output files, respectively. **They must be provided in quotes**.

The `shell` block will contain the actual process that will be used to produce the desired output from the provided input.

> **Note:** for the 3rd block of a rule, use `shell` to run shell commands, programs or scripts, `script` to run a script in a supported language (Python or R) and `run` to run a block of Python code.

Run a `Snakefile` located in working directory with:

```sh
snakemake --cores 1
```

> **Note:** options like dedicated cores or memory can be set in the block of code of a rule. Arguments from command line take precedence and override that behaviour.

Snakemake also provides a `wrapper` keyword, used to create a block of code in a rule that runs a program from the Snakemake-wrappers repository. It is mainly used to run generalised and frequently used programs (like popular mappers and other bioinformatics tools).

### `rule all`

Usually the first part of a Snakefile is a `rule all` block. `rule all` has an `input` block, used to specify the ultimate targets of the workflow. Snakemake will work backwards from these targets to determine which other rules need to be executed and in what order, so that the files listed in the `rule all`'s `input` block can be created.

In the example below, the firt block of code is a `rule all` block. Its `input` block specifies that the final outputs that need to be produced by the workflow are `data/output/file1.txt` and `data/output/file2.txt`. After that rule, other rules are present in the workflow, which will be executed as needed to produce the target files. In this case only another rule is listed: `rule convert` takes as input all `.txt` files in `data/input` to produce files with the same names in `data/output`; to do that, it will run the Python script `convert_to_uppercase.py` as specified in the `shell` block.

```py
rule all:
	input:
		"data/output/file1.txt",
		"data/output/file2.txt"

rule convert:
	input:
		"data/input/{filename}.txt"
	output:
		"data/output/{filename}.txt"
	shell:
		"python convert_to_uppercase.py {input} {output}"
```

### Configuring resources

Resources like dedicated cores and memory are configured at rule level:

```py
rule convert:
	threads: 2
	resources:
		mem_mb=1024
	input:
		"data/input/{filename}.txt"
	output:
		"data/output/{filename}.txt"
	shell:
		"python convert_to_uppercase.py {input} {output}"
```

The main configurable resources are:
- CPUs (cores, `cores`)
- Memory (RAM, `mem_mb` and `mem_gb`)
- GPU (`gpu`)
- disk (`disk`)

Values for resources can be specified in the `resources` block:

```py
rule my_rule:
	resources:
		cores=4
		mem_gb=2 # or `mem_mb=2048`
```

Resources can be passed in 3 main ways:
- **Integer Value:** directly specifies the value as an integer number, for example memory requirement in MB (`mem_mb`) or GB (`mem_gb`);
- **Dynamic Resource Allocation:** uses a lambda function (or another Python function) for dynamic allocation based on wildcards or other parameters.
- **Named Resource Specifications:** defines custom resource specifications in a `yaml` configuration file and references them in the `rule` `resources` block.

Examples:

```py
# integer value
rule my_rule:
	resources:
		mem_mb=2048
		cores=4

# dynamic resource allocation using a lambda function
rule my_rule:
	resources:
		mem_mb=lambda wildcards: 1024 * int(wildcards.sample_size) # meaning is not important

# named resource specification
rule my_rule:
	resources:
		gpu=config["resources"]["gpu"]
```

Example of a `yaml` configuration file to reference using named resources:

```yaml
# file name: `config.yaml`
resources:
	gpu: 1
```


# QUI

### Wildcards and Objects

* `wildcards`: a Snakemake object that allows to access the wildcard values that are matched in the input and output file patterns of the rule. Example:

```py
# given that sample_{whatever_I_write_here_is_fine}.txt matches the values '1' and '2'
rule all:
	input:
		"data/output/sample_1.txt",
		"data/output/sample_2.txt"

rule convert:
	input:
		"data/input/sample_{sample_size}.txt"
	output:
		"data/output/sample_{sample_size}.txt"
	resources:
		mem_mb=lambda wildcards: 1024 * int(wildcards.sample_size)
# then wildcards holds the matched values;
# using wildcards.sample_size extracts from the object the value of the wildcard named {sample_size}
```