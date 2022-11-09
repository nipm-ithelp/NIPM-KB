# How to use any version of R on val

1. Pull the latest Rocker docker container using singularity.
the different versions can be found here: 

https://hub.docker.com/r/rocker/r-ver/tags

<pre>
mhan@node01:/scratch/han_lab/rocker$ singularity pull docker://rocker/r-ver:4.2.2
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob e96e057aae67 done  
Copying blob bc51a8f1fa3b done  
Copying blob 0abc830544e9 done  
Copying blob d6e7a8cc8197 done  
Copying blob 2a88509eb7d5 done  
Copying config 7011c7368c done  
Writing manifest to image destination
Storing signatures
2022/11/07 11:22:42  info unpack layer: sha256:e96e057aae67380a4ddb16c337c5c3669d97fdff69ec537f02aa2cc30d814281
2022/11/07 11:22:44  info unpack layer: sha256:bc51a8f1fa3b4df846119307b33b000a62a6bf03085fb1ef136a35aa18c80e37
2022/11/07 11:22:44  info unpack layer: sha256:0abc830544e9ff1f0dcaafcdac48ab71958e085155e24b42740a89cd61fbd73f
2022/11/07 11:22:54  info unpack layer: sha256:d6e7a8cc819736157eeee47d1dbb41120b807e57f354bbe50a855e94bbc874e3
2022/11/07 11:22:54  info unpack layer: sha256:2a88509eb7d54dc7c4cd322b299ffa8d6c3027703ba2afe726aa17bd4e04e6c7
INFO:    Creating SIF file...
mhan@node01:/scratch/han_lab/rocker$ ls -als
total 309M
   0 drwxr-xr-x 2 mhan Han_lab   29 Nov  7 11:23 .
   0 drwxrwxr-x 9 mhan Han_lab  124 Nov  7 11:22 ..
309M -rwxr-xr-x 1 mhan Han_lab 309M Nov  7 11:23 r-ver_4.2.2.sif

</pre>


2. Go into singularity container and run R

<pre>
mhan@node01:/scratch/han_lab/rocker$ singularity shell --bind /scratch r-ver_4.2.2.sif
Singularity> ls
r-ver_4.2.2.sif
Singularity> which R
/usr/local/bin/R
Singularity> R

R version 4.2.2 (2022-10-31) -- "Innocent and Trusting"
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


3. Install any package you need. 

<pre>
> install.packages("glmnet") 
Installing package into ‘/usr/local/lib/R/site-library’
(as ‘lib’ is unspecified)

</pre>


4. If it asks to create a personal library answer yes. 

<pre>
Warning in install.packages("glmnet") :
  'lib = "/usr/local/lib/R/site-library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel) yes
Would you like to create a personal library
‘/home/mhan/R/x86_64-pc-linux-gnu-library/4.2’
to install packages into? (yes/No/cancel) yes
</pre>

5. Wait until the installation finishes

<pre>
Warning in install.packages("glmnet") :
  'lib = "/usr/local/lib/R/site-library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel) yes
Would you like to create a personal library
‘/home/mhan/R/x86_64-pc-linux-gnu-library/4.2’
to install packages into? (yes/No/cancel) yes
also installing the dependencies ‘iterators’, ‘foreach’, ‘shape’, ‘Rcpp’, ‘RcppEigen’

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/iterators_1.0.14.tar.gz'
Content type 'binary/octet-stream' length 345456 bytes (337 KB)
==================================================
downloaded 337 KB

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/foreach_1.5.2.tar.gz'
Content type 'binary/octet-stream' length 137791 bytes (134 KB)
==================================================
downloaded 134 KB

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/shape_1.4.6.tar.gz'
Content type 'binary/octet-stream' length 783376 bytes (765 KB)
==================================================
downloaded 765 KB

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/Rcpp_1.0.9.tar.gz'
Content type 'binary/octet-stream' length 3780950 bytes (3.6 MB)
==================================================
downloaded 3.6 MB

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/RcppEigen_0.3.3.9.3.tar.gz'
Content type 'binary/octet-stream' length 1548623 bytes (1.5 MB)
==================================================
downloaded 1.5 MB

trying URL 'https://packagemanager.rstudio.com/cran/__linux__/jammy/latest/src/contrib/glmnet_4.1-4.tar.gz'
Content type 'binary/octet-stream' length 2108389 bytes (2.0 MB)
==================================================
downloaded 2.0 MB

* installing *binary* package ‘iterators’ ...
* DONE (iterators)
* installing *binary* package ‘shape’ ...
* DONE (shape)
* installing *binary* package ‘Rcpp’ ...
* DONE (Rcpp)
* installing *binary* package ‘foreach’ ...
* DONE (foreach)
* installing *binary* package ‘RcppEigen’ ...
* DONE (RcppEigen)
* installing *binary* package ‘glmnet’ ...
* DONE (glmnet)

The downloaded source packages are in
	‘/scratch/tmp/Rtmpkb4vCc/downloaded_packages’
> 

</pre>

6. Load the library

<pre>
> library(glmnet)
Loading required package: Matrix
Loaded glmnet 4.1-4
> 

</pre>




