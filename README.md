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

The data for the tutorial comes from the publication Young _et al._ 2021 which used passive collection of insects using Lindgren funnel traps with a high salt solution to survey for invasive insects. The insects were removed from the traps and the salt solution was retained and used to extract environmental DNA (eDNA). The data used in this example are a subset of the data obtained in this work.

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

Finally, the Shiny applicaiton uses [buttons](#buttons), for example the Dada function 'Data Location'.  Once clicked these buttons will do one of two things. 

  1. The buttons will bring up a dialog window (referred to as an 'Open' dialog in iOS or an 'Open File' dialog in Windows and a 'File Picker' dialog in Linux and referred throughout this tutorial as an 'open file dialog window'). These dialog windows are sometimes opened behind the RShiny DBTCShiny dashboard window. Clicking the R source (R terminal window or Posit/R Studio application) will bring up the dialog window for use. 
  2. In some cases (such as launching DBTCShiny using a terminal window in iOS or Linux or running the R exeuctable in Windows) the file selection will appear in the terminal or exeuctable window where DBTCShiny was initially launched. This input will beed to be entered by typing the path manually.

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

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/df23f66c-c9a1-4364-b7ad-fa816fe61b79)

There are several expandable panels on the initial page that can be explored for more information on installation, dependencies, and contact for troubleshooting. 

The side panel of DBTCShiny contains three additional options. The first option, DBTC Tools conatins all of the main high-throughput processing functions of DBTCSHiny and this is the section where we will first start. Once DBTC Tools is clicked it will show a page with the 8 tabs for the main functions of DBTCShiny. The first tab is the Dada tab and this is where our tutorial will begin (See image below). 

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/9464aa34-f19b-4380-a3de-3d801b7dad24)

([Back to Top](#table-of-contents))

# Dada Implement

DBTCShiny uses [dada2](https://benjjneb.github.io/dada2/) to complete the analysis of raw fastq files generated from high-throughput sequencing runs. While this tutorial will cover some of the possible settings possible when implementing dada2, for more details on the specifics of the settings available please read through the [dada2](https://benjjneb.github.io/dada2/) documentation. There are five sections within the DBTCShiny Dada submission. We will reference each section in turn below with respect to runnig our example analyses.

## 1. General Information - There are six options that are available in this section.
   
### Data Location button
Once clicked this button will bring up an open file dialog window to select the data to process ([see exceptions](#buttons)). 

Unidirectional 
There are two options presented with this tutorial. The first is a data set of unidirectional fastq files in the folder SaltTrapUnidirectional. The data in this folder do not contain both forward and reverse paired read fastq data files. This dataset only contains can be utilized to test the utility of the DBTCShiny if you have unidirectional data. If selecting this 


([Back to Top](#table-of-contents))

# References 
Young, R. G., Milián‐García, Y., Yu, J., Bullas‐Appleton, E., & Hanner, R. H. (2021). Biosurveillance for invasive insect pest species using an environmental DNA metabarcoding approach and a high salt trap collection fluid. Ecology and Evolution, 11(4), 1558-1569.

([Back to Top](#table-of-contents))
