# Simulation Enables Estimation of Kinetic Rates (SEEKR): a multiscale simulation approach for calculating binding kinetics

In this tutorial, you will learn the basics of setting up a SEEKR simulation to calculate the binding kinetics of 
a model host-guest system, beta-cyclodextrin with aspirin. 

You will:
1. Prepare the SEEKR input file
2. Run SEEKR to prepare the milestone systems
3. Visualize simulation trajectories 
4. Construct a milestoning model and calculate rates
5. Perform convergence analysis


## Preparing the SEEKR input file
In the tutorial directory, you will find the SEEKR input file "bcd_aspirin.seekr".
Open this file with a text editor. You will see that most of the file is already filled out.
We will now fill out the remainder with the appropriate parameters for our system.

make the `rootdir` the appropriate location for your machine (probably /scratch)

In the NAMD TCl block, the `ligrange` corresponds to the atom numbers of the aspirin ligand and
`recrange` corresponds to the atom numbers of the cyclodextrin.



 
