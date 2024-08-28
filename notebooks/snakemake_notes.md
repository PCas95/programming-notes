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

## Writing and Running Snakefile Workflows

<https://snakemake.readthedocs.io/en/stable/snakefiles/rules.html>

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

In the example below, the first block of code is a `rule all` block. Its `input` block specifies that the final outputs that need to be produced by the workflow are `data/output/file1.txt` and `data/output/file2.txt`. After that rule, other rules are present in the workflow, which will be executed as needed to produce the target files. In this case only another rule is listed: `rule convert` takes as input all `.txt` files in `data/input` to produce files with the same names in `data/output`; to do that, it will run the Python script `convert_to_uppercase.py` as specified in the `shell` block.

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

> A rule's name is usefule to reference it later on, and to describe what's inside it, but Snakemake does not necessarily need rule names: a nameless rule is called "implicit rule" (`rule:`).

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

### Wildcards and Objects

Snakemake wildcards are replaced by the regular expression `.+`. Basically, everything that is matched by `.+` is expanded in the wildcard (see examples below).

* `wildcards`: a Snakemake object that allows to access the wildcard values that are matched in the input and output file patterns of the rule. Example:

```py
# given that sample_{any_wildcard_name}.txt matches the values '1' and '2'
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
# then wildcards holds the matched values (`.+`, but in between 'sample_' and '.txt');
# using wildcards.sample_size extracts from the object the value of the wildcard named {sample_size}
```

In case of ambiguity for the wildcard interpretation, the regex used to generate the wildcards can be overridden by constraining it in 3 different ways:

```py
# 1. constraining the pattern in the wildcard by appending a regex pattern to the wildcard name, after a comma, inside the curly braces.
rule all:
	input:
		"data/input/file_{input_number,\d+}.fasta
# 2. constraining the pattern in the rule via the `wildcard_constraints` keyword.
rule all:
	input:
		"data/input/file_{input_number}.fasta
    	wildcard_constraints:
        	input_number="\d+"
# 3. constraining the wildcards globally
wildcard_constraints:
	input_number="\d+"
rule A:
	...
rule B:
	...
```

### Helper functions to pass input functions

Input files can be Python lists ("Aggregation" of inputs), but Snakemake can also receive inputs using a collection of helper functions that facilitate aggregation. 

```py
import os, sys
path = '/input'
def get_input_files():
	return = sorted([os.path.join(path, i) for i in os.listdir(path)])

rule:
	input:
		get_input_files()
```

#### `unpack()`

Given a function that generates a dictionary with input names as keys and file names as values, the `unpack()` keyword is used to return named input files from that function.

```py
def myfunc(wildcards):
    return {'name_1': '{wildcards.token}.txt'.format(wildcards=wildcards)}

rule:
	input:
		unpack(myfunc)
```

### Helper functions to pass input and output files

#### `expand()`

The `expand()` function can pass inputs in place of a list comprehension and can also be used to combine different variables. **Note that** in the following cases `{dataset}` and `{ext}` are not wildcards, but placeholders that are expanded to whatever is defined in the additional arguments (`DATASETS` and `FORMATS`, which are previously defined lists).

```py
rule aggregate:
    input:
        expand("{dataset}/a.{ext}", dataset=DATASETS, ext=FORMATS)
    output:
        "aggregated.txt"
    shell:
        ...
```

If `FORMATS=["txt", "csv"]` contains a list of desired output formats, `expand()` will automatically combine any dataset with any of these extensions. Furthermore, the first argument can also be a list of strings. In that case, the transformation is applied to all elements of the list:

```py
expand(["{dataset}/a.{ext}", "{dataset}/b.{ext}"], dataset=DATASETS, ext=FORMATS)
```

> `expand()` uses Python `itertools`'s function `product` to cretate combinations of values, however that function can be replaced by a different combinatoric function using a second positional argument (*e.g.* `expand(["{dataset}/a.{ext}", "{dataset}/b.{ext}"], zip, dataset=DATASETS, ext=FORMATS)`). 

Argument values passed to `expand()` can also be functions or lists of functions if the return value of `expand()` or `expand()` itself is used within `input` or `params`.

#### `multiext()`

`multiext()` provides a simplified variant of `expand()` that allows to define a set of output or input files that just differ by their extension:

```py
rule plot:
    input:
        ...
    output:
        multiext("some/plot", ".pdf", ".svg", ".png")
    shell:
        ...
```

The effect is the same as writing `expand("some/plot{ext}", ext=[".pdf", ".svg", ".png"])`.

### Semantic helper functions

#### `collect()`

The `collect()` function is just an alias for the `expand()` function.

#### `lookup()`

`lookup()` is a function that allows to fetch a value from a python mapping object (*i.e.* a dictionary) or a `pandas`/`numpy` dataframe/series.

```py
lookup(
    dpath: Optional[str | Callable] = None,
    query: Optional[str | Callable] = None,
    cols: Optional[List[str]] = None,
    is_nrows: Optional[int], within=None
)
```

It's used, for example, to assign fetched values to a variable, used by `expand()`. Its arguments expect parameters to retrieve the necessary information to get the value(s):

* The `within` parameter takes a python mapping object (dictionary), a pandas dataframe, or series;
* If a dictionary is passed to `within`, `lookup()` expects the `dpath` argument, otherwise it expects the `query` argument. Both `dpath` and `query` can be passed a function;

Example:

```py
expand("results/{item.sample}.txt", sample=lookup(query="someval > 2", within=samples))
```

#### `branch()`

The `branch()` function allows to choose different input files based on a conditional statement.

```py
branch(
    condition: Union[Callable, bool],
    then: Optional[Union[str, list[str], Callable]] = None,
    otherwise: Optional[Union[str, list[str], Callable]] = None,
    cases: Optional[Mapping] = None
)
```

The `condition` arguemnt has to be a function or expression that evaluates to a Boolean value. If it is a function, it needs to take only wildcards as parameters.

The `then` and `otherwise` arguments allow `branch()` to use as input the file(s) specified for `then` if the conditional statement evaluates to `True` and those specified for `otherwise` if it evaluates to `False`.

```py
def use_sometool(wildcards):
    # determine whether the tool shall be used based on the wildcard values.
    ...

rule a:
    input:
        branch(
            use_sometool,
            then="results/sometool/{dataset}.txt",
            otherwise="results/someresult/{dataset}.txt"
        )
```

#### `evaluate()`

Evaluates a Python expression containing wildcards:

```py
rule a:
input:
    branch(evaluate("{sample} == '100'"), then="a/{sample}.txt", otherwise="b/{sample}.txt"),
output:
    "c/{sample}.txt",
shell:
    ...
```

#### `exists()`

The `exists()` function allows to check whether a file exists or not.

---

> Most of the times a rule in a workflow needs to get inputs and generate outputs dinamically, starting from files that match wildcards. This is especially true for bioinformatics workflows that work on many files and, on top of that, paired-end reads.
> In such cases, functions are used to pass inputs to the rule: lambda functions are often proposed as a clean and fast option, but defining one's own functions is better: they are easier to understand and can be defined separately and imported in the `Snakefile` whenever needed, so that if the files and directroy structure grant reproducibility, Snakemake will also grant to avoid redundancy and verboseness.

### Running a `Snakefile`

Running the command `snakemake` will automatically look  in current directory to execute a file named `Snakefile`, using the resource parameters specified in such file. Snakefiles, however, can be named in any way, but that also requires to explicitly pass the name of the file:

```sh
$ snakemake --snakefile trimming_fastp.smk
```

Other useful flags for CLI execution:

```sh
$ snakemake --dryrun	# tests if the workflow is defined properly and estimates the computation time (no execution)
$ snakemake --cores 1	# overrides default cores usage for the whole workflow
$ snakemake --resources mem_gb=8	# overrides default memory usage for the whole workflow
$ snakemake --resources cores=4 mem_gb=8	# overrides multiple default resource parameters for the whole workflow
```
