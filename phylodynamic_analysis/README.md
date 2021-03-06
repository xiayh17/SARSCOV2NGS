# Phylodynamic analyses

The phylodynamic analyses presented in this report depend on having [BEAST v2.6.2](https://github.com/CompEvol/beast2), [PhyDyn v1.3.6](https://github.com/mrc-ide/PhyDyn), and [CoupledMCMC v1.0.1](https://github.com/nicfel/CoupledMCMC) installed. It also depends on having already generated a ML starting tree (data/tree.nwk) and an alignment (data/masked.fasta) of your sequences. In the future we plan to extend these scripts to allow more flexibility in the generation of the Beast XML file, particularly the use of random starting trees. The file scripts/process_fasta.py takes in the starting tree and alignment and labels sequence names in the tree and alignment with date of sampling and whether the sequences are considered part of the global reservoir of sequences ("exog") or whether they are sequences from Israel ("Il" or "Ih"). Sequences from the focal region are randomly assigned to "Ih" with probability p_h, else they are assigned to "Il." This script then calls the scripts/generate_xml.py file which inserts your model parameters into the XML template of choice (located in config/). This XML template was originally generated using BEAUti (https://github.com/CompEvol/beast2) but has been extensively edited by hand. 

Currently, this repository contains code and XML files for four different analyses: 
1. The analysis presented in our [MedRxiv preprint](https://www.medrxiv.org/content/10.1101/2020.05.21.20104521v1) which is labelled as "preprint" in the data/ and figures/ directories. In this analysis we attempted to fit the import rate using an exponential prior with a mean of 10 and an upper limit of 10, in line with [previous PhyDyn based analyses](https://www.medrxiv.org/content/10.1101/2020.03.09.20033365v2.full.pdf). This import rate is per-capita and individuals in E, Il, and Ih are able to migrate into and out of the exog category. This analysis was repeated for a range of pH values (0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.07, 0.08, 0.09, 0.10, 0.20). These chains were run for roughly 2 million MCMC steps. We're in the process uploading Beast logs and trees for this analysis to Zenodo. 
2. The analysis which was included in the submitted manuscript which is labelled as "june3_fix_import" in the data/ directory and "submission_results" in the figures/ directory. In this analysis rather than try to fit the import rate we fix it to a given constant value (NOT per-capita). Additionally, we only allow individuals to migrate in and out of Exog from/to the E class. These models use the config/SEIR_TEMPLATE_FIX_IMPORT.xml template for these models. We repeat this analysis for a range of import rate (10, 100, 1000, 2500, 5000) values and a range of pH values (0.02, 0.05, 0.10, 0.20, 0.50, 0.80). These chains were run for roughly 10 million MCMC steps. 
3. An extended anlysis with constant importation rates which is labelled "june11_CMCMC_Final". To aid in convergence we utilized [Metropolis Coupled MCMC](https://academic.oup.com/bioinformatics/article/20/3/407/186341) with three chains and a desired acceptance probability of 0.234. All other model settings are the same. We also added an up-down Beast operator on the E and R0 parameters. These models utilize the config/SEIR_TEMPLATE_FIX_IMPORT_UPDOWN_CMCMC.xml template. These chains were run for 10 million MCMC steps for pH values of 0.02, 0.05, 0.10, 0.20, 0.50, 0.80).
4. An analysis with an empirical estimate of import rate which is labelled as "july22_TMRCA_Import_Final" To do this we first estimated dates of introduction into Israel using ancestral state reconstruction of a relatively large (\~5000 samples) downsapled global tree. These dates are tabulated and a piecewise exponential model fit to the data using the scripts/import_analysis.py file. The underlying data is in data/dates_tmrca.csv file. We also performed a sensitivity analysis by scaling the growth/decay rates of these exponential curves by factors of 0.8, 0.9, 1.0, 1.1, adn 1.2. The empirical data and model fits are presented in the [figures/importation_dates](https://github.com/SternLabTAU/SARSCOV2NGS/blob/master/phylodynamic_analysis/figures/importation_dates_3.0.pdf) file. This modeled curve is then implemented as the import rate (not per-capita) in the beast model using the config/SEIR_TEMPLATE_FIX_IMPORT_UPDOWN_CMCMC.xml template. These models were run for 10 million MCMC steps for pH values of 0.02, 0.05, 0.10, 0.20, 0.50, 0.80). 

Analyses #3 and #4 are what is presented in the final manuscript. The Beast outputs for these runs have been uploaded to Zenodo and are available here: [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.3946651.svg)](https://doi.org/10.5281/zenodo.3946651)



Other priors (for the results in the final manuscript) include: 
- Clock rate: Uniform(5.0e-4, 2.0e-3)
- HKY Kappa: Lognormal(M=1.0, SD=1.25)
- Gamma shape: Exponential(M=1.0)
- R0: Lognormal(M=1.5, SD=0.5)
- alpha: Uniform(0.0, 2.0)
- E_init: Expoenential(M=1.0)
- Exog_init: Exponential(M=1.0)

Results were visualized using [Matplotlib](https://matplotlib.org) and [Baltic](https://github.com/evogytis/baltic) using the scripts/combine_plot_traj.py file. MCC trees were generated using [TreeAnnotator](https://github.com/CompEvol/beast2). 

Please note that the sequence with ID Israel/SH16/2020 in our study has ID Israel/13075879/2020 on GISAID (EPI_ISL_447443). We apologize for any confusion this may have caused. 


