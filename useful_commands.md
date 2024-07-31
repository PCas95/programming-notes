# useful commands taken from previous works on trainings and workflows (Bash-R-training_Data-management-genomics, GWAS-Pyseer, unused_GWAS_commands).


## from casual scripting/everyday work:

### basic mkdir build-up command for usual work/prj (WiP):

```sh
mkdir -p name-YYYYMMDD/{input/{reference,fastqgz,fastqgz40X,fasta,vcf,gff,nwk,Rtab,kmers,matrix,pheno},output/{genes,variants,kmers},cmd,lists}
```

### reorder columns (easy one-liner):

```sh
paste <(cut -f 2 file.tsv) <(cut -f 1 file.tsv) > reordered_file.tsv
```

### for loop to backup/copy/move/rename... files with the same name in multiple directories

```sh
for i in $(ls /home/IZSNT/p.castelli/results/kSNP3)
do
    cp $i/NJ.dist.matrix $i/NJ.dist.matrix-ORIG
    cp $i/SNPs_all_matrix.fasta.mldist $i/SNPs_all_matrix.fasta.mldist-ORIG
    cp $i/SNPs_all_matrix.fasta.nwk $i/SNPs_all_matrix.fasta.nwk-ORIG
done
```

## execute command for each item in directories loop:

```sh
ls kSNP3 > ls-kSNP3.lst && ls ALIAS_*.csv > ls-alias.lst

while IFS= read -r sample <&1 && IFS= read -r csv <&2; do
    if [[ $csv =~ $sample ]]; then
        perl /home/IZSNT/p.castelli/replace_loop.pl $csv kSNP3/$sample/kSNP3_dist.matrix > rename_$sample.sh
        perl /home/IZSNT/p.castelli/replace_loop.pl $csv kSNP3/$sample/iqtree.ml.dist.matrix >> rename_$sample.sh
        perl /home/IZSNT/p.castelli/replace_loop.pl $csv kSNP3/$sample/iqtree.ml.nwk >> rename_$sample.sh
    fi
done 1<ls-kSNP3.lst 2<ls-alias.lst
```

## rename files based on list
### method 1 - read tsv file splitting it into columns, generating variables with old and new name

```sh
while IFS=$'\t' read -r col1 col2; do
    old_name=${col1}.fasta;
    new_name=${col2}.fasta;
    if test -f 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${old_name}; then
        mv 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${old_name} 20240215-ASFV/ASFV_schema2_2_183/schema_seed/${new_name}
    fi
done < corrispondenze2.tsv
```

### method 2 - same as above but instead of reading file splitting its columns, it splits the line and puts it into an array 

```sh
while IFS= read -r line <corrispondenze2.tsv; do
    cols=($(echo $line | tr "\t" "\n"));
    echo ${cols[0]};
    echo ${cols[1]};
done
```

## I run a program in a bash for loop. Such program may, for some iterations, ask for my input (as yes/no) . How can I make a for loop that automatically answers no when prompted during iterations?

You can achieve this by redirecting the standard input (stdin) of your program to provide a default response of 'no'. You can use a Bash here string (<<<) to supply this input. Here is an example of how you can do it:

```sh
for ((i = 1; i <= 10; i++)); do
    # Run your program and redirect 'no' to its input
    your_program <<< "no"
done
```

Replace your_program with the actual command you're running in the loop. This loop will run the program 10 times, automatically providing 'no' as input whenever it prompts for user input. Adjust the loop parameters (i <= 10) to match the number of iterations you need.


## from Bash-R-training_Data-management-genomics:

### check the number of columns in a vcf file (with `awk`)

```sh
awk -F'\t' '{print NF}' file.vcf | sort -nu | tail -n 1 # Nicolas original command

awk -F"\t" '{print NF}' input/SRA_biohansel_metadata_wip.tsv | sort -nur | head -n 2 # my version 2.0
```

I changed the command a bit to ensure to get the most out of usin `awk`: the main reason of using `awk` is that it performs the action on each line, thus printing (in this case) the number of fields of each line. This can be used to check that the number of fields does not change. The commands piped downstream ensure just that: I sort numerically and in decreasing order, ensuring the numbers are unique, then I output the 2 largest numbers. If they are indeed 2 in the list, they will differ and the file will have some rows with a different number of fields.

### write a script for a program

```sh
echo "time some_program \
    --options \
    --arguments \
    > output_file" > cmd-some_program.sh
```

### print a list of available programs in the docker image

```sh
cat /etc/profile.d/bioinfo_docker_aliases.sh | grep 'export -f' | sed 's@export -f @@'g | sort -f
```

### mv files while loop with 2 input lists

```sh
while IFS= read -r old_name <&1 && IFS= read -r new_name <&2; do
    mv "$old_name" "$new_name"
done 1<IDs-old.lst 2<IDs-new.lst
```

## from unused_GWAS_commands:

### Bash script to join unsorted tsv files (check if it works!!)

```sh
rm /home/pierluigi/Documents/joined.tsv
export line=1
for j in `cat /home/pierluigi/Documents/col_1.tsv`;
do
	echo $j"\t"`sed -n "$line"p /home/pierluigi/Documents/col_2.tsv`;
	line=$((line + 1))
done >> /home/pierluigi/Documents/joined.tsv
```

### updating paths (check out what was that I wanted to do here)

```sh
rm /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_dataframe.tsv
export line=1
for j in `cat /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/samples_phenotype_dataframe.tsv`;
do
	echo -e $j"\t"`sed -n "$line"p /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_col2_1.tsv`;
	line=$((line + 1))
done >> /home/IZSNT/p.castelli/20220516-GWAS-Pysee-Docker/input/pheno/phenotype_dataframe.tsv
```

### script to transpose tsv file (check if it works!!)

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

#### another way to transpose (check if it works!!)

```sh
col="$(head -1 header-ID-REF-allele-assembly-BioNumericsAF.tsv | wc -w)"
for i in $(seq 1 $col); do
    awk '{ print $'$i' }' header-ID-REF-allele-assembly-BioNumericsAF.tsv | paste -s -d "\t"
done >> TRANS-header-ID-REF-allele-assembly-BioNumericsAF.tsv
```

### split columns and remove them (transform any character in a column into a \t with `tr` so that the newly formed field can be removed with `cut`)

```sh
cat file.tsv \
    | tr "_" "\t" | cut --complement -f1,2 \
    > cut_file.tsv
```

### create a list of paths to files of different types (using extended RE to get 2 different file extensions)

```sh
find "$PWD"/reference_files/* \
	| grep -P '^.*(\.fasta|\.gff)$' \
	> references.txt
```

### transform trailing newline (\n) into \t to put the paths on the same line as tsv (check if it works!!!)

```sh
tr "\n" "\t" < references.txt > references.tsv
```

### change the position of column 2 in tsv file and transform the first 3 columns into a single column (this uses `awk` to re-order the columns and `sed` to transform the first 2 \t into underscores to merge those fields. The first field will look like 1000426_G_T).

```sh
awk -F "\t" '{print $1,$3,$4,$2,$5,$6,$7,$8,$9,$10,$11}' OFS="\t" \
    file.tsv \
    | sed "s@\t@_@1" | sed "s@\t@_@1" \
    > merged_fields.tsv
```

### `sort` for `join` command that keeps header on top (check if it works!!!)

```sh
head -n 1 output/variants/snps_merged_int.tsv \
    > output/variants/snps_merged_ord-header.tsv ; \
    tail -n +2 output/variants/snps_merged_int.tsv | sort | uniq \
    > output/variants/snps_merged_ord-data.tsv ; \
    cat output/variants/snps_merged_ord-header.tsv output/variants/snps_merged_ord-data.tsv \
    > output/variants/snps_merged_ord.tsv
```    
      
## unused GWAS/ML 4 adp commands:

### if statement to check if string "a" is in string "b"

```sh
	if [[ $line == *"$i"* ]]; then
		echo "${i} ${line}";
	fi
```

### line processing in for loop with shell parameter expansion

```sh
rm sample-IDs-fasta-paths.lst
for line in $(cat paths-fasta.lst); do
	f=$(basename ${line});
	g=${f#*_};
	echo "${g%%_*} ${line}";
done >> sample-IDs-fasta-paths.lst
```
