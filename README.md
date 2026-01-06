# IonSPA

Repository space for development of IONSPA programs with Jim Prell and research group at University of Oregon

The programs (except the GUI) have been tested on both Windows and Linux platforms. In general, they should be fully cross-platform, though installation details can be expected
to vary.

This repository is intended for shared development of the IonSPA programs.  This will allow for public release along
with continued development.

IonSPA is a collection of programs to model ion internal energy and fragmentation (unfolding) of large ions as they pass through
a collision cell. Collision cell models have been implemented to model both Waters and Agilent cells, though other cell models can
be added in future.  The collision model is done with a Monte Carlo technique with individual ion trajectories and collisions. 

Further, with spa_db_fit, the program can obtain parameters for change in enthalpy and entropy that capture the experimental dissociation behavior for the ion. This can be used
to predict expected behavior on other types of cells with different collision gases or pressures.

The programs (except the GUI) have been tested on both Windows and Linux platforms. In general, they should be fully cross-platform, though installation details can be expected
to vary.

Full details will appear in forthcoming papers.

## Currently active programs:
    Primary command line programs:
        spa_mpirun.py        - the main program to run large sets of ion trajectories at multiple input energies (initial delta V)
        spa_db_fit.py        - the main program for fitting the model fragmentation to experimental values to estimate dH, dS
        spa_db_listruns.py   - an auxillary program to list run data in one of the database files from the program runs
        spa_db_plot.py       - an auxillary program to plot data from the database files
        spa_db_predict.py    - an auxillary program to plot modeled fragmentation curves with specified dH, dS
        spa_db_refrag.py     - a developmental program to recompute fragmentation histories of ions from the database trajectories
        spa_avg.py           - a separate program to compute internal ion heating bypassing individual trajectories
        
    GUI program for spa_avg:
        spa_avg_wxg.py       - runs the spa_avg program in a GUI window, allowing overlay of several plots
        
    Utility/support code used by multiple modules in the ionspa directory:
        spa_paramd.py    - provides support for reading the input file describing cell, ion and program settings
        spa_const.py     - supplies physical constant values
        spa_heat.py          - the core of the IonSPA calculation. Models single ion trajectories through a cell
        cell.py          - the basic cell definition along with some variations
        cell_A_CIU.py    - an extra cell definition to model the Agilent CIU cell
        hcprofiles.py    - contains heat capacity data for a number of ion types
        hcprofiles.json  - old format heat capacity data
        hcprofiles2.json - new format heat capacity data

Most of these (all eventually?) can be run with a -h or --help option to display
the command line options for that program. Some of the files in the ionspa directory
can also be run (for testing) using the commend format "python -m ionspa.spa_heat"
as an example.

Most will use a json format file as an input file to specify the cell parameters,
ion parameters, experimental voltages or experimental data, and other run
parameters. Use of the spa_paramd module helps to standardize use of the input file.
An example of input files for a simple collision cell and the Shiga toxin ion are
found in the IonSPA_1.0_inputs folder.

## Installation
Uses Python 3 with numpy, pandas, mpi4py and a few other packages
A few python distributions exist that supply most of these for scientific python
work including Anaconda, Enthought and others.  A standard Python download from python.com will also work. The following sequence (on Windows) will produce a virtual environment with the minimum set of modules to run all the IonSPA programs:

`python -m venv myenv`

`myenv\Scripts\activate`

`python -m pip install numpy matplotlib scipy pandas wxpython wxmplot mpi4py`

When done in this virtual environment, the deactivate command will return to any
system Python that was installed.  Running without the virtual environment is also
possible.  The requirements.txt file can be used as:

`python -m pip install -r requirements.txt`

- but the requirements.txt is probably too specific to my install in May 2024, so the generic install above is preferred.

In addition, the spa_mpirun program needs a version of MPI installed. The Microsoft
version of this can be found here:
https://learn.microsoft.com/en-us/message-passing-interface/microsoft-mpi
 or search for microsoft MPI

The program package itself can be downloaded from this GitHub site, either by using the 
git system to clone the repository to a local copy or simply by downloading the zip file
(from the Code button).

## Usage
Typically, a series of runs will use commands similar to the following:

### run the model
`mpiexec -np 5 python spa_mpirun.py --db spa_dbtest exp_fit_CC622_1xt_b.json`

This uses the system MPI system to run multiple processes to calculate ion trajectories
and temperatures, storing the results in the database file

### list runs in the database
`python spa_db_listruns --db spa_dbtest`

This creates a summary output of the database contents. Note that sqlite3 can be installed
and used to examine and extract information from the database as well.

### plot time series data from a run
`python spa_db_plot exp_fit_CC622_1xt_b.json --db spa_dbtest --vin 10 --vname u`

This will plot data from the specified runs in the database.  Additional curves are 
added to illustrate the spa_avg results.

### show plot of predicted fraction remaining with alternate dH, dS values
`python spa_db_predict exp_fit_CC622_1xt_b.json --db spa_dbtest --dH 75 --dS -95

This shows a predicted ion fraction remaining graph given specified values for dH and dS.

### do the fit of the model to experimental data
`python spa_db_fit.py --db spa_dbtest.sqlite3 exp_fit_CC622_1xt_b.json --newfrac --weighting combined --outfile CC622.txt --plotfile CC622.png --mapfile CC622 map.png`

This is the workhorse program to adjust values for dH and dS to obtain the best fit to the 
experimental data. Optional plot and file outputs can capture the fit results.


### run the spa_avg GUI
`python spa_avg_qt.py`

This opens a GUI to use spa_avg to model a simple collision cell and plot results.
An optional parameter can be used to initialize the parameters from an input file.
See reference below.

Generally, most of the programs (others not described here) will accept a --db option to specify a database file name other than the default spa_db.sqlite3. As these files can sometimes be quite large (>1GB), it is sometimes advisable to group sets of runs with the same ion or cell into their own file for easier management. 

## Example usage
`mpiexec -n 5 python spa_mpirun.py IonSPA_1.0_inputs\cell_Stx_13.txt`

`python spa_db_fit.py IonSPA_1.0_inputs\cell_Stx_13.txt`

This results in a fit plot display and key output lines:

infile, dH, dS, dG, R2, Tfit, major3sd, major10sd, minor3sd, minor10sd

['IonSPA_1.0_inputs\\cell_Stx_13.txt'] , 92.03, -0.20, 92.13, 9.78e-05, 495.91, 20.95, 44.44, 0.52, 1.10

The value for dH is 92.03 and dS is -0.20
Other values are related to the fit uncertainty for dH and dS (illustrated if the option
for --mapfile screen is included)

Also see files
    run_agilent_myoglobin.bat
    run_synapt_shigatoxin.bat
    and example input files in folders agilent_example_inputs and synapt_example_inputs


## Bibliography

1. James S. Prell, James S. "Modeling collisional kinetic energy damping, heating, and cooling of ions in mass spectrometers: A tutorial perspective", International Journal of Mass Spectrometry, 504 (2024), 117290. https://doi.org/10.1016/j.ijms.2024.117290


2. Shepherd, S.O.; Green, A.W.; Wylie, E.P.; Newton, K.R.; Kurulugama, R.T.; Prell, J.S. Determination of Thermochemical Barriers in Gas-Phase Multiple-Collision Induced Dissociation Experiments on Gas-Phase Protein Complexes, J. Am. Chem. Soc. 2025, 147, 46854-46870. https://dx.doi.org/10.1021/jacs.5c07327


3. Paris, L.R.; Green, A.W.; Prell, J.S. Computed Vibrational Heat Capacities for Gas-Phase Biomolecular Ions, J. Am. Soc. Mass Spectrom., 2025, 36, 862-872. https://dx.doi.org/10.1021/jasms.5c00008
