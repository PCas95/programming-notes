# Useful Bash Commands

> Useful Bash commands, one-liners and snippets adapted from previous work or training. They proved useful in more than one occasion.

## `mkdir` Command Template for Project Directory

```sh
mkdir -p name-YYYYMMDD/{input/{reference,fastqgz,fastqgz40X,fasta,vcf,gff,nwk,Rtab,kmers,matrix,pheno},output/{genes,variants,kmers},cmd,lists}
```

## Reorder Columns One-liner

### Using `awk`

```sh
awk '{print $2, $1, $3}' test.tsv
```

### Using `paste`

```sh
paste <(cut -f 2 file.tsv) <(cut -f 1 file.tsv) > new_file.tsv
```

> **Note:** **do not** sort the file lines, since that can and will break the correspondence of cells for each line. By leaving the original file untouched, we are sure that each line will be kept at the same position, while changing the order of columns.


## Rename Files Based on List

### Method 1 - Read `tsv` file splitting it into columns and generate variables with old and new name

```sh
while IFS=$'\t' read -r col1 col2; do
	old_name=${col1}.fasta;
	new_name=${col2}.fasta;
	if test -f 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${old_name}; then
		mv 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${old_name} 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${new_name}
	fi
done < corrispondenze2.tsv
```

### Method 2 - Read `tsv` file and split lines to create an array 

```sh
while IFS= read -r line <corrispondenze2.tsv; do
	cols=($(echo $line | tr "\t" "\n"));
	echo ${cols[0]};
	echo ${cols[1]};
done
```

## Automatically Give Default Input to Programs that Require User Response

### Using Bash's "Here String" (`<<<`) 

*I run a program in a bash for loop. Such program may, for some iterations, ask for my input (as yes/no) . How can I make a for loop that automatically answers no when prompted during iterations?*

You can achieve this by redirecting the standard input (`stdin`) of your program to provide a default response of 'no'. You can use a Bash "here string" (`<<<`) to supply this input. Here is an example of how you can do it:

```sh
for ((i = 1; i <= 10; i++)); do
	your_program <<< "no";
done
```

Replace `your_program` with the actual command you're running in the loop. This loop will run the program 10 times, automatically providing 'no' as input whenever it prompts for user input. Adjust the loop parameters (i <= 10) to match the number of iterations you need.

### Using Bash's `yes` (for "yes" answer only)

```sh
yes | rm test_dir/*
```

## Check Number of Columns in a File (with `awk`)

```sh
# Nicolas' original command
awk -F'\t' '{print NF}' file.vcf | sort -nu | tail -n 1

# My version
awk -F"\t" '{print NF}' input/SRA_biohansel_metadata_wip.tsv | sort -nur | head -n 2
```

> I changed the command a bit to ensure to get the most out of using `awk`: the main reason of using `awk` is that it performs the action on each line, thus printing (in this case) the number of fields of each line. This can be used to check that the number of fields does not change from one line to the other. The commands piped downstream ensure just that: I sort numerically and in decreasing order, ensuring the numbers are unique, then I output the 2 largest numbers. If they are indeed 2 in the list, they will differ and the file will have some rows with a different number of fields.

## Dinamically Write an `sh` Script to Run a Program

```sh
echo "time some_program \
	--options \
	--arguments \
	> output_file" > cmd-some_program.sh
```

## Print List of Available Docker Image Aliases on `gtc-collab-int.izs.intra`

```sh
cat /etc/profile.d/bioinfo_docker_aliases.sh | grep 'export -f' | sed 's@export -f @@'g | sort -f
```

## `while` Loop with 2 Input Files

```sh
while IFS= read -r old_name <&1 && IFS= read -r new_name <&2; do
	mv "$old_name" "$new_name"
done 1<IDs-old.lst 2<IDs-new.lst
```

## updating paths (check out what was that I wanted to do here)

```sh
rm /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_dataframe.tsv
export line=1
for j in `cat /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/samples_phenotype_dataframe.tsv`;
do
	echo -e $j"\t"`sed -n "$line"p /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_col2_1.tsv`;
	line=$((line + 1))
done >> /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_dataframe.tsv
```

## Transpose a Tabular File (check if it works!!)

### Method 1

```sh
export line=0
words=$(awk -F "\t" '{print NF; exit}' input/pheno/no-loci_phenotype_dataframe.tsv)
for j in `cat input/pheno/no-loci_phenotype_dataframe.tsv | wc -l`;
do
	line=$((line + 1))
	sed "$line s@\t@\n@g" input/pheno/no-loci_phenotype_dataframe.tsv | head -n $words \
	> input/pheno/phenotype_col2_"$line".tsv;
done
```

###  Method 2

```sh
col="$(head -1 header-ID-REF-allele-assembly-BioNumericsAF.tsv | wc -w)"
for i in $(seq 1 $col); do
	awk '{ print $'$i' }' header-ID-REF-allele-assembly-BioNumericsAF.tsv | paste -s -d "\t"
done >> TRANS-header-ID-REF-allele-assembly-BioNumericsAF.tsv
```

## Split and Remove Substring from all Columns in Tabular File

```sh
cat file.tsv \
	| tr "_" "\t" | cut --complement -f1,2 \
	> cut_file.tsv
```

> This transforms any character in a column into a `\t` with `tr`, so that the newly formed field can be removed with `cut`. This method could be used in case `sed` cannot be used for some reason.

## `sort` Keeping Header on Top

```sh
head -n 1 output/variants/snps_merged_int.tsv \
	> output/variants/snps_merged_ord-header.tsv ; \
	tail -n +2 output/variants/snps_merged_int.tsv | sort | uniq \
	> output/variants/snps_merged_ord-data.tsv ; \
	cat output/variants/snps_merged_ord-header.tsv output/variants/snps_merged_ord-data.tsv \
	> output/variants/snps_merged_ord.tsv
```	

## Check if Substring is in String (`if` Condition)

```sh
	if [[ $line == *"$i"* ]]; then
		echo "${i} ${line}";
	fi
```

## Example of Shell Parameter Expansion in `for` Loop 

```sh
rm sample-IDs-fasta-paths.lst
for line in $(cat paths-fasta.lst); do
	f=$(basename ${line});
	g=${f#*_};
	echo "${g%%_*} ${line}";
done >> sample-IDs-fasta-paths.lst
```
