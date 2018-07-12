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
If you look in the .pdb files of the host and guest, you will see that the cyclodextrin is residues 
1 to 147 and the ligand is 20 residues (therefore 148 to 168).

In the "Active Sites" block, we need to define the binding site and the vector along which to place
the milestones.
The `r` setting specivies the milestone spacing in Angstroms, **set this to 1.5**
the `r_low` value corresponds to the starting milesone radius in angstroms-- **set this to 1.5 also**
the `x,y,z` values correspond to the coordinates of our bound state. To get these we will need to use 
VMD.

from the tutorial directory, execute `VMD inputs bcd_q4md_holo_wet.pdb`. this will load the cyclodextrin 
structure for us.

open the tkconsole `
 



 
