# Esempi di Generazione Dinamica per Valentina 

Guarda prima l'[esempio 2](#esempio-2) che forse è più facile capire il concetto.

## Esempio 1

Quello qui sotto è un esempio da un vecchio lavoro:

```bash
rm cmd-bbnorm.sh
for path in `cat paths-fastqgz.lst`; do
export ID=`basename $path`;
echo "bbnorm \
    in=${path}_R1.fastq.gz \
    in2=${path}_R2.fastq.gz \
    out=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X_R1.fastq.gz \
    out2=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X_R2.fastq.gz \
    hist=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.hist \
    k=30 \
    target=8 \
    threads=30 \
    2> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.stderr \
    1> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.stdout";
done >> cmd-bbnorm.sh
```

E' un loop che scrive un comando in un file eseguibile. Per ogni iterazione cambia il file di input e i nomi degli output. Ti riscrivo una versione commentata e un esempio di come verrebbe il comando.

```bash
rm cmd-bbnorm.sh    # prima rimuovo il file chiamato 'cmd-bbnorm.sh' (se esiste). Così non rischio di fare un pastrochio se devo provare ad eseguire il loop più volte (alla fine capisci perchè).
for path in $(cat paths-fastqgz.lst); do    # eseguo i comandi nel `for` loop per ogni riga in un file di testo, in cui ho scritto dei percorsi di file senza estensione
    export ID=$(basename $path);    # creo una nuova variabile $ID in cui c'è solo il nome del file, senza il percorso
    # qui comincio a scrivere:
    echo "bbnorm \    # comincio a scrivere con echo (parte da " e finisce dove chiudo le virgolette)
    in=${path}_R1.fastq.gz \    # il parametro `in` diventa il nome del file con la prima estensione (es. /percorso/del/mio/file-A_R1.fastq.gz) 
    in2=${path}_R2.fastq.gz \    # stessa cosa per `in2` (cambia R2)
    out=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X_R1.fastq.gz \    # il parametro `out` si prende un percorso che ho scelto e il nome del file dinamico: /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X_R1.fastq.gz
    out2=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X_R2.fastq.gz \    # lo stesso vale qui e per tutti le righe sotto
    hist=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.hist \
    k=30 \
    target=8 \
    threads=30 \
    2> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.stderr \
    1> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/${ID}-40X.stdout";    # qui finisce il comando echo
done >> cmd-bbnorm.sh    # qui sto scrivendo tutto quello che viene fuori dal mio loop in un file eseguibile (quello che per sicurezza elimino all'inizio)
```

`>> cmd-bbnorm.sh` non sovrascrive: fa "append", quindi per ogni iterazione del loop scrivo una nuova riga nel mio file `.sh`, che quindi poi posso eseguire con `sh cmd-bbnorm.sh`.****

Il file assomiglierà a qualcosa del genere:

```
bbnorm in=/percorso/del/mio/file-A_R1.fastq.gz in2=/percorso/del/mio/file-A_R2.fastq.gz out=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X_R1.fastq.gz out2=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X_R2.fastq.gz hist=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X.hist k=30 target=8 threads=30 2> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X.stderr 1> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-A-40X.stdout
bbnorm in=/percorso/del/mio/file-B_R1.fastq.gz in2=/percorso/del/mio/file-B_R2.fastq.gz out=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-B-40X_R1.fastq.gz out2=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-B-40X_R2.fastq.gz hist=/home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-B-40X.hist k=30 target=8 threads=30 2> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-B-40X.stderr 1> /home/IZSNT/p.castelli/20220705-GWAS-Pyseer/input/fastqgz40X/file-B-40X.stdout
...
...
```

## Esempio 2

### Parte 1

Qui creo un comando per ogni classificatore. Puoi lanciarlo in un qualunque terminale Bash: stampa semplicemente il comando sul terminale, quindi non corri rischi!

```bash
for i in DT KNN LR RF SVC XGB; do
  echo "docker run --rm -u $(id -u):$(id -g) -v $(pwd):/wd nicolasradomski/genomicbasedclassification:1.1.0 modeling -m genomic_profiles_for_modeling.tsv -ph phenotype_dataset.tsv -o output_dir_${i} -x ${i}_FirstAnalysis -da random -s 80 -c ${i} -k 5 -pa tuning_parameters_${i}.txt -de 20 -j 64";
done
```

### Parte 2

Più complesso (nested):

```bash
for classifier in DT KNN LR RF SVC XGB; do
for splitting in 50 60 70 80 90; do
echo "docker run --rm -u $(id -u):$(id -g) -v $(pwd):/wd nicolasradomski/genomicbasedclassification:1.1.0 modeling -m genomic_profiles_for_modeling.tsv -ph phenotype_dataset.tsv -o output_dir_${classifier} -x ${classifier}_FirstAnalysis -da random -s ${splitting} -c ${classifier} -k 5 -pa tuning_parameters_${classifier}.txt -de 20 -j 64";
done
done
```

Anche quello sopra puoi lanciarlo tranquillamente. Non ho messo l'indentazione perchè a volte incollando nel terminale più di un tab triggera l'autocompletamento e sbrodola tutto. Facci attenzione.

Noterai che:

1. nel secondo caso otteniamo 5 comandi per ogni classificatore (6), ovvero tutte le 30 combinazioni di classificatore e splitting
2. il comando come l'ho impostato io stampa solo a terminale il comando. Da lì hai due strade:
   - aggiungi `>> comandi.sh` dopo l'ultimo `done` e poi esegui il file `.sh` come nell'[esempio 1](#esempio-1)
   - rimuovi `echo` e le virgolette attorno al comando
    > Attenzione in questo caso, perchè i comandi verranno eseguiti uno dopo l'altro, quindi mettiti in uno screen e salvati almeno il loop in un documento, così puoi rilanciarlo facendo copia-incolla

Se aumentiamo la complessità, aumentiamo anche il numero di parametri (e di combinazioni di essi) che possiamo generare.

> **Dislaimer:** non è detto che questi metodi siano i più adatti, ma possono aiutare. Fai attenzione quando li lanci e controllali bene.
>
> - E' utile avere il file `.sh` da lanciare (così puoi ispezionare ogni riga), oppure
> - lanciare il loop con `echo ""` e mettere un pipe in `less` (` | less`) dopo l'ultimo `done`, in maniera da stampare solo i comandi e leggerli dentro `less`






















