This is to look for the orthologous in BD_21-3 from Araidopsis thaliana.
The Arabidopsis genes can be found in the data directory.

# Resources:

### Original literature:
- Badri et al., 2009 Plant Physiology

- Link: http://www.plantphysiol.org/content/151/4/2006

### Links for the Arabidopsis protein sequence:
- [Tair10](https://www.arabidopsis.org/servlets/TairObject?id=128747&type=locus)
- AT4G15230.1

### Protein sequences of Brachy (Phytozome13):
- Brachypodium Distachyon Bd21-3
- Brachypodium Distachyon V3

---

# Steps

## 1 Do local blast for Bd21_3: Only pick the first gene model: AT4G15230.1

### (1) Make the local library

./makeblastdb -in /Users/LiLei/Projects/Kate_project/BdistachyonBd21_3_460_v1.1.protein.fa -dbtype prot -out /Users/LiLei/Projects/Kate_project/BdistachyonBd21_3_460_v1.1.protein

### (2) Blast the gene model 1 against the BdistachyonBd21_3 libarary

./blastp -db /Users/LiLei/Projects/Kate_project/BdistachyonBd21_3_460_v1.1.protein -query  /Users/LiLei/Projects/Kate_project/AT4G15230.1.fasta -out /Users/LiLei/Projects/Kate_project/AT4G15230.1_out.txt -outfmt 6

### (3) Take the e-value as 0 and the matched length as 1000
awk '$4>=1000 && $11==0' AT4G15230.1_out.txt |cut -f 2 >candi_genes_Bd21-3


## 2 Do local blast for B.distachyon_V3.1: Only pick the first gene model: AT4G15230.1

### (1) Make he local library

./makeblastdb -in /Users/LiLei/Projects/Kate_project/Bdistachyon_314_v3.1.protein.fa -dbtype prot -out /Users/LiLei/Projects/Kate_project/Bdistachyon_314_v3.1.protein

### (2) Blast the gene model 1 against the BdistachyonBd21_3 libarary

./blastp -db /Users/LiLei/Projects/Kate_project/Bdistachyon_314_v3.1.protein -query  /Users/LiLei/Projects/Kate_project/AT4G15230.1.fasta -out /Users/LiLei/Projects/Kate_project/AT4G15230.1_out_v3.1.txt -outfmt 6


### (3) Take the e-value as 0 and the matched length as 1000

awk '$4>=1000 && $11==0' AT4G15230.1_out_v3.1.txt|cut -f 2 >candi_genes_Bd21_v3.1

## 4 Extract the sequence from the CDS 

### (1) Change the sequence from mutiple lines into single lines
awk '/^>/ { print (NR==1 ? "" : RS) $0; next } { printf "%s", $0 } END { printf RS }' BdistachyonBd21_3_460_v1.1.protein.fa >oneline_BdistachyonBd21_3_460_v1.1.protein.fa

### (2) Extract the sequence according to the list

perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' candi_genes_Bd21-3 oneline_BdistachyonBd21_3_460_v1.1.protein.fa >candi_genes_Bd21-3.fasta

### (3) Change the sequence from mutiple lines into single lines

awk '/^>/ { print (NR==1 ? "" : RS) $0; next } { printf "%s", $0 } END { printf RS }' Bdistachyon_314_v3.1.protein.fa >oneline_Bdistachyon_314_v3.1.protein.fa

### (4) Extract the sequence according to the list

perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' candi_genes_Bd21_v3.1 oneline_Bdistachyon_314_v3.1.protein.fa > candi_genes_Bd21_v3.1.fasta

and then do multiple sequence alignment with ClastalX using the default paramters


## 4 Do multiple sequence alignment with ClastalX using default parameters:
- Paiwise alignment:
Gap opening: 10
Gap Extend: 0.1
- Protein weight matrix: Gonnet 250

## 5 Build the phylogenetic tree with Mega:

Test ddifferent models:

#1 P-distance
complete deletion

Uniform Trates
#2 JTT
Gama
1.0
Partial deletion

#3ML
›

#pick the other three gene models and see what they look like

### According to John Vogel's comments, we add more Arabidopsis genes

We did blast using the AT4G15230.1 and find the first four sequences with highest Bits score and lowest e value. Adding these four sequences and additional three gene models, we did the sequence alignment again and build the tree again with both ML and NJ approaches.

The ML tree parameters are:


The NJ tree parameters are:

### John suggested to just keep single gene modle for each gene and also get rid of the reference genes.
Then I rerun the clustalx and rebuild the NJ tree with 100 bootstrape.

Here is the paramters for the NJ trees:

```
Scope                         :  All Selected Taxa
    Statistical Method            :  Neighbor-joining
Phylogeny Test
    Test of Phylogeny             :  Bootstrap method
    No. of Bootstrap Replications :  1000
Substitution Model
    Substitutions Type            :  Amino acid
    Model/Method                  :  Jones-Taylor-Thornton (JTT) model
Rates and Patterns
    Rates among Sites             :  Gamma Distributed (G)
    Gamma Parameter               :  1.00
    Pattern among Lineages        :  Same (Homogeneous)
Data Subset to Use
    Gaps/Missing Data Treatment   :  Pairwise deletion
System Resource Usage
    Number of Threads             :  8

```
# Conclusion
The most promising gene is:

Bradi4g45397 1 nonsense mutation beginning, 11 MISSENSE

But for this genes, there are several Arabidopsis genes clustered with single Brachypodium genes, so I am not exactly sure if this gene works. Maybe their real orthologs get lost, and it is hard to say.


### After carefully check the mutants in the [Phytozome](https://phytozome.jgi.doe.gov/jbrowse/index.html?data=genomes%2FBdistachyonBd21_3_er%2F&loc=Bd4%3A48682891..48700870&tracks=Mutant_Sites_Impactful%2CMutant_Sites%2CDNA%2CUserBlastResults%2CGaps%2CBlastx_protein%2CBlatx_BasalEmbryophyte%2CBlatx_Dicots%2CRNAExpression%2CBlatx_Monocots%2CAlt_Transcripts%2CTranscripts%2CPASA_assembly%2CRepeatMasker%2CT-DNAInsertionSites%2CPASA_align_EST&highlight=), I found that there are 44 mutants with mutations in this gene. But only one have nonsense mutation (NaN2154_Bd4_48691883_Het), 11 have nonsynnomous mutations. John suggested we can give the seeds with the nonsense mutations. I attached the info of the mutant in the directory.


