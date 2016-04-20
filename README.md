# ShinyQC
Automated Proteomics quality control package

**The zip file containing the R-scripts is currently password protected, until the package is published**

## Table of Contents
[Package description](#head1)

[Prerequisites](#head2)

[Initialize folder structure and parameter files](#head3)

[Setting up MaxQuant for each SampleType](#head4)

[Analyze first raw file for each SampleType](#head5)

[Setting up individual parameter files for each SampleType] (#head6)

[Starting the Shiny app](#head7)


## <a name="head1"></a>Package description


This package allows the automatic analysis and visualization of proteomics standard samples. ID rates as well as certain parameter can be monitored over time for multiple LC-MS platforms in an interactive web application. In addition, a logbook function is implemented, that allows to track hardware on LC-MS systems like column exchanges or calibrations, which can also be visualized in plots. The software supports any kind of peptide standard and is highly flexible.

It consists of three R-scripts: 

1.  QC-setup.R: Setup scripts that creates the folders and files for the different workflows

2.	AutoQC.R: Automatic MaxQuant analysis of MS raw files

3.	runAutoQCShiny.R: Interactive web application (Shiny app), to visualize data


## <a name="head2"></a>Prerequisites


*  The hosting computer needs to have a running version of [MaxQuant](http://www.coxdocs.org/doku.php?id=maxquant:common:download_and_installation) (version number should not matter).

*	[R](https://cran.r-project.org/bin/windows/base/) needs to be installed (package was developed for version 3.2.4).

*	[R-Studio](https://www.rstudio.com/products/rstudio/download/) needs to be installed.

*	Following naming conventions for your raw files must be matched:

    +	MS raw files must **start with a unique identifier for the instrument**. Identifiers for different instruments must be of **same length**. If a lab has multiple LC-MS platforms, identifiers could be named like this:

        ```
        Orbi1_*.raw
        Orbi2_*.raw
        etc
        ```

    + MS raw files must have an **unique identifier for the sample type**. This can be placed anywhere in the raw file name. e.g.:

        ```
        Orbi1_*_BSA_*.raw.
        Orbi1_*_HeLaSTD_*.raw
        etc
        ```
    + **Spaces and unconventional special character must be avoided in file names and folders used by the software (Including MaxQuant path).**

*	Client computers, from which data can be interactivally und simultaneously inspected need to be connected in the same local area network.


## <a name="head3"></a>Initialize folder structure and parameter files

1.  Create an empty folder that will store all data created by the software on a local hard-drive. This folder will henceforth be referred to as "DataFolder"
    ```
    D:/QC-software/
    ```
  
2.	Create subfolder for every sample type that should be analyzed by the software. The folder name is the identifier for sorting raw files in the correct subfoder and must match exactly to a unique identifier Tag in the .raw file name (see Prerequisites: raw file naming convention). These subfolders will henceforth be referred to as “SampleType folders”.
      
    ```
    Orbi_*_BSA_*.raw
    ```
    needs a sample type folder named:
    ```
    D:/QC-software/BSA
    ```


3.	Unpack the zip folder in the DataFolder

4.	Run the “QC-setup.R” script, which copies all needed files in all created subfolders.

5.	Configure the instrument.txt file in the DataFolder according to your instruments. The Abbreviation must match exactly to a unique identifier Tag in the .raw file name, which must be located at the beginning of the raw file. The full instrument name will be the one shown in plots.

      *Make sure to keep format as .txt and don’t change the file name.*

6.	Configure the logBookChoices.txt file in the DataFolder according to your lab setup. Entry types and user names configured here can be selected for logbook entries in the software.

      *Make sure to keep format as .txt and don’t change the file name.*


## <a name="head4"></a> Setting up MaxQuant for each SampleType

1.  Go to the raw file type-specific sub-folder (e.g.“D:/QC-software/BSA”). It contains a dummy.raw file, an PeptidesOfInterest.txt file and an “borders.txt” file which need to be configured.

2.	Create an MaxQuant parameter file by starting MaxQuant and follow the upcoming steps:

    +	Load the “Dummy.raw” file from the workflow type folder.
    
    +	Configure the “Group-specific parameters” and “Global parameters” according to your Raw file type.
    
    +	Save the changed parameter file by the “Save parameters” button using default location and name.
    
    +	Close MaxQuant.
    
    ***Important: If you work with a low complex standard like BSA, which contains standard proteins, you should uncheck the "Include contaminats" options in MaxQuant (located in "Global parameters/Sequences")***
    

## <a name="head5"></a> Analyze first raw file for each SampleType

1. Drag and drop a one raw file for each SampleType in the DataFolder.

2. Open the AutoQC.R script in RStudio and run the code by hitting the "Run App" button.

3. Specify the DataFolder in the pop-up window.

4. Track the raw file processing in the R-Studio console and wait until it's finished. The console should print the following statements:

    ```
    Found file Orbi1_160130_BSA_01.raw
    Start maxquant analysis of file Orbi1_160130_BSA_01
    Finished MQ search file Orbi1_160130_BSA_01
    Finished writing QC-pdf for Orbi1_160130_BSA_01
    No more raw files to process; check again in 2 min
    ```
    
    ***If any problem occurs during MaxQuant search, try a regular MaxQuant search without the script but same settings. If that worked out fine, change back to the dummy.raw file in the mqpar.xml file and save.***
    

## <a name="head6"></a>Setting up individual parameter files for each SampleType

The SampleType folders contain two files which need to be configured, the "PeptidesOfInterest.txt" and the "borders.txt".

1.  PeptidesOfInterest.txt:

    +	The PeptidesOfInterest.txt file contains peptides that can be manually controlled over all analyzed runs to check e.g. retention time, score or intensity of these peptides.
    
    +	To specify these peptides copy the Modified sequence and Charge from the evidence.txt file in PeptidesOfInterest.txt file.
    
    +   evidence.txt files can be found after a first MaxQuant analysis of a raw file in the folder:
    
    ```
    D:/QC-software/BSA/Orbi1_160130_BSA_01
    ```
    
    +	Make sure to choose peptides that are well suited for your quality control (e.g. avoid peptides bearing Methionine which can be oxidized and therefore vary in intensity, choose peptides occurring in only one charge state, choose peptides covering a large area of your gradient,…).
    
    +	Make sure to keep format as .txt and don’t change the file name.

2.	borders.txt

    +	The borders.txt file shows all column names of the MaxQuant evidence.txt file as rows.
    
    +	If the “Include” column shows “TRUE” this data type can be plotted in the software.
    
    +	The three value columns (“High”, “Low” and “Optimal”) are optional values. If specified these values will be shown in the plot as vertical line in red (“Low” and “High”) or green (“optimal line”).
    
    +	As these values can differ between different instrument types the according instrument needs to be specified in the “Instrument” column.
    
    +	Make sure to keep format as .txt and don’t change the file name.


## <a name="head7"></a>Starting the Shiny app

1. Open another instance of R-Studio and open the runAutoQCShiny.R.

2. Run the app and specify the the SampleType folder, which should be inspected, in the first pop-up window.

    ```
    D:/QC-software/BSA
    ```
    
3. Specify the MaxQuant/bin folder in the second pop-up window.

    ```
    D:/MaxQuant_1.5.3.8/MaxQuant/bin
    ```
    
4. Interactively inspect your data and create logbook entries for the instruments.

5. Connect the the software from other computer in the same LAN by calling the IP adress of the hosting pc and the port 3168 in browser.

    ```
    //192.168.0.1:3168
    ```
    
6. Update the data with newly analyzed raw files by hitting the "update app button" from host or client computers.

