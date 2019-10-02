# Computation framework of the metabolic tasks

We define a metabolic task as the capacity of producing a defined list of output products when only a defined list of input substrates is available. For each available reference genome-scale model, we computed the list of task the model successfully passed (i.e. list of functions the model is able to describe). If a task successfully passes in a model, we determined the set of reactions associated to this task. Therefore, based on the GPR rules, we can enumerate the genes that may contribute to the acquisition of this metabolic function.

We developed a computation framework for attributing a score to each metabolic task based on the availability of transcriptomic data. To this end, we first attribute a gene activity level (GAL) for each gene present in the model and for which expression data have been provided.
**GAL=5\*log(1+ gene expression value / threshold)**
We propose multiple options to define the threshold (see section Threshold Definition for more details).

We further used the GPR rule associated to each reaction required for a task to decide which gene will be the main determinant of the enzyme abundance associated to this reaction and attribute the corresponding gene activity level. Therefore, each reaction involved in a task is associated to a reaction activity level (RAL) that corresponds to the preprocessed gene expression value of the gene selected as the main determinant for this reaction.

We also computed the significance of each gene selected with regard of its overall use in the observed condition. Actually, some genes will be mapped to multiples reactions (e.g. promiscuous enzyme). Therefore, we assume that it may exist some competition between the reactions using this gene. We define the significance of a gene (S) by its specificity for a reaction by the inverse of the number reactions in which this gene is used as main determinant.

Finally, the metabolic score can be compute as the mean of the product of the activity level of each reaction with the significance of its associated gene:
MT score= sum(RAL*S)/number of reactions involved in the task

MT score provides a relative quantification of the activity of a metabolic task in a specific condition based on the availability of data for multiple conditions. Indeed, it has been shown that some important housekeeping genes always present very low expression value. Therefore, a metabolic function that will completely rely on this set of gene will actually always present very low MT score. Contrarily, some tasks can be associated with gene presenting very high expression levels. Therefore, MT scores cannot be compared across tasks but only across samples. To partly overcome this problem, we also propose this scoring approach in its binary version to determine whether a metabolic task is active or not based on a gene expression profile. To this end, the MT score no longer take into account the significance of gene determinant for each reaction but is just computed as the mean of the reaction activity levels. Doing so, a metabolic task will be considered as active if it's MT score in its binary version has a value superior to 5log(2).

# Threshold Definition

We propose two approaches to describe the scheme of threshold imposition on expression value for gene and/or reaction to be considered as “active”.

-  **Global** approach, the threshold value is the same for all the genes. The global approach is mainly applied when only one sample is available (i.e. sample could be associated to a condition, a cell-type or a tissue) and/or no information is available in the literature to define expression threshold for a single gene. The user can define the global threshold using a value or a percentile:
    - value - the user provide the expression value for which a gene is considered as active or not
    - percentile - the threshold is defined based on a percentile computed from the distribution of expression values for all the genes and across all samples of the user dataset
- **Local** approach, the threshold value is different for all the genes. The local approach is often applied when multiple samples are available as it allows having a relative assessment of the activity of a gene across samples. Multiple options are available to compute the local thresholds:
    - **Mean** - the threshold for a gene is defined as the mean expression value of this gene across all the samples, tissues, or conditions (option only available if more than 3 samples are provided by the user).
    - **MinMaxMean** - the threshold for a gene is determined by the mean of expression values observed for that gene among all the samples, tissues, or conditions BUT the threshold :(i) must be higher or equal to a lower bound and (ii) must be lower or equal to an upper bound. The lower an upper bound can be defined using a value introduced by the user or based on a percentile of the distribution of expression value for all the genes and across all samples of your dataset (option only available if more than 3 samples are provided by the user).
    - **Custom** - (UNDER CONSTRUCTION) the user provide the list of threshold value associated to each gene. This option allows user to use the local threshold approach even if their dataset present less than 3 samples.

# List of available genome-scale models

## Human models

- Recon 2.2 - Swainston, N. et al. (2016) Recon 2.2: from reconstruction to model of human metabolism. Metabolomics, 12, 109.
- Recon2 - Thiele, I. et al, (2013) A community-driven global reconstruction of human metabolism. Nature Biotechnology, 31, 419-425.
- Recon 1 - Duarte et al. (2007) Global reconstruction of the human metabolic network based on genomic and bibliomic data. Proceedings of the National Academy of Sciences, 104(6):1777-1782.
- quek14:Quek et al (2014). Reducing Recon 2 for steady-state flux analysis of HEK cell culture. Journal of Biotechnology, 184:172-178
- iHsa : Blais et al. (2017) Reconciled rat and human metabolic networks for comparative toxigenomics and biomarker predictions. Nature Communications, 8:14250

## Rat models

- iRno : Blais et al. (2017) Reconciled rat and human metabolic networks for comparative toxigenomics and biomarker predictions. Nature Communications, 8:14250

## Mouse models

- iMM1415 : Sigurdsson et al. (2010) A detailed genome-wide reconstruction of mouse metabolism based on human Recon1. BMC Systems Biology, 4:140.

## CHO models

- iCHOv : Hefzi et al. (2016) A consensus Genome-scale reconstruction of Chinese Hamster Ovary cell metabolism. Cell Systems, 3(5):434-443.

# Curation of metabolic tasks

The curation has been done by first taking the union of previously published lists of metabolic task (Recon2 and iHsa). We removed duplicated tasks or lumped the ones that were relying on the description of similar metabolic functions. Each remaining task for which we didn’t found significant biological evidence has been removed. We also created 9 new tasks that were essential for the acquisition of already described metabolic function (i.e. intermediary biosynthetic steps for the acquisition of a task). Doing so, we obtained a collection of 195 tasks associated to 7 systems (energy, nucleotide, carbohydrates, amino acid, lipid, vitamin & cofactor and glycan metabolism).

# Parameters

- **data**\* - 	A .mat or .csv expression matrix with rows corresponding to genes and columns corresponding to samples. Should contain a header row starting with "genes" and containing all of the sample names.
- **SampleNumber**\* - The number of samples in the input file to analyze
- **ref**\* - Name of the reference model to use to compute metabolic task scores
- **param\_ThreshType**\* - 	The thresholding type to use - global or local
- **param\_percentile\_or\_value**\* - The type of threshold to use, either user-supplied cutoff values or cutoff percentiles
- **param\_LocalThresholdType**\* - The type of local thresholding, either minmaxmean or mean
- **param\_percentile\_value\_low**\* - The lower bound to use for thresholding, either a value or a percentile
- **param\_percentile\_value\_high**\* - The upper bound to use for thresholding, either a value or a percentile
