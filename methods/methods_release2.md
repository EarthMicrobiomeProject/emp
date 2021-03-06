## methods – 16S Release 2 and Multi-omics (EMP500)

Computational methods for Release 2 and the EMP Multi-omics project (EMP500) are described here. For laboratory methods, see [`protocols`](https://github.com/biocore/emp/tree/master/protocols).

<!--made with https://luciopaiva.com/markdown-toc/ -->

# Table of contents

  - [0 Metadata](#0-metadata)
  - [1 Amplicon sequencing](#1-amplicon-sequencing)
    - [1.1 16S rRNA gene data](#11-16s-rna-gene-data)
    - [1.2 18S rRNA gene data](#12-18s-rna-gene-data)
    - [1.3 Fungal ITS data](#13-fungal-its-data)
  - [2 Shotgun sequencing](#2-shotgun-sequencing)
    - [2.1 Short-read analysis](#21-short-read-analysis)
      - [2.1.1 Sequence file demultiplexing](#211-sequence-file-demultiplexing)
      - [2.1.2 Adapter trimming and poly-G removal](#212-adapter-trimming-and-poly-g-removal)
      - [2.1.3 Qiita read alignment to the Woltka reference database](#213-qiita-read-alignment-to-woltka-reference-database)
      - [2.1.4 Woltka gOTU feature-table generation](#214-woltka-gotu-feature-table-generation)
    - [2.2 Metagenomic assembly and binning](#22-metagenomic-assembly-and-binning)
      - [2.2.1 Assembly and co-assembly of EMP500 samples within each environment](#221-assembly-and-co-assembly-of-emp500-samples-within-each-environment)
      - [2.2.2 Binning of assemblies to generate MAGs for each environment](#222-binning-of-assemblies-to-generate-mags-for-each-environment)
      - [2.2.3 Functional annotation of assemblies](#223-functional-annotation-of-assemblies)
      - [2.2.4 Taxonomic profiling of MAGs](#224-taxonomic-profiling-of-mags)
  - [3 Metabolomics data analysis](#3-metabolomics-data-analysis)
    - [3.1 Non-targeted mass spectrometry analysis by LC-MS/MS](#31non-targeted-mass-spectrometry-analysis-by-lc-msms)
    - [3.2 Non-targeted mass spectrometry analysis by GC-MS](#32-non-targeted-mass-spectrometry-analysis-by-gc-ms)


### 0 Metadata

The metadata workflow takes individual study metadata files, general sample information, and prep information, then converts this to mapping files, sample information files, and prep information files.

Note: files with the words "manual" or "prepandas" in the title are curated manually and are not output by a notebook or script.

Processing is done by these IPython notebooks:

* `emp500_s1_merge_sample_info.ipynb` Merge individual study metadata Excel files, add general prep information (including plate and well numbers for the initial DNA extraction), generate sample information file and basic mapping file. 
    - Inputs: `emp500_sample_information_manual.xlsx`, `STUDY_XX_METADATA.xlsx` (sample metadata for each individual study)
    - Outputs: `emp500_sample_information.tsv`, `emp500_full_map.tsv`, `emp500_basic_map.tsv`
* `emp500_s2_add_prep_info_sample_names.ipynb` Add prep information (16S, 18S, ITS) and sample information (sample names, study information, EMPO categories, etc.).
    - Inputs: `emp500_sample_information_manual.xlsx`, `emp500_prep_information_general_prepandas.xlsx`, `emp500_prep_information_*_prepandas.xlsx` (prep info files for each of Round 1 runs)
    - Outputs: `emp500_prep_information_general.xlsx`, `emp500_prep_information_*.xlsx` (prep info files for each of Round 1 runs)
* `emp500_s3_make_mapping_files_prep_info.ipynb` Generate mapping files and prep information files.
    - Inputs: `emp500_sample_information.tsv`, `emp500_prep_information_general.xlsx`, `emp500_prep_information_*.xlsx `
    - Outputs: `emp500_*_prep_info_*.tsv`, `emp500_*_mapping_file_*.tsv`
* `emp500_s4_project_summary.ipynb` Generate project summary and list of samples (for labels).
    - Inputs: `emp500_basic_map.tsv`
    - Outputs: `emp500_project_summary.csv`, `emp500_sample_names.csv`
* `emp500_s5_labels.ipynb` Generate label spreadsheet with QR codes (not encoded).
    - Inputs: `emp500_project_summary.csv`, `emp500_per_study_indexes.xlsx`
    - Outputs: `emp500_labels.xlsx`, `emp500_labels_extra.xlsx`, `emp500_gsheet.xlsx`, `emp500_gsheet_extra.xlsx`, `emp500_box_labels.xlsx`
    
  
### 1 Amplicon sequencing

Demultiplexing, feature-table generation, fragment insertion, and taxonomic profiling were performed in Qiita on a per-sequencing lane basis. Final feature-tables were generated using the meta-analysis functionality in Qiita to combine multiple preps per data type.

### 1.1 16S rRNA gene data

#### Demultiplexing:
* Process: Split libraries FASTQ
* Parameters: Multiplexed FASTQ, Golay 12 base pair reverse complement mapping file barcodes with reverse complement barcodes

#### Sequence trimming, denoising, feature-table generation, and fragment insertion:
* Process: Trimming
* Parameters: 150 base pair
* Process: Deblur + SEPP
* Parameters: Default settings (i.e., Fragment insertion into the GreenGenes 13_8 release phylogeny)

#### Taxonomic profiling
* Process: Feature-classifier sklearn 
* Parameters: Using the GreenGenes 13_8 release as a reference

#### OTU clustering
* Process: Closed-reference OTU picking 
* Parameters: 97% sequence similarity threshold, using the GreenGenes 13_8 release as a reference


### 1.2 18S rRNA gene data

#### Demultiplexing:
* Process: Split libraries FASTQ
* Parameters: Multiplexed FASTQ, Golay 12 base pair reverse complement mapping file barcodes

#### Sequence trimming, denoising, feature-table generation, and fragment insertion:
* Process: Trimming
* Parameters: 150 base pair
* Process: Deblur
* Parameters: Default settings

#### Taxonomic profiling
* Process: Feature-classifier sklearn 
* Parameters: Using the SILVA 138.1 release as a reference

#### OTU clustering
* Process: Closed-reference OTU picking 
* Parameters: 97% sequence similarity threshold, using the SILVA 119 release as a reference


### 1.3 Fungal ITS data

#### Demultiplexing:
* Process: Split libraries FASTQ
** Parameters: Multiplexed FASTQ, Golay 12 base pair reverse complement mapping file barcodes

#### Sequence trimming, denoising, feature-table generation, and fragment insertion:
* Process: Trimming
* Parameters: 150 base pair

* Process: Deblur
* Parameters: Default settings

#### Taxonomic profiling
* Process: Feature-classifier sklearn 
* Parameters: Using the UNITE 8 release as a reference

#### OTU clustering
* Process: Closed-reference OTU picking 
* Parameters: 97% sequence similarity threshold, using the UNITE 8 release as a reference


### 2 Shotgun sequencing

Demultiplexing and adapter trimming were performed prior to processing in Qiita. Read alignment to the Web of Life Toolkit Analysis (Woltka) was performed in Qiita on a per-sequencing lane basis. Each read alignment was processed in Woltka to produce a genome-OTU (gOTU) feature-table, allowing for use of the Woltka phylogeny and taxonomy for downstream analyses. The final Woltka gOTU feature-table was generated by merging across preps using QIIME2. In parallel, metagenomic assemblies and metagenome assembled genomes (MAGs) were constructed for each of 35 major environments represented among the samples.

#### 2.1 Short-read analysis

#### 2.1.1 Sequence file demultiplexing

Shotgun sequence files from Illumina were demultiplexed using bcl2fastq and custom sample sheets, with demultiplexed files placed in directories designated for each PI–study combination.

#### 2.1.2 Adapter trimming and poly-G removal

Adapter trimming and poly-G removal were performed on per-sample FASTQ files using Atropos 1.1.25:

`atropos \
   -a GATCGGAAGAGCACACGTCTGAACTCCAGTCAC \
   -A AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT \
   -pe1 forward.fastq.gz \
   -pe2 reverse.fastq.gz \
   -o forward_trimmed.fastq.gz \
   -p reverse_trimmed.fastq.gz \
   --nextseq-trim 30 \
   --match-read-wildcards \
   -e 0.1 \
   -q 15 \
   --insert-match-error-rate 0.2 \
   --minimum-length 100 \
   --pair-filter any \
   --report-file report.txt \
   --report-formats txt \
   -T 16`

#### 2.1.3 Qiita read alignment to the Woltka reference database

* Process: Shogun v1.0.7
* Parameters: bowtie2 as aligner tool, Web of Life (WoL) as reference database

#### 2.1.4 Woltka gOTU feature-table generation

`woltka classify \
  --input bowtie2_wol_alignment.sam.xz \
  --uniq \
  --demux \
  --output woltka_gotu_table.biom`
  
`qiime tools import \
  --type FeatureTable[Frequency] \
  --input-path woltka_gotu_table.biom \
  --output-path woltka_gotu_table.qza`
  
`qiime feature-table merge \
  --i-tables woltka_gotu_table.qza \
  --i-tables woltka_gotu_table_2.qza \
  --p-overlap-method 'sum' \
  --o-merged-table woltka_gotu_table_merged.qza`

#### 2.2 Metagenomic assembly and binning

#### 2.2.1 Assembly and co-assembly of EMP500 samples within each environment

* To determine which of the EMP500 environments could be co-assembled, MASH, atool employing the MinHash dimensionality reduction technique [(Ondov, Treangen et al. 2016)](https://doi.org/10.1186/s13059-016-0997-x) was used to evaluate the pairwise distances between the metagenomic data sets. 
* Sequence‭-based grouping ‬was done by ‭Markov Clustering (MCL) [(Van Dongen and Abreu-Goodger 2012)](https://doi.org/10.1007/978-1-61779-361-5_15) of Mash. 
* A combination of MASH distances and a Markov cluster algorithm which identifies orthology groups (OGs) in reciprocal best matches (RBM), was employed to evaluate the samples that could be grouped for co-assembly. 
* Mash sketches were initially created using a sketch size of 10,000 and k-mer size of 32 and the sketches were combined using the “paste” option. 
* A pairwise distance matrix was constructed using the “dist” option and the distance matrix was used in downstream analyses. 
* Samples which had distance values below 0.1 were then co-assembled [(Karthikeyan, Rodriguez-R et al. 2020)](https://doi.org/10.1111/1462-2920.14966). ‬‬
* Co-assembly was carried out using metaSPades v3.15.0 with the “--only-assembler” mode and the following k-mers: 21,33,55,77,99,127. Quality of the assemblies were evaluated using MetaQUAST [(Mikheenko, Saveliev et al. 2016)](https://doi.org/10.1093/bioinformatics/btv697).  

#### 2.2.2 Binning of assemblies to generate MAGs for each environment

* Binning was carried out using MaxBin v2.2.7 [(Wu, Simmons et al. 2016)](https://doi.org/10.1093/bioinformatics/btv638) and MetaBAT v2.12.1 [(Kang, Froula et al. 2015)](https://doi.org/10.7717/peerj.1165). 
* For each co-assembly both the tools were employed for binning and the resulting bins were hen de-replicated at 95% gANI (genome-aggregate average nucleotide identity) to remove redundancy using FastANI v1.1 [(Jain, Rodriguez-R et al. 2018)](https://doi.org/10.1038/s41467-018-07641-9). 
* Only scaffolds larger than 1000bp were used for MAG generation. Completeness and contamination were estimated using CheckM (Parks, Imelfort et al. 2015). MAG (Metagenome-assembled genome) quality was determined as [Completeness – 5 * (Contamination)]. 
* All MAGs with quality score > 50 were used in downstream analyses. High‐quality MAGs were defined as Completeness > 75% and Contamination < 5%, and medium‐quality MAGs were defined by Completeness > 50% and Contamination < 10%. 
* MAG refining was carried out using RefineM v0.0.23 [(Parks, Rinke et al. 2017)](https://doi.org/10.1038/s41564-017-0012-7). Divergent taxonomic assignments from the MAG scaffolds were identified using the “call_genes” option and then searching them against the reference database available at https://data.ace.uq.edu.au/public/misc_downloads/refinem/ using the “taxon_profile” option. Potentially contaminating scaffolds were then removed from the bins using the “filter_bins” option. 

#### 2.2.3 Functional annotation of assemblies

* protein-coding gene predictions were performed using Prodigal v2.6.3
* annotation of gene predictions was performed using Diamond v2.0.5
* carbohydrate-active-enzyme (CAZy) prediction was performed using dbCAN2 v2.0.11
* secondary metabolite gene cluster prediction was performed using antiSMASH v5

#### 2.2.4 Taxonomic profiling of MAGs

* Taxonomic classification of MAGs was performed using GTDB-Tk v1.3.0 release95 [(Chaumeil, Mussig et al. 2020)](https://academic.oup.com/bioinformatics/article/36/6/1925/5626182) using the “gtdbtk classify_wf” option.



### 3 Metabolomics data analysis

**Table of contents**

* [Non-targeted LC-MS/MS](#Non-targeted-mass-spectrometry-analysis-by-lc-msms)
* [Examples from real-life studies](#examples-from-real-life-studies)
* [Installation](#installation)
* [Input](#input)
* [How to use it?](#how-to-use-it)
* [Demo](#demo)

#### 3.1. Non-targeted mass spectrometry analysis by LC-MS/MS

**IMPORTANT**: The processing and annotations below were performed to study the entire EMP dataset at the dataset scale. Additionally, the feature table were not subject to normalization or "blank substraction". 
These results could be used to investigate a specific study that is part of the EMP, but this is not recommended. Instead, this processing would have to be performed and optimized for each study. Contact Louis Felix Nothias [(nothias@health.ucsd.edu)](nothias@health.ucsd.edu) for more informations. 

The data were processed and annotated by Louis Felix Nothias [(nothias@health.ucsd.edu)](nothias@health.ucsd.edu) from the [Dorrestein Lab at University of California San Diego](https://dorresteinlab.ucsd.edu/).

##### Data conversion, preparation and desposition
The mass spectrometry data were centroided and converted from the proprietary format (.raw) to the m/z extensible markup language format (.mzML) using [ProteoWizard](http://proteowizard.sourceforge.net/download.html) (ver. 3.0.19, MSConvert tool). Citation: [(Chambers et al. _Nature Biotech._, 2012)](https://www.nature.com/articles/nbt.2377).
 
The data were visualized and inspected with the [OpenMS TOPPAS tool](https://github.com/OpenMS/OpenMS) (ver 2.4.0). Citation: [Rost et al. Nat. Methods, 2016](https://www.nature.com/articles/nmeth.3959)

The mass spectrometry method and data (.RAW and .mzML) were deposited on the MassIVE public repository and are available under the dataset accession number MSV000083475 at this page: [https://massive.ucsd.edu/ProteoSAFe/dataset.jsp?task=3de2b5de5c274ca6b689977d08d84195](https://massive.ucsd.edu/ProteoSAFe/dataset.jsp?task=3de2b5de5c274ca6b689977d08d84195).

- The .RAW files are accessible via FTP here: [ftp://massive.ucsd.edu/MSV000083475/peak/mzML/](ftp://massive.ucsd.edu/MSV000083475/peak/mzML/).

- The .mzML files are accessible via FTP here: [ftp://massive.ucsd.edu/MSV000083475/raw/RAW/](ftp://massive.ucsd.edu/MSV000083475/raw/RAW/).


##### Processing: LC-MS/MS feature detection and alignement with MZmine
The mzML files were then processed with a custom build of MZmine toolbox (*vers.2.37corr17.7kaimerge2* at [https://github.com/robinschmid/mzmine2/releases](https://github.com/robinschmid/mzmine2/releases)) that includes advanced modules for adduct/isotopologue annotations. Citations [Pluskal et al., _BMC Bioinf._ 2010](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-11-395) and [Schmid, Petras, Nothias et al. bioRxiv, 2020, 2020.05.11.088948](https://www.biorxiv.org/content/10.1101/2020.05.11.088948v1).

The MZmine processing was performed on Ubuntu 18.04 LTS 64-bits workstation (Intel Xeon 5E-2637, 3.5 GHz, 8 cores, 64 Go of RAM) and took ~3 days. 

The MZmine project, the MZmine batch file (.XML format), and results files (.MGF and .CSV) are available in the MassIVE dataset ([MSV000083475]((https://massive.ucsd.edu/ProteoSAFe/dataset.jsp?task=3de2b5de5c274ca6b689977d08d84195))). The MZmine batch file contains all the parameters used during the processing. In brief, feature detection and deconvolution was performed with the ADAP chromatogram builder, and local minimum search algorithm. The isotopologues were regrouped, and the features (peaks) were aligned accross samples. The peaklist was gap filled and only peaks with an associated fragmentation spectrum (MS2) and occuring in a minimum of 3 files were conserved. Peak shape correlation analysis was used to group peaks originating from the same molecule, and used for adduct/isotopologue annotations. Finally the feature table results (.CSV) and spectral information (.MGF) were exported for subsquent analysis on GNPS and with SIRIUS export.

###### Processing files:

These files were deposited on MassIVE (MSV000083475) and available at: [ftp://massive.ucsd.edu/MSV000083475/updates/2019-08-21_lfnothias_7cc0af40/other/1908_EMPv2_INN/](ftp://massive.ucsd.edu/MSV000083475/updates/2019-08-21_lfnothias_7cc0af40/other/1908_EMPv2_INN/)
    
- `1907_Benchmarking_dataset_EMP_batch_v9_MinimumLocal_QE_v6_INN_v4.xml` : this is the MZmine batch file that was used for the procesing. It does not contains the Export step (GNPS and SIRIUS export that were employed with the default parameters).

- `1907_EMPv2_v3.mzmine`: the MZmine project that contains the results. It can be open with a powerful desktop computer (see the spec) to visualize aligned chromatograms etc.
	
###### Result files: 

- `1907_EMPv2_INN_GNPS_quant.csv`: the feature table generated by MZmine that contains the ion intensity (LC-MS peak area) accross the EMP samples. This feature table is the unprocessed table (no normalization or other post processing was applied to it). The ion intensity is the standard proxy to estimate the relative concentration of a metabolite accross the samples. However the intensities of different metabolites (ions) cannot be compared due to different ionisation efficiency.
  
- `1907_EMPv2_INN_GNPS.mgf`: the MS2 spectral summary file generated by MZmine that contains the most intense MS2 spectra associated with an ions detected. It is used for GNPS analysis (molecular networking, spectral annotation, etc).
   
- `1907_EMPv2_INN_GNPS_edges_msannotation.csv`: the edges annotations from ion identity networking in MZmine that contains informations on related group of ions (peak shape correlation analysis).
   
- `1907_EMPv2_SIRIUS.mgf`: the MS2 spectral summary file generated by MZmine that contains the MS1 (isotopic pattern, adducts) MS2 spectra used for SIRIUS annotation (molecular formula prediction, and structure/class annotation).


##### Annotation with Global Natural Products Social Molecular Networking (GNPS)

The results files of MZmine (.MGF and .CSV files) were uploaded to GNPS [(http://gnps.ucsd.edu)](http://gnps.ucsd.edu) and analyzed with the Feature-Based Molecular Networking workflow [(Nothias, Petras, Schmid et al bioRxiv 2019)](https://www.biorxiv.org/content/10.1101/812404v1). The metadata were also inputed in the job. Citation: [(Wang et al., Nat. Biotech. 2016)](https://www.nature.com/articles/nbt.3597).

Spectral library matching was performed against public MS/MS spectral library and the NIST17 library to obtain putative level 2 annotation (putative structure) based on [MSI standards](https://pubs.acs.org/doi/abs/10.1021/es5002105).

- The GNPS molecular networking job, paramaters and results can be consulted at the following address [no metadata are included]: [https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=3381f4f48f2e4cbeb0cd364b4b71a844](https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=3381f4f48f2e4cbeb0cd364b4b71a844). 

- The GNPS molecular networking job was also performed in analogue mode to obtain level 3 MSI annotations (partial/class annotation) [to be updated]: [https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=cd321f79ae284ef6bb183cb45122632c](https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=cd321f79ae284ef6bb183cb45122632c).

###### Putative annotation of small peptides with the DEREPLICATOR 

For the putative annotation of small peptides. These annotations can be classified as level 2/3 annotation (putative/partial structure) based on [MSI standards](https://pubs.acs.org/doi/abs/10.1021/es5002105)). The DEREPLICATOR algorithm was used on GNPS. While DEREPLICATOR+ annotates known structures, the DEREPLICATOR VarQuest can search analogues of known molecules and that are differing by one amino acid residue. Citations: 
[(Mohimani et al Nat. Chem. Bio. 2016)](https://www.nature.com/articles/nchembio.2219) and 
[(Mohimani et al. Nat. Com. 2018)]
(https://www.nature.com/articles/s41467-018-06082-8?_ga=2.258351242.188697708.1538611200-1366481109.1538611200).

- The DEREPLICATOR+ job can be accessed here: [https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=0a392ef93919475c9fbfc1534234d0a2](https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=0a392ef93919475c9fbfc1534234d0a2) 

- The DEREPLICATOR VarQuest job can be accessed here: [https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=c09ee787c3b448bbb1ddd081bd2193cd](https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=c09ee787c3b448bbb1ddd081bd2193cd). 

###### Putative annotation of small cyclopeptides with the CycloNovo

CycloNovo performs de novo cyclopeptide sequencing using employs de Bruijn graphs [See preprint](https://www.biorxiv.org/content/10.1101/521872v2). Many of these cyclopeptides are bioactive molecules produced by microbes.

- The CycloNovo job results can be accessed here: [https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=41267a9438f64548a914d212dfaa4ead](https://gnps.ucsd.edu/ProteoSAFe/status.jsp?task=41267a9438f64548a914d212dfaa4ead)


###### Putative annotation of small molecules with SIRIUS

Additional spectral annotation of tandem mass spectrometry data were obtained with the SIRIUS [(Durkhop et al., Nat. Methods, 2019)](https://www.nature.com/articles/s41592-019-0344-8) computational annotation tool (vers. 4.4.25, headless, linux) running on a cluster computer (32 cores, 256 Gb of RAM).

- De novo molecular formulas were computed with the SIRIUS module by matching the experimental and predicted isotopic patterns [(Böcker, Bioinformatics 25, 218–224, 2009](https://academic.oup.com/bioinformatics/article/25/2/218/218950), and from fragmentation trees analysis of the fragment ions [(Böcker and Dührkop, J. Cheminform. 8, 5, 2016](https://jcheminf.biomedcentral.com/articles/10.1186/s13321-016-0116-8).
- Molecular formula prediction was refined with the ZODIAC module by using Gibbs sampling [(Ludwig et al, bioRxiv, 842740, 2019)](https://www.biorxiv.org/content/10.1101/842740v1) for fragmentation spectra that were not chimeric spectra or had a poor fragmentation.
- Structure annotation with structure database was done with the CSI:FingerID  module [(Durkhop et al., PNAS 2015)](https://www.pnas.org/content/112/41/12580).
- Database-independent class annotations were obtained with the CANOPUS module [(Dührkop, et al.bioRxiv, 2020)](https://www.biorxiv.org/content/10.1101/2020.04.17.046672v1).  

Parameters were set as follows, for SIRIUS: molecular formula candidates retaine (80), molecular formula database (ALL), maximum precursor ion m/z computed (750), profile (orbitrap), m/z maximum deviation (12 ppm), ions annotated with MZmine were prioritized and other ions were considered ([M+H3N+H]+, [M+H]+, [M+K]+,[M+Na]+, [M+H-H2O]+, [M+H-H4O2]+, [M+NH4]+); for ZODIAC: the features were splitted into 10 random subsets and were computed separately with the following parameters:   treshold filter (0.95), minimum local connections (0); for CSI:FingerID: m/z maximum deviation (10 ppm) and biological database (BIO).

The resulting results are available at [../data/metabolomics/sirius/release_202007](../data/metabolomics/sirius/release_202007). The processing was performed on a linux cluster computer (32 cpu with 256 GB of RAM).

**Citations**:

- K. Dührkop, et al., SIRIUS 4: a rapid tool for turning tandem mass spectra into metabolite structure information. [Nat. Methods 16, 299–302 (2019)] (https://www.nature.com/articles/s41592-019-0344-8).
- S. Böcker, M. C. Letzel, Z. Lipták, A. Pervukhin, SIRIUS: decomposing isotope patterns for metabolite identification. [Bioinformatics 25, 218–224 (2009)](https://academic.oup.com/bioinformatics/article/25/2/218/218950)
- S. Böcker, K. Dührkop, [Fragmentation trees reloaded. J. Cheminform. 8, 5 (2016)](https://jcheminf.biomedcentral.com/articles/10.1186/s13321-016-0116-8).
- M. Ludwig, et al., ZODIAC: database-independent molecular formula annotation using Gibbs sampling reveals unknown small molecules. [bioRxiv, 842740, (2019)](https://www.biorxiv.org/content/10.1101/842740v1).
- K. Dührkop, H. Shen, M. Meusel, J. Rousu, S. Böcker, Searching molecular structure databases with tandem mass spectra using CSI:FingerID. [Proc. Natl. Acad. Sci. U. S. A. 112, 12580–12585 (2015)](https://www.pnas.org/content/112/41/12580).
- K. Dührkop, et al., Classes for the masses: Systematic classification of unknowns using fragmentation spectra. [bioRxiv, 2020.04.17.046672 (2020)](https://www.biorxiv.org/content/10.1101/2020.04.17.046672v1). 

#### 3.2 Non-targeted mass spectrometry analysis by GC-MS

Untargeted analyses of polar metabolites was performed by GC-MS (electronic ionisation source) The data were collected by Sneha Couvillion (sneha.couvillion@pnnl.gov) from the [Thomas Metz laboratory, Pacific Northwest National Laboratory](https://omics.pnl.gov/staff-page/Metz/Tom).

##### Data conversion, preparation and desposition
The GC-MS data were converted from the proprietary file format (.d format) to the netCDF file format (.cdf format) using [OpenChrom](https://sourceforge.net/projects/openchrom/) (ver. win32.x86_64_1.0.0_rel). Citation: [(Wenig et al. _OpenChrom._, 2010)](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-11-405). For a detail description of the conversion, please refer to this [document pages 1-4](document/Metabolite Detector_GC-MS_PNNL_tutorial.pdf)

- The files were deposited on MassIVE under the following accession number ([MSV000083743](https://massive.ucsd.edu/ProteoSAFe/dataset.jsp?task=948bad7011a544eab485d2114cac22f0)). Every batch of samples that are run on the GC-MS have accompanying blanks and a FAMES file. 


##### PNNL GC-MS pipeline

The data were processed and annotated by Sneha Couvillion (sneha.couvillion@pnnl.gov) from the [Thomas Metz laboratory, Pacific Northwest National Laboratory](https://omics.pnl.gov/staff-page/Metz/Tom).

###### Data Processing
The GC-MS data files (.netCDF format) were processed using MetaboliteDetector ([Hiller et al., Anal. Chem. 2009](https://pubs.acs.org/doi/10.1021/ac802689c)) in order to detect, align and measure the metabolites intensities accross samples. For a detail description of the processing, see this [document pages 4-8](document/Metabolite Detector_GC-MS_PNNL_tutorial.pdf)


###### Annotation
Retention indices (RI) of detected metabolites were calculated based on the analysis of the FAME standard mixture, followed by their chromatographic alignment across all analyses after deconvolution. Metabolites were then identified by matching GC-MS features (characterized by measured retention indices and mass spectra) to an augmented version of the Agilent Fiehn Metabolomics Retention Time Locked (RTL) Library ([Kind et al., Anal. Chem. 2009](https://pubs.acs.org/doi/10.1021/ac9019522)), which contains spectra and validated retention indices for over 700 metabolites. All metabolite identifications were manually validated to reduce deconvolution errors during automated data-processing and to eliminate false identifications. The NIST 08 GC-MS library was also used to cross-validate the spectral matching scores obtained using the Agilent library.

###### Result files

- `EMP_GCMSmetabolitedata_blocksABCD_080919.xlsx`: This excel file (.xlsx) contains the metabolites intensities and identification that were manually validated. The file can be accessed on the MassIVE at [ftp://massive.ucsd.edu/MSV000083743/updates/2019-08-22_lfnothias_7cc043bc/other/](ftp://massive.ucsd.edu/MSV000083743/updates/2019-08-22_lfnothias_7cc043bc/other/)

QC files for each samples accessible at [ftp://massive.ucsd.edu/MSV000083743/other/](ftp://massive.ucsd.edu/MSV000083743/other/):

- The `_GC_DatasetInfo.xml`: contains informations on the sample acquisition.
- The `_GC_BPI_MS.png`: contains an image of the sample's base peak chromatogram.
- The `_GC_GC_TIC.png`: contains an image of the sample's total ion chromatogram.
- The `_GC_LCMS.png`: contains an image of the sample's 2D chromatogram.
- The `_GC_HighAbu_LCMS.png`: contains an image of the sample's 2D chromatogram.
