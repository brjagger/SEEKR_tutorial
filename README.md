# Simulation Enables Estimation of Kinetic Rates (SEEKR): a Multiscale Simulation Approach for Calculating Binding Kinetics

In this tutorial, you will learn the basics of setting up a SEEKR simulation to calculate the binding kinetics of 
a model host-guest system, beta-cyclodextrin with aspirin. 

You will:
1. Prepare the SEEKR input file
2. Run SEEKR to prepare the milestone systems
3. Visualize simulation trajectories 
4. Construct a milestoning model and calculate rates
5. Perform convergence analysis

## Obtaining the latest version of SEEKR

The latest version of SEEKR can be cloned or downloaded through the NBCR github: https://github.com/nbcrrolls/SEEKR

Additional information about SEEKR can be found in ```SEEKR/doc/manual.txt```.

## SEEKR Requirements

To run a complete SEEKR calculation, you will need the following installed:
* VMD for visualization http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD
* NAMD molecular dynamics package http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=NAMD 
* BrownDye for BD simulations https://browndye.ucsd.edu/
* LEaP when using the AMBER forcefield http://ambermd.org/GetAmber.php
* APBS for electrostatics calculations http://apbs-pdb2pqr.readthedocs.io/en/latest/downloads.html
* Python version 2 (2.7 or later) with the following packages (I recommend using Anaconda):
   - numpy
   - scipy
   - MDAnalysis
   - matplotlib
   
 ## SEEKR Tutorials
 
This repository contains two tutorails:
 
The abbreviated tutorial ```abbrev_tutorial.md``` focuses entirely on visualizing and analyzing the results of a SEEKR calculation and does not require the installation of many of the packages used for system preparation. 

The complete tutorial ```complete_tutorial.md``` covers system preparation, generation of simulation files, running simulaitons, and analysis. This requires the installation of all packages (specifically LEaP and APBS for system prep).


