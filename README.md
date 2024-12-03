# Bio312-Extra-Credit


## Lab 3:


The first step taken was to uncompress the given proteomes in the lab3 repository:
```
gunzip proteomes/*.gz
```


The protein sequences were then placed into a single file ``allprotein.fas``
```


cat proteomes/*.faa>allprotein.fas
```


A blast database was then build using this file:
```
makeblastdb -in allprotein.fas -dbtype prot
```


A directory was created in lab 3, where the following files will go:
```
mkdir ~/lab03-$MYGIT/sirtuin
```


The given sirtuin protein was downloaded into the sirtuin directory:
```
ncbi-acc-download -F fasta -m protein "NP_057623.1"
```


The BLAST search was performed on this protein. The matches were placed into the file ``sirtuin.blastp.detail.out``:
```
blastp -db ../allprotein.fas -query NP_057623.1.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out sirtuin.blastp.detail.out
```


The returns from the BLAST search was filtered out. Only the matches that has an e-value less than 1e-14 was kept. These matches were placed into the file ``sirtuin.blastp.detail.filtered.out``:
```
awk '{if ($6< 1e-14)print $1 }' sirtuin.blastp.detail.out > sirtuin.blastp.detail.filtered.out
```


## Lab 4:


A directory was created in lab 4, where the following files will go:
```
mkdir ~/lab04-$MYGIT/sirtuin
```


The filtered out sequences were pulled out of the file ``allprotein.fas`` (In the lab 3 repository). These sequences were then placed in the file ``sirtuin.homologs.fas``:
```
seqkit grep --pattern-file ~/lab03-$MYGIT/sirtuin/sirtuin.blastp.detail.filtered.out ~/lab03-$MYGIT/allprotein.fas | seqkit grep -v -p "carpio" > ~/lab04-$MYGIT/sirtuin/sirtuin.homologs.fas
```


A multiple sequence alignment was done on the sequences in the file ``sirtuin.homologs.fas`` using muscle. The output was placed in the file ``sirtuin.homologs.al.fas``
```
muscle -align ~/lab04-$MYGIT/sirtuin/sirtuin.homologs.fas -output ~/lab04-$MYGIT/sirtuin/sirtuin.homologs.al.fas
```
## Lab 5:


A directory was created in lab 5, where the following files will go:
```
mkdir ~/lab05-$MYGIT/sirtuin
```


The sequences in the file ``sirtuin.homologs.al.fas`` (in the lab 4 repository) was taken and placed in the file ``sirtuin.homogsf.al.fas``. First though, sequences that contained a duplicate label tag was removed:
```
sed 's/ /_/g'  ~/lab04-$MYGIT/sirtuin/sirtuin.homologs.al.fas | seqkit grep -v -r -p "dupelabel" >  ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.fas
```


IQ-TREE was used to find the maximum likehood tree estimate of the sequences in the file ``sirtuin.homogsf.al.fas``. A tree file (``sirtuin.homologsf.al.fas.treefile``) and a .iqtree file (``sirtuin.homologsf.al.fas.iqtree``) was outputted:
```
iqtree -s ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.fas -bb 1000 -nt 2
```


The tree in the file ``sirtuin.homologsf.al.fas.treefile`` was rooted at the midpoint. The result of the midpoint rooted tree was placed in the file ``sirtuin/sirtuin.homologsf.al.mid.treefile``:
```
gotree reroot midpoint -i ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.fas.treefile -o ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile
```


## Lab 6:


A directory was created in lab 6, where the following files will go:
```
mkdir ~/lab06-$MYGIT/sirtuin
```


A copy of the midpoint rooted tree in the file "sirtuin.homogsf.al.mid.treefile" (In the lab 5 respository) was made and placed into the file ``sirtuin.homolosf.al.mid.treefile``:
```
cp ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile
```


The reconcilation of the gene tree with the species tree was performed. The output was placed in the file ``sirtuin.homologsf.pruned.treefile.rec.events.txt``:
```
java -jar ~/tools/Notung-3.0_24-beta/Notung-3.0_24-beta.jar -s ~/lab05-$MYGIT/species.tre -g ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile --reconcile --speciestag prefix --savepng --events --outputdir ~/lab06-$MYGIT/sirtuin/
```


A RecPhyloXML object was generated from this file. The resulting file is called ``sirtuin.homologsf.al.mid.treefile.rec.ntg.xml``:
```
python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile.rec.ntg --include.species
```


From this file, a gene-reconciliation-within species tree reconciliation graphic was created. The output was in the file ``sirtuin.homogsf.al.mid.treefile.rec.svg``:
```
thirdkind -Iie -D 40 -f ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile.rec.ntg.xml -o  ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile.rec.svg
```


This file was then converted into a pdf:
```
convert  -density 150 ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile.rec.svg ~/lab06-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile.rec.pdf
```


## Lab 8:


A directory was created in lab 8, where the following files will go:
```
mkdir ~/lab08-$MYGIT/sirtuin
```


A copy of the raw unaligned sirtuin sequences in the file ``sirtuin.homologs.fas`` (In the lab 6 repository) was made, as well as the stop codons being removed. This was then placed in the file ``sirtuin.homologs.fas``:
```
sed 's/*//' ~/lab04-$MYGIT/sirtuin/sirtuin.homologs.fas > ~/lab08-$MYGIT/sirtuin/sirtuin.homologs.fas
```


The sequences in this file was put through RPS-BLAST. The results were placed in the file ``sirtuing.rps-blast.out``:
```
rpsblast -query ~/lab08-$MYGIT/sirtuin/sirtuin.homologs.fas -db ~/data/Pfam/Pfam -out ~/lab08-$MYGIT/sirtuin/sirtuin.rps-blast.out  -outfmt "6 qseqid qlen qstart qend evalue stitle" -evalue .0000000001
```


The gene tree from lab was copied over to the sirtuin directory:
```
cp ~/lab05-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile ~/lab08-$MYGIT/sirtuin
```


The script written by Dr. Rest in R was used to combine the PFAM domains with the gene tree. The result was placed in file ``sirtuin.tree.rps.pdf``:
```
Rscript  --vanilla ~/lab08-$MYGIT/plotTreeAndDomains.r ~/lab08-$MYGIT/sirtuin/sirtuin.homologsf.al.mid.treefile ~/lab08-$MYGIT/sirtuin/sirtuin.rps-blast.out ~/lab08-$MYGIT/sirtuin/sirtuin.tree.rps.pdf
```




