# DBTCShinyTutorial
A step-by-step example on the use of the DBTCShiny package.

# Description

This repository contains the files and instructions to use the DBTCShiny package [rgyoung6/DBTCShiny](https://github.com/rgyoung6/DBTCShiny). The DBTC functions have four main outcomes...

  - [Fastq](https://en.wikipedia.org/wiki/FASTQ_format) file processing using Dada in R
  - Using the Basic Local Alignment Search Tool ([BLAST](https://en.wikipedia.org/wiki/BLAST_(biotechnology))) amplicon sequence variants ([ASV](https://en.wikipedia.org/wiki/Amplicon_sequence_variant)) can be searched against local NCBI or custom libraries
  - Assign taxa to the unique reads using NCBI taxon database through taxa names and/or taxaID's
  - Condense the resulting ASV taxonomic assignment tables to unique taxa with the ability to combine datasets (using different sequence libraries for the same reads, or results from the same samples for different molecular regions) into a combined results table

Before using these files and working through this tutorial please install the [DBTCShiny](https://github.com/rgyoung6/DBTCShiny) package.

# Table of Contents  
- [Data](#data)
- [Initial Considerations](#initial-considerations)
- [Getting Started](#getting-started)
- [Dada Implement](#dada-implement)
  - [General Informaiton](#general-information)
      - [Fastq Files](#fastq-files)
      - [Primer File](#primer-file)
      - [Directional Processing](#directional-processing)
      - [Forward and Reverse Identifiers](#forward-and-reverse-identifiers)
      - [Print Quality Plots](#print-quality-plots)
  - [Pattern Trim](#pattern-trim)
      - [Maximum Number of Mismatches](#maximum-number-of-mismatches)
  - [Dada Filter and Trim](#dada-filter-and-trim)
      - [Trim Length and Errors Allowed](#trim-length-and-errors-allowed)
      - [Quality Trim and Length Trim Options](#quality-trim-and-length-trim-options)
  - [Dada Learn Errors](#dada-learn-errors)
  - [Dada Merge Pairs](#dada-merge-pairs)
  - [Dada Implement Function Output](#dada-implement-function-output)
      - [Quality Plot Folders](#quality-plot-folders)
      - [Filtered and Filtered and Trimmed Files](#filtered-and-filtered-and-trimmed-files)
      - [Main Output Files](#main-output-files)
      - [Next Step Data Files](#next-step-data-files)
- [Combine Dada Output](#combine-dada-output)
- [Make a BLAST Database](#Make-a-blast-database)
- [BLAST Sequences](#blast-sequences)
- [Taxon Assign](#taxon-assign)
- [Combine Taxon Assign](#combine-taxon-assign)
- [Reduce Taxa Assign](#reduce-taxa-assign)
- [Combine Reduced Taxon Assign](#combine-reduced-taxon-assign)
- [Mapping Dashboard](#mapping-dashboard)
- [References](#references)

# Data 

The data for the tutorial comes from the publication Young _et al._ 2021 which passively collected insects using Lindgren funnel traps with a high salt solution to survey for invasive insects. The insects were removed from the traps and the salt solution was retained and used to extract environmental DNA (eDNA). The data used in the examples in this tutorial are a subset of the data obtained.

To download the data for the tutorial go to the main [DBTCShinyTutorial](https://github.com/rgyoung6/DBTCShinyTutorial) GitHub repository page and select the green button labelled 'Code' and then select 'Download ZIP' (see image below). This will save all of the tutorial files you need to your local computer (Please note that this is a fairly large file approxitmately 526mb). Once downloaded, extract the contents of the compressed file and place the 'DBTCShinyTutorial-main' in an accessible area of your computer ideally near the [root](https://en.wikipedia.org/wiki/Root_directory). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/bac440d1-1d13-4e9a-8858-5fea48c872fc)

## Permissions 

Also, please make sure that you have [permissions](https://en.wikipedia.org/wiki/File-system_permissions) for this location so that the files will be accessible for reading, the folders for writing data, and that the blastn and makeblastdb files are able to be executed (if you are using these program files instead of loading the programs as trusted software into your system). For Windows, it is not often a problem to run these programs. For Linux [terminal](https://en.wikipedia.org/wiki/Linux#User_interface) and Mac OS [terminal](https://en.wikipedia.org/wiki/Terminal_(macOS)) open a terminal window and navigate to the container holding the 'DBTCShinyTutorial-main' folder you have downloaded and extracted. Once there, use the following command to change the permissions on the BLAST program files (Note: you may need user [access](https://en.wikipedia.org/wiki/Computer_access_control) to change the file permissions).

```
chmod -R 0777 DBTCShinyTutorial-main
```

For Mac OS another issue may arise where the BLAST software is not recognized as a trusted program. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/2b567c20-9856-42ef-9619-de2a410eaad0)

To allow the computer to run makeblastdb as a trusted program you will need to navigate to the 'DBTCShinyTutorial-main' in a [Finder](https://en.wikipedia.org/wiki/Finder_(software)) window. Once there go to the file of choice (makeblastdb or blastn) and do the following...
    
  **Right-Click the Application:** Instead of double-clicking to open the file, right-click on the program file.
  
  **Choose Open:** From the context menu that appears after right-clicking, choose "Open." This action should present you with an option to open the file even though it's from an unidentified developer.
  
  **Open Anyway:** After selecting "Open," macOS will display a warning dialog stating that the application cannot be opened because it's from an unidentified developer. In this dialog, there should be an "Open Anyway" button. Click on this button to bypass the warning and open the program file.
  
  **Confirm Your Choice:** You may need to enter your administrator password or confirm your choice before macOS allows the file to be opened. Follow the on-screen prompts to complete this step.

Once complete the program should be usable (Note: the process to indicate that this is a trusted program may need to be completed with every MacOS session). Rerun the DBTCShiny and attempt your analysis again.

([Back to Top](#table-of-contents))

# Initial Considerations

When working with the DBTCShiny applicaiton there are some limitations. The functions in the package make use of several external resources and programs. The programs do not work well when there is white space in the naming conventions of files and directories. As such it is reccommended that when using DBTCShiny, working files and folders be as close to the [root](https://en.wikipedia.org/wiki/Root_directory) directory (computer or external hard drive). 

Also, special characters should be avoided (including question mark, number sign, exclamation mark). It is reccommended that dashes be used for separations in naming conventions while retaining underscores for use as information deliminters (this is how DBTC functions use underscore). 

There are several key character strings used in the DBTC pipeline, the presence of these strings in file or folder names will cause errors when running DBTC functions. 
The following strings are those used in DBTC and should not be used in file or folder naming:
  - _BLAST
  - _taxaAssign
  - _taxaCombined
  - _taxaReduced

The DBTCShiny can be run through the terminal window, R window, or through [Posit](https://posit.co/download/rstudio-desktop/).

([Back to Top](#table-of-contents))

# Getting Started

All descriptions and comments used in the tutorial below relate to the DBTCShiny application and the graphical user interface to implement the functions.

To begin, ensure that the DBTCShiny package is installed in your instance of R and that you have loaded the package through the library('DBTCShiny') command (see the [DBTCShiny GitHub repository](https://github.com/rgyoung6/DBTCShiny/blob/main/README.md#installation)).

Once all dependencies and the DBTCShiny package are installed and loaded, start the program using the following command...

```
launchDBTCShiny()
```

Once initiated a window will appear in your computers default web browser with the title screen for DBTCShiny (see below)

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/44274e5b-f7d0-438c-8559-1f30e5a7f19a)

There are several expandable panels on the initial page that can be explored for more information on installation, dependencies, and contact for troubleshooting. 

The side panel (left side of the screen) of DBTCShiny contains two options in addition to the welcome option. The first option, DBTC Tools conatins all of the main high-throughput processing functions of DBTCSHiny and this section is where we will start. Once DBTC Tools is clicked it will show a page with the 8 tabs for the main functions of DBTCShiny (See image below). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/60310743-9135-448e-bba6-291de9b2eea3)

Shiny applicaiton uses <a id="buttons"></a> to select files necessary when running analyses. The buttons will bring up a dialog window (referred to as an 'Open' dialog in Mac OS or an 'Open File' dialog in Windows and a 'File Picker' dialog in Linux and referred throughout this tutorial as an 'select file dialog window'). 

Finally, before moving on be sure that you have the tutorial data downloaded and stored on your local machine (see here if unsure [Data](#data)).

([Back to Top](#table-of-contents))

# Dada Implement

DBTCShiny uses [dada2](https://benjjneb.github.io/dada2/) to complete the analysis of raw fastq files generated from high-throughput sequencing **runs** <a id="runs"></a> (A run is a group of results processed at the same time on the same machine representing the same molecular methods). While this tutorial will cover some of the settings when implementing dada2, for more details on the specifics of available settings please read through the [dada2](https://benjjneb.github.io/dada2/) documentation. There are five sections within the DBTCShiny Dada submission. We will reference each section in turn below with respect to runnig our example analyses.

## General Information
This section provides file locations, processing, and saving options for the Dada analysis of Fastq files.

### Fastq Files

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/0f55cd3d-3cea-4faa-bdb8-fc7ada037e79)

Once clicked this button will open a select file dialog window to select the data files to process. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/23a899c0-514b-4546-84d0-8115ab87e0eb)

The 'Fastq File' selection will need to be in the appropriate file structure (see [Dada Implement Input](https://github.com/rgyoung6/DBTCShiny/blob/main/README.md#dada-implement) in the DBTCShiny ReadMe). Move to the location of your downloaded and extracted [tutorial files](https://github.com/rgyoung6/DBTCShinyTutorial?tab=readme-ov-file#data).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/6045cbac-98da-4d6a-b331-da7741179c15)

Once there we will make use of the A-Dada folder and use one of the two options either Bidirectional or Unidirectional (remember this selection as it will matter in a couple of steps). For the purposes of this tutorial we will use the **Bidirectional data folder**. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/269c0cd7-1396-43e3-89f8-683bff60a09e)

Then select either one of the [runs](#runs) (here we will use the first Run folder but either could be used). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/fb52715a-cd6e-4d24-9858-5c18b489e0f6)

And Finally, we will select one of the fastq files. Note: that these files are compressed, but are not paired within a compressed folder, the files are paired through naming convention see [Forward and Reverse Identifiers](https://github.com/rgyoung6/DBTCShinyTutorial?tab=readme-ov-file#forward-and-reverse-identifiers).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/c298e6c5-37aa-495a-b8c9-5a8a13fc74a1)

([Back to Top](#table-of-contents))

### Primer File

The second button is the **Primer File button and this option will be utilized for this tutorial**. Again, once selected it will bring up a select file dialog window (Note: if this button is not utilized or the selection is cancelled then the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) function will run without the use of a primer file and primer matching and trimming).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/715e6051-50b4-4687-9d02-3cc141ee1464)

Navigate to the same A-Dada folder. In this folder there are two primer files (SaltTrapPrimers-Bidirectional.tsv and SaltTrapPrimers-Unidirectional.tsv). **Select the bidirectional primer file** for this tutorial (please note if unidirectional was selected above you will need the unidirectional primer file). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/97e9b201-fc11-4b10-b270-b8bd2c4f83b8)

The format of this file can be viewed by opening it or you can see the description in the DBTCShiny ReadMe [Dada Implement Format of the Primer File](https://github.com/rgyoung6/DBTCShiny/blob/main/README.md#dada-implement) Section. The data in this file will be used with the R ShortRead package and the trimLRPatterns() function to remove primers by pattern matching from the ends of the sequences. Note: With your own data if you are getting poor quality matches to library sequences it could be due to the presence of primers and other indicies or tags which were not properly removed from the sequence data and the contents of this file should be considered in these cases.

([Back to Top](#table-of-contents))

### Directional Processing
There are three options possible to indicate the directional data present in your samples. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/cffadce4-eb12-4348-844b-c2dc65b89a32)

Bidirectional - If selected the data will be processed using both forward and reverse reads. The Dada analysis will merge these reads and the data coming out of the analysis will be representative of both the forward and reverse reads. **For the purposes of this tutorial we will process the data only bidirectionally.**

Unidirectional - If selected the data will be processed only in the forward direction. The outcome of the analysis will not be from paired and merged reads but will only represent data from a single direction. Note: if this is selected the Forward identifier and the Reverse identifier sections will disapear from the DBTCShiny interface as these are not needed to identify the forward and reverse elements of paired reads when only processing with one direction (see image below).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/cfa3f11f-ac5b-4355-aada-e0ab1d1bad78)

Both - If selected there will be three parallel runs of the data, one merged using both forward and reverse reads, a second analysis using foward reads, and a third analysis using reverse reads (descriptors to indicate the type of analysis will be present on output files and within summary data files. Note: processing using both will significanlty increase the time to analysis, however, the independent forward and reverse analyses may be helpful to assess if there was poor amplification or quality in one of the directions. This could possible help to identify reasons for poor merged data or help to better understand primer issues.

([Back to Top](#table-of-contents))

### Forward and Reverse Identifiers
There are two fillable fields, one for forward and one for reverse. These fields indicate the patterns in the naming convention of the files that will identify if the data file contains forward or reverse data. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/a944db22-2f6b-4a0f-8dd6-908d2c0d456e)

The default contents of these fields are patterns often used in MiSeq generated files. If you have other naming conventions you can type them in here now. **For this tutorial we will use the default patters.**

([Back to Top](#table-of-contents))

### Print Quality Plots
This binary selection will, if yes is selected, output files in .pdf format with plots of the quality for the reads being processed. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/5b31af72-0e4b-4776-b8ae-b6cfe22de396)

These results will be generated for the initial fastq files and the cleaned and trimmed fastq files. The data from the quality analyses are also reported in the final reporting table but these files allow you to visualize the data. The default for this selection is to produce these files, **for the purposes of this tutorial we will select no and not produce these files**.

([Back to Top](#table-of-contents))

## Pattern Trim

### Maximum number of Mismatches
This section has a single field that accepts numeric input. This value is used by the ShortRead trimLRPatterns() pattern matching function when pattern matching to trim primers and other artifical nucleotide sequence data at the end of reads. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/ef2b8d31-7a09-4951-bb99-b037cbcee0ae)

The default for this field is 2 which will allow up to two mismatched nucleotides between primer sequences and experimental reads. If there are very large primer regions then increasing this value could be considered, but for the purposes of **this tutorial the default value of 2 will be used.**

([Back to Top](#table-of-contents))

## Dada Filter and Trim
This section contains field used by dada2 when end trimming and quality filtering. 

### Trim Length and Errors Allowed

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/f3b3dc42-529e-42e4-a379-1f7e38b42c06)

The first two fields accept values for the dada2 end trimming function (dada2 parameter trimLeft). This function trims from the left and each of the two values here will trim the forward or reverse read respectively (For more information see <a href="https://benjjneb.github.io/dada2/ITS_workflow.html" target="_blank">here</a>). When pattern trimming this function is not utilized. **The default for these values is 0 (and therefore this function is not used) and these are the values that will be used in this tutorial.** 

The third field accepts a value for the dada2 maximum number of expected errors. length trimming function where it identifies the maximum expected errors value (dada2 maxEE parameter and for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>). **For the purposes of this tutorial we will use the default value of 2**

The next fillable fields provide data on quality trimming and length trimming. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/7d0b609e-29d4-4dd8-b472-6ce7580992e2)

The truncation value field (dada2 parameter truncQ for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>) provides information on the quality where the sequences are trimmed. **The default for this value is 2 and this is what will be used for this tutorial.**

The final two filable fields in this section are the truncLenValueF and the truncLenValueR values. These values trim the nucleotide sequences based on the overall expected length of the sequences (for more information see <a href="https://benjjneb.github.io/dada2/tutorial.html" target="_blank">here</a>). When pattern trimming this function is not used and so these values are set to 0 by default and **we will use 0 for the tutorial.**

([Back to Top](#table-of-contents))

## Dada Learn Errors
This section provides information on the error assessment and quality filtering of sequences using the dada2 learnErrors() function. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/72d904bb-12ec-4736-8d8d-f8fcdc7e8280)

The first fillable field in the Dada learnErrors section indicates the percent of data to use when assessing errors. The dada2 analysis generally evaluates the total number of reads present in a submitted run (a run is a group of results processed at the same time on the same machine representing the same molecular methods) and then takes a percentage of the sequences and estimates the error present. The DBTCShiny uses the same learnErrors() function, but instead forces the function to use a subset of the data by fastq file wholistically. In doing this we can provide the files used for the error estimation and the process is reproducible. The first fillable section here identifies the percentage of fastq files used for the analysis. The default is 0.1 or 10%, but in cases where there are very few results files and when the percent of the files is three or fewer, the minimum number of files is automatically set to three. **For our tutorial we will use the default value of 0.1.**

The second fillable field is for the upper value of the total number of nucleotides used for the error assessment. This value is set very high to allow the selection of files for the error assessment. Lowering this value will cause the assessment of the errors to potentially use only a subset of the selected files. **The default of value for this field is set to 1,000,000,000 and this is the value we will used for this tutorial.**

([Back to Top](#table-of-contents))

## Dada Merge Pairs
This section includes fields necessary for the merging of pairs. Please note that some elements of this section will disappear if the unidirectional data analysis is selected (in the Gerneal Information section).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/e6a66d64-f65b-4205-9276-018d649a3b3f)

The first fillable field in the Dada mergePairs section provides a value for the maximum number of mismatches allowed in the merge analysis. The mismatches represents the number of acceptable mismatches in the overlapping sections between merged forward and reverse reads. The smaller this number the more constrained the analysis and the more potential merged pairs will be rejected. As the overlapping region increases, the corresponding increase in the acceptable number of mismatches may result in more reads at the end of the analysis. If this value is lower it will also provide higher confidence in the reulting reads. **The default value is set to 2 and this is the value that will be used in this tutorial.**

The second field in this section is the minimum total number of overlapping nucleotides required to merge forward and reverse pairs. With increasing overlap this value can also be increased or if kept at the default of 12 as the overlap increases the stringency of the merge will also increase. **The default value is set to 12 and this is the value that will be used for this tutorial.**

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/1cc4789f-441c-4279-a4a7-f9f0a057421d)

The third field is the trim merged reads field. If set to TRUE, this field will trim the merged read to only include data with two nucleotides, one in the forward and one on the reverse read. If set to FALSE then the merged pairs will include longer tails on the ends of the merged sequences in both the forward and reverse directions. **The default value if FALSE and will be used for the tutorial.**

The final field provides an input value for the final total length of the reads coming out of the analysis. This value is set as a default value of 100 and can be adjusted depending on the expected length of the reads based on the molecular primers. **The tutorial will use the default value of 100.**

At the bottom of this section there is a button labeled 'Dada Submit'. Clicking this button will initiate the running of the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) function.

([Back to Top](#table-of-contents))

## Dada Implement Function Output

### Quality Plot Folders
The output from the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) function can include up to four file folders in each of the Run folders submitted. In our example we are missing the output folder 'A_Qual' and 'C_FiltQual' as we selected FALSE for the [print quality plots](https://github.com/rgyoung6/DBTCShinyTutorial?tab=readme-ov-file#print-quality-plots) option. If this was set to TRUE then there would be two additional folders with plots displaying the quality metrics of each of the raw (in folder 'A_Qual') and filtered and trimmed (in folder 'C_FiltQual') results.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/ce957e29-a39c-4f2e-8fa3-69626a6fd626)

### Filtered and Filtered and Trimmed Files

The 'B_Filt' folder contains the filtered files and trimmed results (in the 'Primer_Trim' subfolder) in fastq compressed files. These are provided as they represent the the raw output from the analysis and could be evaluated using other programs outside of the DBTCShiny pipeline.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/5fd2fd33-9e60-4077-bcca-20d3ae1fc388)

### Main Output Files

There are five main types of files as output from the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement):
  
![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/6ad16b63-6ce2-4af4-aa5c-b46e78870dbc)

**dadaSummary** 

A text based summary of all of the settings used to process the fastq files in this instance of [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement)

**dadaSummaryTable**

The dadaSummaryTable contains descriptive data about all of the fastq files processed from the [run](#runs) where the output files are located.

**Dada Summary Table headers**
- inputReads
- fwdInputQualMin
- fwdInputQualMedian
- fwdInputQualMean
- fwdInputQualMax
- revInputQualMin
- revInputQualMedian
- revInputQualMean
- revInputQualMax
- filteredReads
- fwdDenoisedReads
- fwdDenoisedAvgQual
- fwdDenoisedAvgLen
- fwdDenoisedUniqueReads
- revDenoisedReads
- revDenoisedAvgQual
- revDenoisedAvgLen
- revDenoisedUniqueReads
- mergedReads
- mergedReadsAvgLen
- noChimReads
- noChimTrimReadsOverMinLen
- noChimTrimUniqueReadsOverMinLen
- noChimTrimAvgLenOverMinLen
- finalCleanedPerReads

**Error Assessment Visualizations** 

The Dada analysis will assess the fastq files for likely instances of nucleotide errors. These data are represented visually in the 'ErrorForward' and 'ErrorReverse' pdf files. The intrepretation of these data are well covered [here](https://benjjneb.github.io/dada2/tutorial.html).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/c23fd216-b091-4871-826e-6bb09e08711b)

**Run ASV Tables and Paired Fasta Files**

The main output from the dada analysis are the ([ASV](https://en.wikipedia.org/wiki/Amplicon_sequence_variant). files and their associated [Fasta](https://en.wikipedia.org/wiki/FASTA_format) files. There are three potential paired ASV-fasta files, Merged, Forward, and Reverse. As we only selected to run the [Merged](#directional-processing) analysis, **only the merged files are present for this tutorial example**.

The format of the ASV files are tables which, at their most basic, include the sequence reads obtained through the analysis and the associated number of those reads for each sample analysed. For the DBTCShiny output, there are several other data that are included in the ASV output tables.
- UniqueID - A unique identifier assigned to the read
- Length - the length of the read in number of nucleotides
- Results - The analysis where the results were obtained (this tutorial example will indicated Merged).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/804b95c4-b71c-475b-ae67-f8d1a49c934f)

**TotalTable**

The total table provides the same format as the paired ASV and fasta <a id="asvfasta"></a> main output files. However, all of the data are combined and retained in this output file. Resluts in this file could include Merged, Forward, Reverse, and ChimRemoved qualifiers in the Results column. The Merged, Forward, and Reverse indicate from which analysis the reads were obtained. The ChimRemoved identifier indicates that the dada analysis did not place the associated read in the paired ASV-fasta files as they were assessed as a [chimera](https://en.wikipedia.org/wiki/Chimera_(molecular_biology)) sequencing error.

## Next Step Data Files

Most of the files generated by the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) function exist to help researchers evaluate the quality of the samples and the efficacy in how they were analysed. In addition, the output files provide all of the information necessary for reproducibility. Once these two elements are satisified, the only files needed for further analysis using the DBTCShiny analysis pipeline are the paired ASV-fasta files. The downloaded examples provided for this tutorial have already moved the Merged ASV-fasta files to the appropriate location for the next steps. However, if you are using your own data it is best to take the paired ASV-fasta files and place them in a new folder to continue your analysis.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/04d689e8-785d-4534-ab0f-0bd7d38199fe)

([Back to Top](#table-of-contents))

# Combine Dada Output

The output from the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) function includes the [paired ASV-fasta](#asvfasta) data files. Where more than one sequence [run](#runs) has been completed for a given project, it is often helpful or needed to combine the data from the different runs into a single data file for further analysis (Note: the files being combined should be from the same molecular protocols). Only the ASV files are required for this combine output function. For the purposes of **this tutorial we are using the 'Run1_Merge.tsv' and the 'Run2_Merge.tsv' files** from the [dada_implement()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#dada-implement) output. To select these files simply click the 'Select a File in the Target Folder' button and then select file dialog window will open. Navigate to the B-CombineDada folder and then select one of the files in this folder and the function will process all '_Merge.tsv',  '_MergeFwdRev.tsv', and '_Forward.tsv' files into paired ASV-fasta files.

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/b4b331c4-0fed-4e63-99cf-e82b9e304d85)

The [combine_dada_output()](https://github.com/rgyoung6/DBTCShiny/blob/main/README.md#combine-dada-output) function produces three output files. The paired ASV-fasta files and a summary file containing information on the implementation of the function (see the image below of the output files for this tutorial).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/e9b7c9fb-27f8-4d03-927c-8ba3c061882b)

([Back to Top](#table-of-contents))

# Make a BLAST Database

There are no data files from previous DBTCShiny elements that lead into this function. Instead, an externally created fasta file is necessary to establish a custom BLASTable database. This file can be manually created or can be more effectively and efficiently created through the use of the [MACER](https://github.com/rgyoung6/MACER) package. In either instance the final format must be in the MACER format (see below).

- The MACER fasta header format - ```>UniqueID|OtherInformation|Genus|species|OtherInformation|Marker```

The [make_BLAST_DB()](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#make-blast-db) shiny page has five elements. The first element is the 'Fasta File' selection button. Once clicked this button will again bring up a [Finder](https://en.wikipedia.org/wiki/Finder_(software)) window.  

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/08dabbcc-695e-4eb9-92ef-28bb0c86aded)

Navigate to the tutorial folder 'C-MakeDB'. For this tutorial there is a single fasta file example (2024_01_05_COI-5P_393_Species_of_Concern.fas) and three folders each containing the program file for the [BLAST+](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.15.0/) makeblastdb program for different platforms. **For this tutorial example select the 2024_01_05_COI-5P_393_Species_of_Concern.fas file.**

Note: The species of concern sequences fasta file contains sequence data for the COI-5P molecular region for all insect taxa on the [CFIA list of invasive and regulated pests](https://inspection.canada.ca/plant-health/invasive-species/regulated-pests/eng/1363317115207/1363317187811). These data were obtained from the [Barcode of Life Datasystems](https://www.boldsystems.org/index.php) and [NCBI GenBank](https://www.ncbi.nlm.nih.gov/genbank/) databases using [MACER](https://github.com/rgyoung6/MACER).

The next element is the 'Make BLAST Location' button. If this button is not selected, or if the file selection is cancelled the program will still run and will assume that the [BLAST+](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.15.0/) makeblastdb program is installed and accessible from all folders on your computer (The program has been added to the computers [path](https://en.wikipedia.org/wiki/PATH_(variable))). If the program is not in the computers path then the appropriate makeblastdb program file needs to be selected for the computers operating system (See image of files below).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/104e5610-dc4b-4bf3-bfc6-60b1370d4acc)

For ease, this tutorial includes a version of the program for three platforms (NOTE: these programs are not updated regularly and may not be the most recent versions). To utilize these program files select them in the [Finder](https://en.wikipedia.org/wiki/Finder_(software)) window, but remember to ensure the program files have [permissions](#Permissions)

The 'Select the NCBI Taxon Database File' button for this function will open a third [Finder](https://en.wikipedia.org/wiki/Finder_(software)) window where you will need to select the 'accessionTaxa.sql' data file ([See the accessionTaxa instructions](https://github.com/rgyoung6/DBTCShiny?tab=readme-ov-file#create-a-local-ncbi-taxonomy-database-to-assign-taxonomic-identifications-to-blast-results).

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/353fd132-891d-4ca3-b221-97f22c729ba7)

There are then two fillable fields. 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/3da9d910-7bab-4aad-9a26-325f956a1866)

The first accepts a numeric value indicating the minimum length of the sequences in nucleotides to include in the constructed database. 

The second fillable field accepts alpha numeric values. This field should be filled with a short (2 to 10 character) string to designate the database you are going to construct. 

Finally using the 'Create BLAST Database Submit' button will initiate the program. 

([Back to Top](#table-of-contents))

# BLAST Sequences

([Back to Top](#table-of-contents))


# Taxon Assign

([Back to Top](#table-of-contents))


# Combine Taxa Assign

([Back to Top](#table-of-contents))



# Reduce Taxa Assign

([Back to Top](#table-of-contents))


# Combine Reduced Taxon Assign

([Back to Top](#table-of-contents))

# Mapping Dashboard

([Back to Top](#table-of-contents))


# References 
Young, R. G., Milián‐García, Y., Yu, J., Bullas‐Appleton, E., & Hanner, R. H. (2021). Biosurveillance for invasive insect pest species using an environmental DNA metabarcoding approach and a high salt trap collection fluid. Ecology and Evolution, 11(4), 1558-1569.

([Back to Top](#table-of-contents))
