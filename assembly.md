## Subsample phage fastq file with seqtk. 

	for x in `cat bcodes.txt`; do seqtk sample -s 11 $x"-phage.fastq" 5000 > $x"-phage-sub".fastq; done

## Perform de novo assembly with flye.

	for filn in `cat bcodes.txt`; do flye --meta --min-overlap 1000 --nano-raw $filn"-phage-sub.fastq" --out-dir $filn"_flye"; done

## Copy each assembly.fasta file and rename each file with the name of its barcode. 

	for x in `cat bcodes.txt`; do cp $x"_flye/assembly.fasta" $x".fasta"; done

## Append the barcode name to each contig in its respective assembly file with sed. 
## But check first to see that the expression worked properly. 

	for x in `cat bcodes.txt`; do sed "s/>/>""$x""_/" $x".fasta" | grep ">"; done

## Now do a search and replace in place with sed with the -i argument.

	for x in `cat bcodes.txt`; do sed -i "s/>/>""$x""_/" $x".fasta"; done

## Check your work with grep. 

	grep ">" barcode05.fasta

## Perform similarity search with blastn against viral database.

	for x in `cat bcodes.txt`; do blastn -query $x".fasta" -db /home/virus_db/ref_viruses_rep_genomes -max_target_seqs 10 -outfmt '6 qaccver saccver stitle pident qcovs qlen length mismatch gapopen qstart qend sstart send evalue bitscore' -out $x".br"; done

## Get tophit for each query to read blast results more easily.

	get_tophit.py -i barcode05.br
