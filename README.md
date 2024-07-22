IonSPA
Repository space for development of IONSPA programs with Jim Prell and research group at University of Oregon

This repository is intended for shared development of the IonSPA programs. This will allow for public release along with continued development.

IonSPA is a collection of programs to model ion internal energy and fragmentation (unfolding) of large ions as they pass through a collision cell.

The programs (except the GUI) have been tested on both Windows and Linux platforms. In general, they should be fully cross-platform, though installation details can be expected to vary.

Full details will appear in forthcoming papers.

Currently active programs:
Primary command line programs:
    TBD

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
Most of these (all eventually?) can be run with a -h or --help option to display the command line options for that program. Some of the files in the ionspa directory can also be run (for testing) using the commend format "python -m ionspa.spa_heat" as an example.

Most will use a json format file as an input file to specify the cell parameters, ion parameters, experimental voltages or experimental data, and other run parameters. Use of the spa_paramd module helps to standardize use of the input file. An example of input files for a simple collision cell and the Shiga toxin ion are found in the IonSPA_1.0_inputs folder.

Installation
Uses Python 3 with numpy, pandas, mpi4py and a few other packages A few python distributions exist that supply most of these for scientific python work including Anaconda, Enthought and others. A standard Python download from python.com will also work. The following sequence (on Windows) will produce a virtual environment with the minimum set of modules to run all the IonSPA programs:

python -m venv myenv

myenv\Scripts\activate

python -m pip install numpy matplotlib scipy pandas wxpython wxmplot mpi4py

When done in this virtual environment, the deactivate command will return to any system Python that was installed. Running without the virtual environment is also possible. The requirements.txt file can be used as:

python -m pip install -r requirements.txt

but the requirements.txt is probably too specific to my install in May 2024, so the generic install above is preferred.
In addition, the spa_mpirun program needs a version of MPI installed. The Microsoft version of this can be found here: https://learn.microsoft.com/en-us/message-passing-interface/microsoft-mpi

The program package itself can be downloaded from this GitHub site, either by using the git system to clone the repository to a local copy or simply by downloading the zip file (from the Code button).

Usage
Typically, a series of runs will use commands similar to the following:

run the spa_avg GUI
python spa_avg_wxg.py

This opens a GUI to use spa_avg to model a simple collision cell and plot results.

Generally, most of the programs (others not described here) will accept a --db option to specify a database file name other than the default spa_db.sqlite3. As these files can sometimes be quite large (>1GB), it is sometimes advisable to group sets of runs with the same ion or cell into their own file for easier management.
