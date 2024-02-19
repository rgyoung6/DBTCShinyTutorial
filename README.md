# DBTCShinyTutorial
A step-by-step example on the use of the DBTCShiny package.

# Description

This repository contains the files and instructions to use the DBTCShiny package [rgyoung6/DBTCShiny](https://github.com/rgyoung6/DBTCShiny). The DBTC functions have four main outcomes...

  - Fastq file processing using Dada in R
  - Using the Basic Local Alignment Search Tool (BLAST) amplicon sequence variants (ASV) can be searched against local NCBI or custom libraries
  - Assign taxa to the unique reads using NCBI taxon database through taxa names and/or taxaID's
  - Condense the resulting ASV taxonomic assignment tables to unique taxa with the ability to combine datasets (using different sequence libraries for the same reads, or results from the same samples for different molecular regions) into a combined results table

Before using these files and working through this tutorial please install the [DBTCShiny](https://github.com/rgyoung6/DBTCShiny) package.

# Table of Contents  
- [Data](#data)
- [Initial Considerations](#initial-considerations)
- [Getting Started](#getting-started)
- [Dada Implement](#dada-implement)
- [Combine Dada Output](#combine-dada-output)
- [Make BLAST DB](#make-blast-db)
- [Sequence BLAST](#sequence-blast)
- [Taxon Assignment](#taxon-assignment)
- [Combine Assignment Output](#combine-assignment-output)
- [Reduce Taxa](#reduce-taxa)
- [Combine Reduced Output](#combine-reduced-output)
- [References](#references)

# Data 

The data for the tutorial comes from the publication Young _et al._ 2021 which used passive collection of insects using Lindgren funnel traps with a high salt solution to survey for invasive insects. The insects were removed from the traps and the salt solution was retained and used to extract environmental DNA (eDNA). The data used in the examples in this tutorial are a subset of the data obtained from Young et al. 2001.

To download the data for the tutorial go to the main [DBTCShinyTutorial](https://github.com/rgyoung6/DBTCShinyTutorial) GitHub repository page and select the green button labelled 'Code' and then select 'Download ZIP' (see image below). This will save all of the tutorial files you need to your local computer (Please note that this is a fairly large file approxitmately 526mb). Once downloaded uncompress the contents and place the 'DBTCShinyTutorial-main' in an accessible area of your computer ideally near the root. Also, please make sure that you have permissions for this location so that the files will be accessible for reading and the folders for writing data.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/bac440d1-1d13-4e9a-8858-5fea48c872fc)

([Back to Top](#table-of-contents))

# Initial Considerations

When working with the DBTCShiny applicaiton there are some limitations. The functions in the package make use of several external resources and programs. The programs do not work well when there is white space in the naming conventions of files and directories. As such it is reccommended that when using DBTCShiny, working files and folders be as close to the root directory (computer or external hard drive). 

Also, special characters should be avoided (including question mark, number sign, exclamation mark). It is reccommended that dashes be used for separations in naming conventions while retaining underscores for use as information deliminters (this is how DBTC functions use underscore). 

There are several key character strings used in the DBTC pipeline, the presence of these strings in file or folder names will cause errors when running DBTC functions. 
The following strings are those used in DBTC and should not be used in file or folder naming:
  - _BLAST
  - _taxaAssign
  - _taxaCombined
  - _taxaReduced

Finally, the Shiny applicaiton uses <a id="buttons"></a>, for example the Dada function 'Data Location'.  Once clicked these buttons will do one of two things. 

  1. The buttons will bring up a dialog window (referred to as an 'Open' dialog in iOS or an 'Open File' dialog in Windows and a 'File Picker' dialog in Linux and referred throughout this tutorial as an 'select file dialog window'). These dialog windows are sometimes opened behind the RShiny DBTCShiny dashboard window. Clicking the R source (R terminal window or Posit/R Studio application) will bring up the dialog window for use. 
  2. In some cases (such as launching DBTCShiny using a terminal window in iOS or Linux or running the R exeuctable in Windows) the file selection will appear in the terminal or exeuctable window where DBTCShiny was initially launched. This input will need to be entered by typing the path manually.

To avoid the second situation it is best to run the DBTCShiny through [Posit](https://posit.co/download/rstudio-desktop/).

([Back to Top](#table-of-contents))

# Getting Started

All descriptions and comments used in the tutorial below relate to the DBTCShiny application and the graphical user interface to implement the functions.

To begin, ensure that the DBTCShiny package is installed in your instance of R and that you have loaded the package through the library('DBTCShiny') command (see the [DBTCShiny GitHub repository](https://github.com/rgyoung6/DBTCShiny/blob/main/README.md#installation)).

Once all dependencies and the DBTCShiny package are installed and loaded run the program using the following command...

```
launchDBTCShiny()
```

Once run a window will appear in your computers default web browser with the title screen for DBTCShiny (see below)

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/fbf0fb3d-e6bc-4d09-9996-b73d6e1af73e)

There are several expandable panels on the initial page that can be explored for more information on installation, dependencies, and contact for troubleshooting. 

The side panel of DBTCShiny contains three additional options. The first option, DBTC Tools conatins all of the main high-throughput processing functions of DBTCSHiny and this is the section where we will first start. Once DBTC Tools is clicked it will show a page with the 8 tabs for the main functions of DBTCShiny. The first tab is the Dada tab and this is where our tutorial will begin (See image below). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/30653efa-ee61-413c-9974-b1c901b975a3)

Finally, before moving on be sure that you have the data downloaded and stored on your local machine (see here if unsure [Data](#data)).

([Back to Top](#table-of-contents))

# Dada Implement

DBTCShiny uses [dada2](https://benjjneb.github.io/dada2/) to complete the analysis of raw fastq files generated from high-throughput sequencing runs. While this tutorial will cover some of the possible settings possible when implementing dada2, for more details on the specifics of the settings available please read through the [dada2](https://benjjneb.github.io/dada2/) documentation. There are five sections within the DBTCShiny Dada submission. We will reference each section in turn below with respect to runnig our example analyses.

## 1. General Information
   
### Data Location button
Once clicked this button will bring up an open select file dialog window to select the data to process ([see exceptions](#buttons)). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/f776dc25-bc2b-402c-aa7c-1e5276b9eb9f)

For the data provided in this tutorial there are two options, unidirectional data and bidirectional data. Using the select file dialog window navigate to the downloaded data location and select the A-Dada folder. In this folder there are two subfolders and two files one of each for bidirectional data and unidirectional data. Navigate to one of the foldersin this location either unidirectional or bidirectional, see below for descriptions of each folder.

Unidirectional - DBTCShinyTutorial-main/A-Dada/SaltTrapUnidirectional 
The fastq data in this folder do not contain both forward and reverse paired read fastq data files. This dataset only contains one direction of sequence data and the data in this folder can be used to test the unidirectional processing in DBTCShiny (see the directional processing section below). 

Bidirectional - DBTCShinyTutorial-main/A-Dada/SaltTrapBidirectional
The bidirectional data is contained in the SaltTrapBidirectional folder and will be the data that will be used throughout this tutorial. **This is the data you should select.**

When selecting data they need to be in the proper data format (see <a href="https://github.com/rgyoung6/DBTCShiny/tree/main?tab=readme-ov-file#dada-implement" target="_blank">here</a>). These examples are in the correct file structure. The user must select one of the files within any of the runs inside the SaltTrapBidirectional folder. Please also note how all of the fastq files are compressed in .gz format and are not in any further file structures in the run folders. Once a file is selected this will close the selection window and show that a file was selected in the DBTCShiny web browser window.

### Primer File
The second button is the Primer File button. Again, once selected it will bring up a select file dialog window.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/a32bc0cb-94b6-4b2f-8421-85c3858e763c)

Navigate to the same A-Dada folder. In this folder there are two primer files (SaltTrapPrimers-Bidirectional.tsv and SaltTrapPrimers-Unidirectional.tsv). **Select the bidirectional primer file** (please note if unidirectional was selected above you will need the unidirectional primer file). The format of this file can be viewed by opening it or you can see the description <a href="https://github.com/rgyoung6/DBTCShiny/tree/main?tab=readme-ov-file#dada-implement" target="_blank">here</a>). The data in this file will be used with the R ShortRead package and the trimLRPatterns() function to remove primers by pattern matching from the ends of the sequences. Note: With your own data if you are getting poor quality matches to library sequences it could be due to the presence of primers and other indicies or tags which were not properly removed from the sequence data and the contents of this file should be considered in these cases.

### Directional Processing
There are three options possible to indicate the directinoal data present in your samples. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/70cf5d05-a321-412f-8b48-6897b641293f)

Bidirectional - If selected the data will be processed using both forward and reverse reads. The analysis will merge these reads and the data coming out of the analysis will be merged reads. **For the purposes of this tutorial we will process the data only bidirectionally.**

Unidirectional - If selected the data will be processed only in the forward direction. The outcome of the analysis will not be from paired and merged reads but will only represent data from a single direction. Note: if this is selected the Forward identifier and the Reverse identifier sections will disapear from the DBTCShiny interface as these are not needed to identify the forward and reverse elements of paired reads when only processing with on direction (see image below).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/bdab251c-4698-4178-9382-1ecead917a90)

Both - If selected there will be three parallel runs of the data, one merged using both forward and reverse reads, a second analysis using foward reads, and a third analysis using reverse reads (descriptors to indicate the type of analysis will be present on output files and within summary data files. Note: processing using both will significanlty increase the time to analysis, however, the independent forward and reverse analyses may be helpful to assess if there was poor amplification or quality in one of the directions. This could possible help to identify reasons for poor merged data or help to better understand primer issues.

([Back to Top](#table-of-contents))

### Forward and Reverse Identifiers
There are two fillable fields, one for forward and one for reverse. These fields indicate the patterns in the naming convention of the files that will identify if the data file contains forward or reverse data. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/7820f894-24b0-4212-b5ad-24831683f7b0)

The default contents of these fields are patterns often used in MiSeq generated files. If you have other naming conventions you can type them in here now. **For this tutorial we will use the default patters.**

### Print Quality Plots
This binary selection will, if yes is selected, output files in .pdf format with plots of the quality for the reads being processed. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/b302f046-4a89-4605-ad44-fc57484160a4)

These results will be generated for the initial fastq files and the cleaned and trimmed fastq files. The data from the quality analyses are also reported in the final reporting table but these files allow you to visualize the data. The default for this selection is to produce these files, **for the purposes of this tutorial we will select no and not produce these files**.

## 2. Pattern Trim

### Maximum number of Mismatches
This section has a single field that accepts numeric input. This value is used by the ShortRead trimLRPatterns() pattern matching function when pattern matching to trim off primers and other artifical nucleotide sequence data at the end of reads. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/a6360bd1-c2aa-4198-a25e-8e283bbc2728)

The default for this field is 2. If there are very large primer regions then increasing this value could be considered, but for the purposes of **this tutorial the default value of 2 will be used.**

## 3. Dada filterAndTrim
This section contains field used by dada2 when end trimming and quality filtering. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/f3b3dc42-529e-42e4-a379-1f7e38b42c06)

The first two fields accepts values for the dada2 end trimming function (dada2 parameter trimLeft). This function trims from the left and each of the two values here will trim the forward or reverse read respectively (For more information see <a href="https://benjjneb.github.io/dada2/ITS_workflow.html" target="_blank">here</a>). When pattern trimming this function is not utilized. **The default for these values is 0 and these are the values that will be used in this tutorial.** 

The third field accepts a value for the dada2 maximum number of expected errors. length trimming function where it identifies the maximum expected errors value (dada2 maxEE parameter and for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>). **For the purposes of this tutorial we will use the default value of 2**

The next fillable fields provide data on quality trimming and length trimming. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/7d0b609e-29d4-4dd8-b472-6ce7580992e2)

The truncation value field (dada2 parameter truncQ for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>) provides information on the quality where the sequences are trimmed. **The default for this value is 2 and this is what will be used for this tutorial.**

The final two filable fields are the truncLenValueF and the truncLenValueR values. These values trim the nucleotide sequences based on the overall expected length of the sequences (for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>). When pattern trimming this function is not used and so these values are set to 0 by default and **we will use 0 for the tutorial.**


## 4. Dada learnErrors
This section provides information on the error assessment and quality filtering of sequences using the dada2 learnErrors() function. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/72d904bb-12ec-4736-8d8d-f8fcdc7e8280)

The first fillable field in the Dada learnErrors section indicates the percent of data to use when assessing errors. The dada2 analysis generally evaluates the total number of reads present in a submitted run (a run is a group of results processed at the same time on the same machine representing the same molecular methods) and then takes a percentage of the sequences and estimates the error present. The DBTCShiny uses the same learnErrors() function, but instead forces the function to use a subset of the data by fastq file wholistically. In doing this we can provide the files used for the error estimation and the process is reproducible. The first fillable section here identifies the percentage of fastq files used for the analysis. The default is 0.1 or 10%, but in cases where there are very few results files and when the percent of the files is three or fewer, the minimum number of files is automatically set to three. **For our tutorial we will use the default value of 0.1.**

The second fillable field is for the upper value of the total number of nucleotides used for the error assessment. This value is set very high to allow the selection of files for the error assessment. Lowering this value will cause the assessment of the errors to potentially use only a subset of the selected file subset of files. **The default of value for this field is set to 1,000,000,000 and this is the value we will used for this tutorial.**

## 5. Dada mergePairs
This section includes fields necessary for the merging of Pairs section. Please note that this section will disappear if the unidirectional data analysis is selected in the 1. Gerneal Information section.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/3d0713b3-63b3-4a30-93bd-81065d9bc48c)

The first fillable field in the Dada mergePairs section provides a value for the maximum number of mismatches allowed in the merge analysis. The mismatches represents the number of acceptable mismatches in the overlapping sections between merged forward and reverse reads. The smaller this number the more constrained the analysis and the more potential merged pairs will be rejected. As the overlapping region increases, the corresponding increase in the acceptable number of mismatches may result in more reads at the end of the analysis. If this value is lower it will also provide higher confidence in the reulting reads. **The default value is set to 2 and this is the value that will be used in this tutorial.**

The second field in this section is the minimum total number of overlapping nucleotides required to merge forward and reverse pairs. The default value is set to 12 and this is the value that will be used fo rthis tutorial.

# References 
Young, R. G., Milián‐García, Y., Yu, J., Bullas‐Appleton, E., & Hanner, R. H. (2021). Biosurveillance for invasive insect pest species using an environmental DNA metabarcoding approach and a high salt trap collection fluid. Ecology and Evolution, 11(4), 1558-1569.

([Back to Top](#table-of-contents))
