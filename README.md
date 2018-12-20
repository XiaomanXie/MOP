# MOP
MOP v1: Motif-based Occupancy Prediction

Saurabh Sinha’s Lab <sinhas@illinois.edu>

Initially created by Xiaoman Xie

Last modified on Dec. 20, 2018

## Description
There are usually multiple TFs available in the literature or databases for one same TF. It is not clear which one of them, if any, is the optimal motif to use for the modeling of binding strengths. We therefore developed a TF binding predictor MOP, motif-based occupancy prediction, which makes use of ChIP-seq data and all available motifs of a TF to predict its binding strength on a pre-defined window. For a given TF, separate STAP models are trained for each provided motif of this TF, and then used a support vector machine (SVM) classifier to combine the binding strength predictions of a TF at a given genomic window, made by those STAP models, into a single score.
 

## Installation
### 1.	STAP
In STAP folder, do 
```
make
```
This version of STAP is compiled using gcc/4.8.2. Compiling error may occur with higher version of gcc. More information about STAP installation can be found [here](https://github.com/UIUCSinhaLab/STAP)

### 2.	MOP
MOP required R package ‘e1071’. To install ‘e1071’, enter the R environment:
```
R
```
and use the standard R package installation method:
```
Install.packages("R")
```

## Tutorial
This section of the README is to show users how to run MOP with the sample data. In this example, we will trained a MOP model for ATF2 using five ATF2 motifs, then predict the binding strength of ATF2 on 1000 windows.

### Training ChIP-seq data
ChIP-seq data is used to train STAP models. The ChIP-seq data for training should be stored in ./train-chip/[TF]-train-chip.bed with the sequence id as the first column and the corresponding ChIP score as the second column. For example, the ./train-chip/sample-train-chip.bed contains:
```
chr20:7839917-7840417	0.364259
chr10:112033194-112033694	0.445205
chr12:113655050-113655550	0.566625
```

### Training sequences
Sequences of training data should be stored in ./train-seq/[TF]-seq.fa . The order should be the same as ./train-chip/[TF]-train-chip.bed . For example, the ./train-seq/sample-train-seq.fa contains:
```
>chr20:7839917-7840417
TTCCTCTTAATCAAAGGCCTTTATCTCTTGACAAAGTTTCCAGTGAAAAAGTGTACCTAGTTTAAGTAGCCAACTCATGCCTGGGGTTTAGCTTCTCAAAGAGGGAGGGGTGTGAACATCGTTGTTCCATTAACAATTTAAGGATCAGCTCTCAAAGTGTAGTCATAAGGCGTAGAATTAACAGATGACATAATGTGATATTTAAAAGTTTTAAGAAACCAGCAAATACATGTCTTGCAGCACAGCACCATCTACTGACCAGACAGAGAAAGAATATGGGGGTGGGGGAAGAAGGTAGAGAGAAAGGATGAGAAGGGCTGAGAAGGCTGGGGGGAAGaggaaagtgaaaagaggggaagaggaaagtgaaaagaggggaagaggaaagtgaaaagaggggaagaggaaagtgaaaagaggggaagaggaaagtgaaaagaagggaagaggaaaagaaagaaaaAACAggacttcagatttccgttaatgcaattccacatt
>chr10:112033194-112033694
AATATGCTTTTGATATATCAGGATCCATTTTTAATGTCATTTTTCATTAGGAAAACAGGCAGGCAGGGTTTTTTGGGGGGATAAATTTGGGTTTTCAGAAGATTCTCAACACATTAAGTTTTAGAGACACTTTGGTATATATGTGTAGGAAAAGTAGCTAGCTGCTGTCACGTGCAGGAGTGTGTGTTGGGAGTTGAGAAACCTGGCCACAAGCCACTGCAGCTTTGAAGAATGTGGCTCATGAGAGGACTCATAGTTCATTACGTAACATTCAGCAGAGCGGAAAATATAAGCACTGTCTCCATCACTTGACTTGCTTTCCATTTTGATTTAGTTATCTGGTAAAGTCTAGGTATCCTATAGTGGAAGAATGAAAGACAAGCAGGTTCCAATGAAGGACCATGCTTCCAAAGGCATCAGCCAGTGGATCGTTTGTGGGGCCAGGCGGGGGTTAGATCTATTATTGTTGGACAGGGGTATTAGAGTTTTGAAAGACTACTT
>chr12:113655050-113655550
CCTCAGACTGGAACAGACAGCACTTTACAACCTGTGGAAACTCTCTCACCTTTCCTGCATAGTACTAATCACACGTGTAATTTTTTTttttttacagctctattgaggtataatttacattaccataaaattcacccattgtaagtgtaaaactcagtgactatttttgtaaatgtatctgaagttgccacatcactacaatccagttttagaacatttctatcactccccccaaattcccatgagctaatctgcaaacaatctctgcttccactcacattttttgttgtggttgttttgtttttgaaactgagtctcgctctgtcgcccaggctggagtgcagtggcgtaatctcggctcactgcaacctccacccccctggttcaagcaattctcgtgcctcagcctcccaagtagctgggattacaggtgcgtgttaccacgcttggctaatttttacgtttttaggagagacagggtttcagcatgttgcccaggct

```

### TF binding motifs
TF motifs should be saved in ./motifs/ in wtmx format. For example:
```
>ATF2	11	0.001
0.409091	0.163636	0.318182	0.109091 
0.000000	0.063636	0.100000	0.836364 
0.000000	0.054545	0.281818	0.663636 
0.163636	0.000000	0.745455	0.090909 
0.000000	1.000000	0.000000	0.000000 
0.290909	0.054545	0.654545	0.000000 
0.000000	0.009091	0.000000	0.990909 
0.018182	0.981818	0.000000	0.000000 
1.000000	0.000000	0.000000	0.000000 
0.000000	0.363636	0.109091	0.527273 
0.100000	0.581818	0.090909	0.227273 
<
```

### Motif list
Motifs of a same TF should be listed in one file and stored in ./motif-list/[TF]-motif-list.txt. For example, ./motif-list/sample-motif-list.txt contains:
```
sample_motif_1.wtmx
sample_motif_2.wtmx
sample_motif_3.wtmx
sample_motif_4.wtmx
sample_motif_5.wtmx
```

### Test data
Test data should be stored in ./test-data/[TEST]-data.bed as the sample format as training ChIP-seq data. Since the ChIP scores for test sequences are usually unknown, 0s can be put in the second column. For example, ./test-data/test-data.bed contains:
```
rs12082473	0
rs3094315	0
rs3131972	0
```

### Test sequences
The sequences of test data should be stored in ./test-seq/[TEST]-seq.fa . The order should be the same as  ./test-data/[TEST]-data.bed . For example, the ./test-seq/test-seq.fa contains:
```
>rs12082473
AATCTTTGGACAAAAGCAAACAATAAATGGAATATATGGCTAAGATCCTTTCTTTTTGAGTGTGGCCCAAGATAAAAATTTCTTCCAAAAGGGTAACATTAATGCAATAAGGCTTTTGTGGAATTCTATTTTGTACGAAATTCCTGTTTTCTATTAGCTACTTCTTCCTCTGTGACACAAGCCTCATTCCTTGTGAAAAACACGAAGCAGAAAACATTCGTGAAATCATCAGGTAAAGATCCCATGACCGTTCTATCACTGACACAGTATGTAGGTACATGGTACAACCCCCAAGGGACCACCTTGTGGCATCCTCAGCAACCACAGAGCCACAGGGCAGATGAGTGTTTGCTCCCACTGAGGCTCTGCAACCTCTGCCACCAATACTGGAGAAATCCTCCAACCTCACTGTTTTCTCCTGAACTTGTTCCCTTCTGTTTTTCGATTAATACACAGCAAACAGGCACACCTTCAAGCTGCCAAAAGCATCTTAAACGTTG
>rs3094315
agccacatacagaagactgaagccagacccctacctttcaccatatacaaaaattaactcaaaatatattaaagatttcaatgtaagacctcaaactgtaagatcctggaagataacctaggaaatactcttcttgacgttggccttggcaaagaatttttggctaagttcccaaaaacgattgcaacaaaacaaaattggcaagtagaatttaactaaatgaaagagcttctgcacagcaagagaaacAtttgacagagaatacagacaacctactgaatgagagaaactatttgcaaactatgcatctgacaaaggcctaatatccagaatctacagggaacttatgcaaAAAACGTTCACTTTCTGTCTGTGTTCACGTCACCAAGAGAATAGAAAGGAAAGGGGAAGAATGCAAAAGTCAAAGACACGTCACCCTCCTTGAGACAGCCCTCCAGTCCAGGCCAAATCTCAGCCTGCCCTTGGTCCGCTGTGGTTGG
>rs3131972
atttttggctaagttcccaaaaacgattgcaacaaaacaaaattggcaagtagaatttaactaaatgaaagagcttctgcacagcaagagaaacAtttgacagagaatacagacaacctactgaatgagagaaactatttgcaaactatgcatctgacaaaggcctaatatccagaatctacagggaacttatgcaaAAAACGTTCACTTTCTGTCTGTGTTCACGTCACCAAGAGAATAGAAAGGAAAGGGGAAGAATGCAAAAGTCAAAGACACGTCACCCTCCTTGAGACAGCCCTCCAGTCCAGGCCAAATCTCAGCCTGCCCTTGGTCCGCTGTGGTTGGGCCTGCACCCAAGCCATGAGCACACGCAGCAATTGTGGCAGCAGAAGCTTCCTCTGGGCTCAGACTCAGGCTGATGCTGCGTCAGGACCTGCCGCGGTCTCGGCTGGGCTTCCTGGGACTCGGTGGTTGTGGGCTGATTGTAAAGCACGGAATGA
```

##### Running the program
```
./run-MOP.txt
```


