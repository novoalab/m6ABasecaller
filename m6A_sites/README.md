In this folder, you can find 5 BED files, which correspond to the list of m6A sites predicted by m6ABasecaller in different publicly available direct RNA nanopore sequencing datasets. 
* The table below includes a brief description of each of the BED files that can be found in this folder. 
* We only list a site as "m6A-modified" if the site has at least 25 reads of coverage in the given dataset,and a minimum per-site modification frequency of 5%.




| File name (list of predicted m6A sites) | Method                            | Cell line       | Species | Technology | Number of m6A sites | Study that produced the sequencing data | Genome annotation |
|-----------------------------------------|-----------------------------------|-----------------|---------|------------|----------------------|-----------------------------------------|-------------------|
| m6Abasecaller_sites_HEK293T_WT_pooled_25cov_5freq.bed | direct RNA (m6ABasecaller) | HEK293T | Human | Nanopore | 7,922 | Pratanwanich et al. Nature Biotechnology 2021 | hg38 |
| m6Abasecaller_sites_HepG2_WT_pooled_25cov_5freq.bed | direct RNA (m6ABasecaller) | HepG2 | Human | Nanopore | 28,846 | Chen, Ying, et al. bioRxiv 2021 | hg38 |
| m6Abasecaller_sites_mESC_WTMETTL3_25cov_5freq.bed | direct RNA (m6ABasecaller) | mESC | Mouse | Nanopore | 8,511 | Jenjaroenpun P et al. Nucleic Acids Research 2021 | mm10 |
| m6Abasecaller_sites_mESC_WTMETTL14_25cov_5freq.bed | direct RNA (m6ABasecaller) | mESC | Mouse | Nanopore | 4,055 | Jenjaroenpun P et al. Nucleic Acids Research 2021 | mm10 |
| m6Abasecaller_sites_zebrafish_WT_cov25_5freq.bed | direct RNA (m6ABasecaller) | Embryos 4hpf | Zebrafish | Nanopore | 8,771 | Begik et al., Nat Methods 2022 | danRer11 |
