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

Finally, the Shiny applicaiton uses buttons, for example the Dada function 'Data Location'.  Once clicked these buttons will do one of two things. 

  1. The buttons will bring up a dialog window (referred to as an 'Open' dialog in iOS or an 'Open File' dialog in Windows and a 'File Picker' dialog in Linux). These dialog windows are sometimes opened behind the RShiny DBTCShiny dashboard window. Clicking the R source (R terminal window or Posit/R Studio application) will bring up the dialog window for use. 
  2. In some cases (such as launching DBTCShiny using a terminal window in iOS or Linux or running the R exeuctable in Windows) the file selection will appear in the terminal or exeuctable window where DBTCShiny was initially launched. This input will beed to be entered by typing the path manually.

To avoid the second situation it is best to run the DBTCShiny through [Posit](https://posit.co/download/rstudio-desktop/).

All descriptions and comments below relate to the DBTCShiny application and the graphical user interface to implement the functions.

([Back to Top](#table-of-contents))

# Dada Implement

![image](https://github.com/rgyoung6/DBTCShinyTutorial/assets/60077841/9464aa34-f19b-4380-a3de-3d801b7dad24)




([Back to Top](#table-of-contents))

# References 
Young, R. G., Milián‐García, Y., Yu, J., Bullas‐Appleton, E., & Hanner, R. H. (2021). Biosurveillance for invasive insect pest species using an environmental DNA metabarcoding approach and a high salt trap collection fluid. Ecology and Evolution, 11(4), 1558-1569.

([Back to Top](#table-of-contents))
