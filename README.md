# Simulation Enables Estimation of Kinetic Rates (SEEKR): a Multiscale Simulation Approach for Calculating Binding Kinetics

In this tutorial, you will learn the basics of setting up a SEEKR simulation to calculate the binding kinetics of 
a model host-guest system, beta-cyclodextrin with aspirin. 

You will:
1. Prepare the SEEKR input file
2. Run SEEKR to prepare the milestone systems
3. Visualize simulation trajectories 
4. Construct a milestoning model and calculate rates
5. Perform convergence analysis

## SEEKR Requirements

To run a complete SEEKR calculation, you will need the following installed:
* VMD for visualization http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD
* NAMD molecular dynamics package http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=NAMD 
* BrownDye for BD simulations https://browndye.ucsd.edu/
* LEaP when using the AMBER forcefield http://ambermd.org/GetAmber.php
* APBS for electrostatics calculations http://apbs-pdb2pqr.readthedocs.io/en/latest/downloads.html
* Python version 2 (2.7 or later) with the following packages:
   - numpy
   - scipy
   - MDAnalysis
   - matplotlib
   
   
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
The `r` setting specifies the outermost milestone spacing in Angstroms, **set this to 13.5**
the `r_low` value corresponds to the starting milesone radius in angstroms-- **set this to 1.5 also**
the `x,y,z` values correspond to the coordinates of our bound state. To get these we will need to use 
VMD.

From the tutorial directory, execute `VMD inputs/bcd_q4md_holo_wet.pdb`. This will load the cyclodextrin 
structure for us.

Open the tkconsole `Extensions -> Tk Console` we will use this to get the COM coordinates of our molecule.

In the Tk Console, type ```set site [atomselect top "resname MGO"]``` this will store this selection in te variable `site`
The type ```measure center $site```. the output will be the x, y, z coordinates of the cyclodextrin ceter of mass.

Set thes in the corresponding lines in the SEEKR input file.
Mine was:
`x 0.0275, y -0.0459, z 0.4599,`

Then type in the Tk console ```$site get serial``` and enter this for the atomid filed.

Now we need to choose where to place the ligand on each milestone.

In the tutorial directory, there should be a script called 'moduseful.tcl'. In the tkconsole window, type "source moduseful.tcl". Then run the following 
command:

 ```eye_vec "0.0275, -0.0459, 0.4599"```

 (These are the coordinates for the center of binding site). 

The script should draw a thin line running from the binding site to where your eye was on the screen. Rotate the molecule to see the line. In order to obtain a good result for this vector, you will want to rotate your view in VMD so that you can see directly into the binding pocket. You can use the above command to draw another line, note the values for the vector. Don't worry about the magnitude of this line, SEEKR will automatically normalize it.

Set the 'vx', 'vy', 'vz' values to be the values you obtained.

My values were

`vx -3.503, vy -11.602, vz 82.823`

The parameters 'startvx', 'startvy', and 'startvz' allow more control over how the ligand is arranged along the milestones. This vector points from the origin to the location on the first milestone where to start the (vx, vy, vz) vector. If unsure how to modify this, then make 'startvx', 'startvy', and 'startvz' to be the same as 'vx', 'vy', and 'vz'.

Set the increment to be 1.5, this is the spacing between milestones in Angstroms. 

Alternatively, you can manually specify the placement of milestones using the
`radius list` option and provide a string of distances (e.g. 1 2 3 4 6 8 )

the option looks like this: `radius_list 1 1.5 2 2.5 3 4 6 8 10 12 14`

NOTE: the radius lis option will override the increment option

for our example, using evenly spaced milestones generated with `increment` will be just fine.

I have already filled out the MD parameters block for you to save some time. For our host-guest example, the leap commands are somewhat complicated in order to build the cyclodextrin parameter file, but for typical protein-ligand systems this is more striaghtforward.

After the leap inputs, you should see parameter specificatins for each of the 
simulation phases of the milestoning calculation: 

* Minimization
* Temperature Equilibration
* Ensemble Equilibration
* Reversal and Forward
* Brownian Dynamics 

Again, I have filled these out for the sake of time, but take a look at the parameters for each section.


**Be sure to save your input file!**

You are now ready to run SEEKR, do this by executing the command

```python /path/to/SEEKR/bin/seekr.py bcd_aspirin_q4md.seekr```

The program may take take a few minutes to complete. 
Once finished, you will see the folder 'tryp' in the directory you provided as 
'root_dir'.

The directory contains a filetree that SEEKR constructed. Inside, you will see several folders that begin with the word 'anchor' and a folder that is called 'b_surface'. Additionally, you will see a milestones.xml file, which the program uses to represent the milestone surfaces, and will be used for analysis in the end. There are also several '.pkl' files. The '.pkl' files are python "pickle" files that allow subsequent runs of seekr.py to be completed more quickly and easily, but you will never interact with them.

The directories that begin with "anchor" are designed to be informative. The format looks like: "anchor_A_B_C_D_E_F_G" The numbers correspond to:

A: The index of the milestone (can be positional or rotational)
B: The index of the positional milestone
C: The site ID for multiple binding locations on the receptor
D: The X-coordinate of the anchor (location where the center-of-mass of the ligand was placed)
E: The Y-coordinate of the anchor (location where the center-of-mass of the ligand was placed)
F: The Z-coordinate of the anchor (location where the center-of-mass of the ligand was placed)
G: The index of the rotational milestone. (Until this feature is fully developed, it is likely only to be zero.)

Each anchor directory corresponds to a milestone surface. Since we set the 
'reject_clashes' option to 'True', it is likely that some anchors were not 
created because the ligand had a steric clash with the receptor. This is 
highly dependent on the vector generated by 'eye_vec'. The missing anchors can 
always be added later, but for now we will continue with just those created 
here.

Look inside one of the directories that begins with 'anchor'. In these 
directories, you will see at least one subdirectory "md", and in the outermost 
one you'll see the subdirectory 'bd'. Look inside the "md" subdirectory. You 
will see the directories: "building", "min", "temp_equil", "ens_equil", and 
"fwd_rev" directories. You'll also see a "holo_wet.pdb" structure. Take a look 
at the structure in VMD and verify that it looks OK.

Look inside the "building" directory. Inside will be a LEAP file used to 
prepare a PRMTOP and INPCRD file, as well as LEAP output and a PDB file 
generated by LEAP. The LEAP output file can be useful for debugging problems 
running LEAP in other projects.

Back up into the "md" folder. The other directories will be useful for running 
preparations of this particular milestone for simulation.

Now back up two directories and enter the "b_surface" directory. Inside here 
are a number of files that will be used for BD simulations starting at this 
anchor.

## Running MD and BD Simulations ##

We do not have time to actually run the MD and BD simulations for this tutorial, as they can take hours to days to complete. However, I have provided you with all the data so that we can do some analysis. 

Unzip the file by executing:

```tar -xzf $FILENAME```

This will contain a filetree that should look similar to the one you made with SEEKR.


## Visualizing MD simulation results ##

In the project directory, navigate to anchor_4* and then into the 
"md" directory. From here you can visualize the results of the ensemble equilibration MD simulations by loading 
.prmtop file located in the building directory and any of the .dcd trajectories located in the ens_equil 
directory in VMD.

**Notice how the ligand was restrained at the appropriate distance for the corresponding milestone.

In the fwd_rev directory, you will see some .out files that contain all of the milestone transition events that occured in the simulation. These are pulled from the NAMD output and placed in this file for simplicity.


## Calculating Kinetic Parameters ##

To analyze our milestoning siulations, SEEKR possesses a script called `analyze.py`

This script can:

- calculate k_on and k_off as well as error estimates
- calculate a binding free energy profile
- Perform convergence analysis: 
   * plot calculated rate constants vs. reversal number
   * plot transition counts (in and out) per milestone vs. reversal number 
   * plot transition probability per milestone vs. reversal number
   * plot incubation time per milestone vs. reversal number
   
analze.py requires a few basic inputs (with additional options for the various calculation types):
   
   - a milestones.xml file which contains basic information about each milestone (name, path, milestone distance, etc.)
   - output files from MD and BD simulations to parse for transition events
   - definition of which milestones correspond to the bound state

Execute ``` python analyze.py -h``` for more information about useage as well as additional arguments.

The filetree `bcd_tutorial_aspirin` has all of the data we need to calculate the kinetic rate constants of interest.

Let's first calculate k_on...

Inside the bcd_tutorial_aspirin directory, execute

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0,9 --on -v ```

This calculates the on rate using both milestone 0 and 9 as bound states, aka sink states.

SEEKR will go in to each anchor and extract the transition statistics. Then it will create a transition probability matrix and 
incubation time vector that can be used to calculate the on rate. You will see a lot of output because of the addition of the verbose flag, take some time to inspect this.


The off rate can be calculated in exacytly the same way usting the --off flag: 

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0,9 --off ```

Similarly, we can calculate a binding free energy profile:

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0 --free_energy ```


## SEEEKR Convergence analysis

SEEKR now has the capability to perform basic convergence analysis/estimates.

First lets look at the convergence of the on and off rates.
execute :

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0,9 --on --conv_filename on_conv.txt --conv_stride 100```

This will calculate the on rate every 100 reversals.

We can do the same for the off rate:

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0,9 --off --conv_filename off_conv.txt --conv_stride 100```


We can then plot the data in the outputted text files using somethinng like xmgrace


SEEKR can also provide convergence estimates per milestone.

to do this execute the command 

```python PATH/TO/SEEKR/bin/analyze.py -m milestones.xml -b 0,9 --milestone_conv --conv_stride 100```


 
