CJAM
====

> **AUTHORS**  
Laura L Watkins (MPIA), <lauralwatkins@gmail.com>  
Mark den Brok (Groningen, Utah)

> **PUBLICATIONS**  
[Watkins et al. 2013, MNRAS, accepted; arXiv:1308.4789][watkins2013]  
[den Brok et al. 2013, MNRAS, accepted; arXiv:1311.3303][denbrok2013]

-------------------------------------------------------------------------------

CONTENTS
--------

* license and referencing
* code description
* requirements, installation and compilation
* input files
* output files
* running the example
* notes
* directory structure

-------------------------------------------------------------------------------

LICENSE AND REFERENCING
-----------------------

This code is released under a BSD 2-clause license.

If you use this code for your research, please cite:  
[Watkins et al. 2013, MNRAS accepted "Discrete dynamical models of omega Centauri" | arXiv:1308.4789][watkins2013]

-------------------------------------------------------------------------------

CODE DESCRIPTION
----------------

This code calculates first and second velocity moments using the Jeans Anisotropic MGE (JAM) models of [Cappellari (2008)][cappellari2008] and [Cappellari (2012)][cappellari2012].  We have extended these models to calculate all three (x, y, z) first moments and all six (xx, yy, zz, xy, xz, yz) second moments.  The full calculations are given in [Watkins et al. (2013)][watkins2013].  Our code is written in C, and is an based on [the IDL implementation of the line-of-sight calculations](http://www-astro.physics.ox.ac.uk/~mxc/idl/#jam) by Michele Cappellari.

*jam/jam_axi_vel_mmt.c* calculates the first moments and *jam/jam_axi_rms_mmt.c* calculates the second moments.  *cjam.c* and *cjam_main.c* offer examples of wrapper functions that may be called from the command line to process the inputs and run the moment calculators.  These programs compile to an executable *cjam* which takes the following arguments:

> flmge   : path to luminous MGE  
> nlg     : number of luminous MGE components  
> fmmge   : path to mass MGE  
> nmg     : number of mass MGE components  
> fxy     : path to star positions file  
> fmom    : path to output moments file
> incl    : inclination angle [radians]  
> dist    : distance [kpc]  
> mbh     : black-hole mass [Msun]  
> rbh     : black-hole scale length [arcsec]  
> *beta   : velocity anisotropy (for each of the nlg components)  
> *kappa  : rotation parameter (for each of the nlg components)  
> *ml     : mass-to-light ratio (for each of the nmg components)  

This example allows the luminous MGE and the mass MGE to be different.  It also allows for velocity anisotropy and rotation that change for each luminous MGE component and mass-to-light ratio that changes for each mass MGE component.  The resulting velocity moments are output to a file with the specified file name.  In total 10 + 2*nlg + nmg arguments are required.

-------------------------------------------------------------------------------


REQUIREMENTS, INSTALLATION & COMPILATION
----------------------------------------

The code requires the GNU Scientific Library (GSL) to perform integrations and interpolations: [downloads and installation instructions](http://www.gnu.org/software/gsl/).

The Makefile will be system-specific, however an example Makefile is provided for guidance regarding compilation.  If GSL is not installed in the default directory for your system, you will also need to provide the path to GSL in the Makefile.

To compile the example code:

    $ make cjam

To clean the directory of all .o files:

    $ make clean


-------------------------------------------------------------------------------

INPUT FILES
-----------

The code requires three input files:

1. the parameterisation for the luminous MGE
2. the parameterisation for the potential (mass) MGE
3. the projected positions of the objects in the dataset

The files containing the MGE parameters have 4 columns and M rows (where M is the number of gaussian components in the MGE).  The columns contain:

1. gaussian number
2. central surface brightness (Lsun/pc^2)
3. major axis dispersion (arcsec)
4. projected flattening

The file containing the stellar positions has 2 columns and N rows (where N is the number of objects in the dataset).  The columns contain:

1. projected major axis coordinate (arcsec)
2. projected minor axis coordinate (arcsec)

Comments may be added to these input files using #.

Example files are provided in the *example/* directory.  In the example, the same MGE file is used for both the luminous and potential MGEs.


-------------------------------------------------------------------------------


OUTPUT FILES
------------

The code outputs a single file containing the calculated moments for all input positions.  This file has 9 columns and N rows (where N is the number of stars in the input positions file).  The columns contain:

1. x' component of the velocity first moment (v_x')
2. y' component of the velocity first moment (v_y')
3. z' component of the velocity first moment (v_z')
4. x' component of the velocity second moment tensor (v^2_x')
5. y' component of the velocity second moment tensor (v^2_y')
6. z' component of the velocity second moment tensor (v^2_z')
7. x'-y' component of the velocity second moment tensor (v^2_x'y')
8. x'-z' component of the velocity second moment tensor (v^2_x'z')
9. y'-z' component of the velocity second moment tensor (v^2_y'z')


-------------------------------------------------------------------------------

RUNNING THE EXAMPLE
-------------------

In our example, the luminous and mass MGEs are the same and are contained in file *example/mge.dat* and have 8 components.  The position data are in *example/xy.dat*.

We will calculate moments for model parameters:

> inclination angle = 0.87 radians  
> distance = 4.6 kpc  
> black hole mass = 100 Msun  
> black hole scale length = 1 arcsec  
> velocity anisotropy = 0.01 (and is the same for all 8 MGE components)  
> rotation parameters = [0.,0.2,0.5,1.1,0.6,0.,0.,0.]  
> mass-to-light ratio = 2.6 (and is the same for all 8 MGE components)

The example function may be run by calling:

    $ ./cjam example/mge.dat 8 example/mge.dat 8 example/xy.dat example/output.dat 0.87 4.6 100. 1. 0.01 0.01 0.01 0.01 0.01 0.01 0.01 0.01 0. 0.2 0.5 1.1 0.6 0. 0. 0. 2.6 2.6 2.6 2.6 2.6 2.6 2.6 2.6

The example outputs the moments to file *example/output.dat*.  The precomputed output from this run is provided in the file *example/moments.dat*.  Successful execution of the example can be checked by running diff on both files:

    $ diff example/output.dat example/moments.dat


-------------------------------------------------------------------------------


NOTES
-----

The first moment calculations will fail if you try to run a spherical, isotropic, non-rotating model; there are checks in place to catch these models before they start running.  Models that are close to being spherical, isotropic and non-rotating may also fail -- increasing the accuracy of the integrations should help though this has not tested.


-------------------------------------------------------------------------------


DIRECTORY STRUCTURE
-------------------

CJAM/
> *cjam.c*              : example wrapper to call moment functions  
> *cjam\_main.c*        : example wrapper to pass command line arguments  
> *Makefile*            : example makefile  
> *LICENSE*             : BSD 2-clause license  
> *README.md*           : this document

CJAM/EXAMPLE/
> *mge.dat*             : example file containing MGE parameters  
> *moments.dat*         : example file containing example moments  
> *xy.dat*              : example file containing stellar positions

CJAM/INTERP/
> *interp.h*            : header file for interp directory  
> *interp2dpol.c*       : performs interpolation over a 2d polar grid

CJAM/JAM/
> *jam.h*                   : header file for jam directory  
> *jam\_axi\_rms\_mgeint.c* : integrand for second moments  
> *jam\_axi\_rms\_mmt.c*    : second moments  
> *jam\_axi\_rms\_wmmt.c*   : weighted second moments  
> *jam\_axi\_vel\_losint.c* : outer integrand for first moments  
> *jam\_axi\_vel\_mgeint.c* : inner integrand for first moments  
> *jam\_axi\_vel\_mmt.c*    : first moments  
> *jam\_axi\_vel\_wmmt.c*   : weighted first moments

CJAM/MGE/
> *mge.h*               : header file for mge directory  
> *mge\_addbh.c*        : add a black hole component to an MGE  
> *mge\_dens.c*         : volume density distribution of an MGE  
> *mge\_deproject.c*    : deproject an MGE  
> *mge\_qmed.c*         : median flattening of an MGE  
> *mge\_read.c*         : read MGE from file into a structure  
> *mge\_surf.c*         : surface density of an MGE

CJAM/TOOLS/
> *maximum.c*           : finds the maximum value in an array  
> *median.c*            : calculates the median of an array of values  
> *minimum.c*           : finds the minimum value in an array  
> *range.c*             : creates an array of n numbers between given limits  
> *readcol.c*           : read in data from a file  
> *readcol.h*           : header file for readcol  
> *sort_dbl.c*          : sorts an array of doubles  
> *tools.h*             : header file for tools directory (except readcol)  
> *where.c*             : selects a given subset of an array

[cappellari2008]: http://adsabs.harvard.edu/abs/2008MNRAS.390...71C
[cappellari2012]: http://adsabs.harvard.edu/abs/2012arXiv1211.7009C
[denbrok2013]: http://adsabs.harvard.edu/abs/2013arXiv1311.3303D
[watkins2013]: http://adsabs.harvard.edu/abs/2013MNRAS.tmp.2480W
