# A critical assessment of gene catalogs for metagenomic analysis

- representative: single sequence that represents a cluste (the longest/first found)

## Introduction

- easier to identify genes than the corresponding species
- abundance of genes to group by oprganism

### CD Hit

- greedy  clustering
- deal with larger datasets from early 2000s
- fixed thresholds, already known as bad
  - clusters of multiple species or species in multiple clusters

### Catalog creation

1. identify genes within metagenomic dataset
2. clustering based on similarity
   - get rid of trivial differences
   - either DNA or Protein seq

- Uses:
  - reduce redundancy  to improve diversity estimates in samples
  - frame of reference for diff samples/studies
  - basis for GWAS
  - binning of contigs by organism

### How clustering works

- DNA or protein level
  - DNA: greater Resolution taxonomic classification
  - amino acid: functional analysis, grouping by function
- Goal: group sequences with same function/taxonomic origin
- structure:
  - each cluster: 1 representative Sequence (selected seq or formed consensus)
  - other sequences 95% (ideally) sequence identiy to rep
- uses: database search, prediction of function/structure, abundance estimation of clustered genes

### how IGC is built

- Multistep Clustering process
- 4 intial / separate cluster catalogs
  - AGC (american)
  - CGC (chinese)
  - EGC (European)
  - SPGC (NCBI/EMBL sequences from isolated genomes)
- 1. Merge: AGC + CGC + EGC = 3GC
- 2. Merge: 3GC + SPGC = IGC (international)
- Problem: no change of threshold
  - each merge only consider representative
  - resulting clusters held sequences below 95%
- gene sequences processed in decreasing order of length
  - longest == representative of its cluster
- clustering
  - if >=95% sequence identity over >=90% length of query
  - else: new cluster
- genus-level annotation: 16.3%
- function annotations: 60.4%

## Results

### (Artifact) transitive clustering error

- end result: cluster with less than 95% identity
  - sequences are less than 95% identical
- due to multiple clustering rounds
  - each individual step: above 95%
  - ignores sequences from prior steps (just representative has to be 95% then?)
- Outcome: unintended increase in effective radius of the new cluster in regards to representative
  - gets worse with every clustering iteration (eg from AGC to 3CGC to IGC)
  - two sequences in a cluster could potentially share only 70% identity and only 85% to the representative (IGC).
  - Actual findings: sequences that differ by 50% to representative of their cluster
  - Fig. 3.C most clusters below 95% identity
- More info in supplementary file
- Clusters can contain more than one species (species gene's spread across clusters)
- Terminology:
  - "Radius": Max distance between sequence and representative
  - "Diameter": Max distance between two sequences (non-representative)
- means that two sequences in one original cluster can be 10% different
- merge means that ONLY Representatives are compared
  - if two reps are 5% different, the most distant sequences of their clusters can be 20% apart
  - Above for clusters from diff. catalogs, would not exist separately in the same catalog

### Hidden Species

- Consequence of multi-species clusters
- if species has no gene as a representative: species undetectable
- SPGC: low rate of assignment for E.coli (83%)
  - Assignment rate: reads assigned to correct species taxonomically
- Misslabelling of sequences a serious issue
- Worst case: no overlap between 2 cluster-seqs at all
  - happens when repr. of cluster changes (merge)
  - cluster-genes a & b might overlap, but b itself does not with repr. c
  - supp. file 3: figure 1

### Mapping algorithms

> *Concordantly mapped reads*:
> forward & reverse map to gene with correct insert size and orientation

- **Simulated data**:
  - reads are only compared to representative sequence of a cluster
  - leads to multiple clusters with equally good mapping
    - clusters can have different species
    - Proposed: requirement of abundance estimation algos (?)
  - Outcome: ~20% of reads mapped to wrong cluster on genus level
- **Real Data**:
  - IGC ist not representing whole diversity of human gut
  - many false positives among mapped reads (Fig. 4)

## Discussion

### Error Sources

- fixed identity threshold for clustering (caused by CD-HIT)
  - results in variable levels of taxonomic relatedness
  - no conistent taxonomic level capture wth just 1 threshold
  - each species: diff proportion of genes not represented
    - leads to bias (downstream analysis)
- Sequences of diff lengths clustered together
  - little seq similarity (with rep.) hints at diff function
- iterative clustering
  - amplifies diffs between seqs in same cluster
  - makes all other issues worse
  - done to save computational cost and integrate old catalogs into new ones
  - better algorithms needed

### conclusion

- IGC analysis: high false positive rate
- analytical artifacts outweigh benefits
  - benefit: common frame of reference
- current catalogs should only be used on samples that contained in them

### Improvements

- create catalogs directly from from samples or closely related ones
- better ways to update catalogs
  - no iterative integration
- dynamic tuning of parameters (thresholds)
- only same-length clusters
- include genomic data unlikely to be propertly assembled (???)
- find ways to deal with non-specific (multi-)mapping
  - use and adapt approaches from RNA-seq

