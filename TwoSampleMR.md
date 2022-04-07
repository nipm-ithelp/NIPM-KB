# How to install TwoSampleMR on val

1. Pull the latest bioconductor docker container using singularity
the different versions can be found here (note these are bioconductor versions not R versions): 
https://hub.docker.com/r/bioconductor/bioconductor_docker/tags

<pre>
mhan@node01:/scratch/han_lab$ singularity pull docker://bioconductor/bioconductor_docker:RELEASE_3_14
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob 7c3b88808835 done  
Copying blob df3a04b8ec6d done  
Copying blob dcc4404e7235 done  
Copying blob f2f0e0084abe done  
Copying blob e2afd2c22576 done  
Copying blob 5e1cc1bb0e86 done  
Copying blob 6e95d27e9be8 done  
Copying blob 3aa5f9921aa2 done  
Copying blob 65dbbf4976cb done  
Copying blob 16a493eaf9fc done  
Copying blob 4fdcc9f4b6a8 done  
Copying blob 4beb047f9752 done  
Copying blob e89b02354f65 done  
Copying blob 8d1e85bb06bf done  
Copying blob d4febcc3a82f done  
Copying blob 9ef6b8ac7034 done  
Copying blob 1b3045d5e84e done  
Copying config e4db6c112f done  
Writing manifest to image destination
Storing signatures
2022/04/06 18:11:12  info unpack layer: sha256:7c3b88808835aa80f1ef7f03083c5ae781d0f44e644537cd72de4ce6c5e62e00
2022/04/06 18:11:13  info unpack layer: sha256:df3a04b8ec6dab5747870af5fb7e191b04a1ffb4c08112f558aa9ba030f826a2
2022/04/06 18:11:13  info unpack layer: sha256:dcc4404e72355c738ea8d40b023ba6e9779a25f92c06db660535b96fdcae54a0
2022/04/06 18:11:24  info unpack layer: sha256:f2f0e0084abe4faa86b4070163ab4dbc4cfef9fecb3c71472dd53a1f13e061cf
2022/04/06 18:11:24  info unpack layer: sha256:e2afd2c225768fa05c833dac9f92a76f63bef6a61c64009dae4ecc21e3d4509a
2022/04/06 18:11:24  info unpack layer: sha256:5e1cc1bb0e8627b4acb34e0fbdb6cec1a994d83f2c48dfe4874ac59c05e7127b
2022/04/06 18:11:33  info unpack layer: sha256:6e95d27e9be837c94792e662e14b8106ba1eaad443eb56aecca161bfa97372ec
2022/04/06 18:11:33  info unpack layer: sha256:3aa5f9921aa2caf0d185d2b876918d21183e956baf339ba41508acb68e07feb4
2022/04/06 18:11:33  info unpack layer: sha256:65dbbf4976cb9a9641203ed5216f62ee2aa7fcb84a0fc2ed474b65e1005c08fd
2022/04/06 18:11:33  info unpack layer: sha256:16a493eaf9fc9608a6e1e08da353ba8adee87d649c5132153da292ee6db730da
2022/04/06 18:11:51  info unpack layer: sha256:4fdcc9f4b6a80dbb24551731c11fc273a8135cb0d1c0af990b3d4868a84abc9f
2022/04/06 18:11:58  info unpack layer: sha256:4beb047f9752358583dbeb2799f8861c844e18da6380b550a7b74079b29fedc2
2022/04/06 18:12:01  info unpack layer: sha256:e89b02354f65804c600980cf1a39656c8f0d593361e499d20126dd18bd79f097
2022/04/06 18:12:07  info unpack layer: sha256:8d1e85bb06bf251b7fe06c5b3625391fd4d7057ddc4af96ebaffdb56f6fb129c
2022/04/06 18:12:07  info unpack layer: sha256:d4febcc3a82f8a89bf78264520b7c4521c6abc716f7779226113f228ba8e0a91
2022/04/06 18:12:07  info unpack layer: sha256:9ef6b8ac70344fae44c3627997100d4db8a325ce75f532694daddc57262e6cde
2022/04/06 18:12:09  info unpack layer: sha256:1b3045d5e84eddcca036961a13cf72dcffa86a0b7230ded430ef15dd2faed3ee
INFO:    Creating SIF file...
mhan@node01:/scratch/han_lab$ ls
bioconductor_docker_RELEASE_3_14.sif
</pre>


2. Go into singularity container and run R

<pre>
mhan@node01:/scratch/han_lab$ singularity shell --bind /scratch bioconductor_docker_RELEASE_3_14.sif 
Singularity> R

R version 4.1.3 (2022-03-10) -- "One Push-Up"
Copyright (C) 2022 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

>
</pre>


3. Install TwoSampleMR using their devtools command. 

<pre>
library(devtools)
install_github("MRCIEU/TwoSampleMR")
</pre>


4. Whenever it asks for update, type enter to refuse. 

<pre>
Enter one or more numbers, or an empty line to skip updates: 
</pre>

5. Wait until the compilation finishes

<pre>
......

* installing *binary* package ‘MendelianRandomization’ ...
* DONE (MendelianRandomization)

The downloaded source packages are in
	‘/scratch/tmp/RtmpaG9I6y/downloaded_packages’
Skipping install of 'RadialMR' from a github remote, the SHA1 (d63d3fc8) has not changed since last install.
  Use `force = TRUE` to force installation
Skipping install of 'MRMix' from a github remote, the SHA1 (56afdb2b) has not changed since last install.
  Use `force = TRUE` to force installation
✔  checking for file ‘/scratch/tmp/RtmpaG9I6y/remotes49f25e29caf3/MRCIEU-TwoSampleMR-2bb4947/DESCRIPTION’ ...
─  preparing ‘TwoSampleMR’:
✔  checking DESCRIPTION meta-information ...
─  checking for LF line-endings in source and make files and shell scripts
─  checking for empty or unneeded directories
   Omitted ‘LazyData’ from DESCRIPTION
─  building ‘TwoSampleMR_0.5.6.tar.gz’
   
Installing package into ‘/home/mhan/R/x86_64-pc-linux-gnu-library/4.1’
(as ‘lib’ is unspecified)
* installing *source* package ‘TwoSampleMR’ ...
** using staged installation
** R
** inst
** byte-compile and prepare package for lazy loading
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded from temporary location
** testing if installed package can be loaded from final location
** testing if installed package keeps a record of temporary installation path
* DONE (TwoSampleMR)
</pre>

6. Load the library

<pre>
> library(TwoSampleMR)
TwoSampleMR version 0.5.6 
[>] New: Option to use non-European LD reference panels for clumping etc
[>] Some studies temporarily quarantined to verify effect allele
[>] See news(package='TwoSampleMR') and https://gwas.mrcieu.ac.uk for further details
</pre>





