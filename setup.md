---
title: Setup
---

__Workshop data files__  
Prior to the workshop, you must download the example datafiles and test importing them into R. To do this:

1) Download the example datasets from [here](https://cloudstor.aarnet.edu.au/plus/s/VlOGpIJnaFPl1qd/download).   
2) Save the downloaded file to Downloads, Desktop or another convenient location on your computer, and double-click to expand it - it will create a directory called 'data'.  
3) Open RStudio and set your working directory to **the same location where you saved the downloaded file** - not the data directory itself. The easiest way to do this is click on the Files tab (normally in the bottom right pane) and navigate to the folder that way, then use the function "Set as working directory" from the More (cog icon) menu just at the top of the file window.  
4) Read in the workshop data by typing the command `gallstones <- read.csv("gallstones.csv")` in the RStudio console pane (usually bottom left). This should complete without any warnings. If you get a message starting 'Error in file(file, "rt") : cannot open the connection' you have not set your working directory to the correct location - recheck where you saved and extracted the workshop datafile and repeat step 2.


__Install Packages__
The following packages are required during the workshop and must be installed beforehand:
* ggplot2
* gmodels
* PMCMRplus

To install packages, use the command `install.packages("PACKAGE_NAME")` (package name is case sensitive). To install all three packages in a single command run `install.packages(c("ggplot2", "gmodels", "PMCMRplus"))`.  
If you are prompted to build packages from source, select "No".
