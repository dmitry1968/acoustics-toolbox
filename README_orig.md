
# Acoustics Toolbox

Original Readme (index) file for the [Acoustics Toolbox](http://oalib.hlsresearch.com/Modes/AcousticsToolbox/).

<!-- TOC -->

- [Acoustics Toolbox](#acoustics-toolbox)
    - [License](#license)
    - [GUI Wrapper:](#gui-wrapper)
    - [Which version should I download?](#which-version-should-i-download)
    - [Overview](#overview)
    - [Porting the code](#porting-the-code)
        - [Record length](#record-length)
        - [Ice Scatter module](#ice-scatter-module)
        - [Plotting](#plotting)
    - [Installation Notes](#installation-notes)
    - [Updates](#updates)
    - [Frequently Asked Questions](#frequently-asked-questions)

<!-- /TOC -->

## License
  
Copyright (C) 2009  Michael B. Porter  
  
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.  

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.  

You should have received a copy of the GNU General Public License along with this program.  If not, see <http://www.gnu.org/licenses/>.  
  

## GUI Wrapper:

[ACT](http://www.curtin.edu.au/curtin/centre/cmst/products/actoolbox/index.html): Matlab front-end for the Acoustic Toolbox written by [Alec Duncan](http://www.curtin.edu.au/curtin/centre/cmst/staff/duncan.html) from the [Centre for Marine Science and Technology](http://www.curtin.edu.au/curtin/centre/cmst/) at [Curtin University](http://www.curtin.edu.au).

## Which version should I download?

There is only one version of the source code; however, you may see options for different pre-compiled binaries. If you're using another machine or operating systems you'll need to recompile it which requires a Fortran95 compiler. The free GFortran compiler works well. We have also used the free `g95` compiler in the past; however, the current version of AT uses `get_command_parameter`, which is a feature of Fortran 2003 not in g95 as of this writing.  
  

*   **atWinPII_f95**. Version for a Windows, Pentium II machine.  The Makefiles are in a sort of Unix style. If you download cygwin (publicly available on the web) you get a sort of pseudo-Unix window in which you can just type `make all` and `make install` to recompile and install.
    
*   **atLinuxPII_f95**. Same as above but with binaries compiled under Linux.
    
*   **atOSXintel**. For the Mac. Compiled with gfortran.  
    

To use the binaries, you will like need gfortran installed (which is very easy) since the binaries use dynamic libraries that need to be pre-installed.

## Overview

The Acoustic ToolBox includes four acoustic models:

**BELLHOP**: A beam/ray trace code

**KRAKEN**: A normal mode code

**SCOOTER**: A finite element FFP code

**SPARC**: A time domain FFP code  

In addition, AT contains BOUNCE, which omputes the reflection coefficient for a layered medium and may be used to provide input to BELLHOP. A common input structure has been used throughout so that only minor modifications are needed to switch from one program to another.

All the models produce `shade' files that can be processed using a common set of plotting routines to plot transmission loss vs. range or vs. range and depth.

## Porting the code

Depending on your platform, you may be able to execute the pre-compiled binaries. Currently we use `GFORTRAN` to compile those. However, our version requires access to gcc dynamic libraries. Therefore, you will need to have a compatible version of gcc and gfortran installed on your system to run the pre-compiled binaries.  

The package adheres strictly to the Fortran 2003 standard. A few of the Fortran 2003 extensions from Fortran 90 are used.  

### Record length

Fortran compilers differ in whether record lengths should be declared as bytes or words. To ensure the programs run, I declare lengths in bytes. If your compiler uses words as the default, look for a compiler switch to override that so that your files are not unnecessarily large.

Some of the programs in this package use direct access I/O and need to know a record length. Many systems do not maintain this information as part of the file info so the code writes that information directly into the file. Then the file is opened twice. First it is opened with a dummy record length to read the actual record length from the file. Then it is re-opened with the correct record length. On machines that _do_ know the correct record length, that first open may generate an error message and will have to be removed. The RECL specifier should then also be removed from the second open.  

### Ice Scatter module

The Twersky-scatter option is problematic because it calls `mathieu.f` and `slbessel.f` which don't adhere to a rigorous Fortran standard. To avoid this problem, the current distribution has this option disabled.

If you want to put it back in, you will need to link to `twersk.f` instead of `twerskfuse.f`. These routines call `mathieu.f` which needs to be compiled with a switch to initialize all variables to zero. Most compilers have some such switch.

### Plotting

Plot packages come and go. However, the output formats of the various models are all fairly simple to work with. The distribution includes Matlab scripts which I currently use for plotting. If you don’t have Matlab, then these will at least provide an example of how to read the file formats.  
  

## Installation Notes

To run the Acoustics Toolbox, the executables need to be in your path. The process for setting your path depends on your operating system. I can't stay current with the options but here are some things that have worked in the past:  

DOS: add the following to your autoexec.bat to make the scripts executables in your default path:

```
SET PATH=%PATH%;C:\\AT\\SCRIPTS;C:\\AT\\BIN
```

**Windows 2000** (and perhaps NT): right click on the “My Computer” icon and select “Properties”, “advanced”.

**Windows XP**: go to control panel/Performance and Maintenance/System/Advanced and look for the pushbutton labled “Environment Variables”. There you can edit the system variable “Path”.  

Obviously these lines should be updated to reflect whichever directory you installed things in. You may need to reboot to make sure this takes effect. Note that the DOS and Unix scripts are not maintained. We currently execute the binaries via Matlab.  

If you’re having problems, verify your path includes these by typing “path” from a DOS window. Typically you run KRAKEN by typing something like `kraken pekeris`. at the DOS command line, or `kraken( 'pekeris')` at the Matlab command line.

On Unix/Linux/MacOSX systems you can ensure that path info is set in Matlab at start up by editing `.../bin/matlab` to add something like:

```
source ~/.profile  
```

where `.profile` is the shell start up script that initializes your path.  

Finally, if you use Matlab then you just need to use the "Set Path" option under the "File" menu. If you include all of the Acoustics Toolbox in your Matlab path, then the Matlab scripts find the location of the binaries using the Matlab `which` command.  

Once you have the basic code working, you might want to run some of the test batteries in at/tests to completely verify your installation. The m-file, runtests.m runs the complete suite of tests using the Fortran binaries. The m-file `runtestsM.m` does the same thing, using the Matlab versions of BELLHOP, SCOOTER, BOUNCE, and SPARC.

## Updates

(Check also the individual subdirectories for updates on individual components of the Acoustics Toolbox.)

_December 1997_

Minor changes have been made to the package for improved f77 to f90 portability. Fortran 90 seems to treat parameters differently in terms of precision so some of those changes are important for the most accurate results.

_July 1999_

The whole package has been converted to f90/95 standard with free-format lines and taking advantage of dynamic allocation. The code is more concise and portable. Dynamic allocation means that you will not have to worry about dimensional limits--- the size of the problem you can solve is limited only by RAM on your system.

The change to dynamic allocation has forced a small change in the input structure. Previously an input format was often used with a number of items in a vector followed by the actual vector. For instance, the vector of receiver depths is read this way.

In the new version the number of items in the vector must appear on a separate line so that the code can read it, allocate the space, and then read the vector contents.  

_May 2009_

The Matlab version of BELLHOP has been replaced by a new version that traces one beam at a time. The ealier version traced all the beams in parallel for efficiency; however, the logic is too complicated to maintain.  

_December 2009_

Converted most `char foo*5` references, which are deprecated in the new Fortran, in favor of `char (len=5) :: foo`.  

_January 2010_

Updated the volume attenuation from the 1967 Thorp formula to the more recent one in JKPS. Converted all the Fortran models so that they would determine the file names from the command line arguments. This eliminates the need for DOS, Unix, or Matlab scripts to move files around.  

_July 2010_

Made all type conversions, e.g. complex to real or double to single, explicit. Expanded use of EXPLICIT NONE to ensure all variable types are called out. Further expansion of the lengths of variable names to take advantage of Fortran 2003. Expanded use of structure-variables. Additional changes as described in the read.me files for the various models. This is a fairly significant revision in terms of the evoloution from Fortran 77 to Fortran 2003. However, there are virtually no changes to the input and output files.  

_April 2011_

Greatly expanded use of `INTENT( IN, OUT,  etc.)` to clarify the source code. Continued changes in variable and subroutine names to make them more readable as the Fortran 2003 standard permits. Use of explicit dimension sizes in variable declarations to make it easier for the compiler to detect incompatibilities. Change to SHDFile format for the pressure field to allow multiple x-y coordinates for the source.  

_October 2011_

Added an option for a source beam pattern in KRAKEN and SCOOTER. The pattern is read from a file, following the same procedure as in BELLHOP. The beam pattern is actually introduced after the KRAKEN or SCOOTER run by processing the mode file (FIELD and FIELD3D) or the Green's function file (FIELDS). Currently the beam pattern is implemented by shading the wavenumber spectrum. As a result the beam pattern must be symmetric with respect to the horizontal (only the part of the pattern with positive angles is used). This capability has also been implemented in the Matlab versions of FIELD and FIELDS; however, in FIELD it current only works for the range-independent case.  

_March 2012_

The new Matlab changed how it did shell escapes (to run things at the command line level) so that piping of stdin and stdout no longer worked. To get around this FIELD and FIELDS have been modified so that they read from a user specified file instead. The field parameter information is now read from `root.flp` instead of `field.flp`, where `root` is the root of a filename passed to the code. This change has been made in both the Fortran and Matlab versions. In addition, to make the Matlab more compatible with the Fortran version, the various output files (SHDFIL.mat, GRNFIL.mat, etc.) now use individually named files (root.shd.mat and root.grn.mat, etc.).  

_February 2013_
A bug was fixed in detecting the end of the SSP for a given layer. Instead of testing for exact equality of the SSP depth to the bottom-depth of the layer, I had previously checked for agreement to within a tolerance. The tolerance was scaled based on the bottom depth of the layer so for a bottom depth of zero (in an aeroacoustic problem) the tolerance was 0 and the bottom was not detected.  

_February 2014_
Checks have been added to ensure certain vectors are monotonically increasing where that assumption is critical to the codes.  

_November 2014_
The Matlab routines that read altimetry and bathymetry files were not clearing old data on subsequent calls. This occured only when the flat boundary was selected and the routines were supposed to return dummy boundary information. Also, the ranges were not being converted to meters from kilometers. It effected SimplePE runs and probably the Matlab version of BELLHOP as well. These routines are not used by any of the Fortran implementations and the error often had no effect on the Matlab codes.  

_June 2014_  
Sometime around January of 2010 the Thorpe formula for volume attenuation was modified at the request of a user. The modified formula was slightly more accurate. The change was verified in BELLHOP; however, there an incorrect variable name was used in the crci routine used by KRAKEN and SCOOTER. That error mean the added Thorpe attenuation was never added. This is corrected in the new code. The volume attenuation was typically most important for higher frequency sources, which in turn were more often treated in BELLHOP. However, the error can be important for low frequency sources at very long range.  

_July 2017_  
The package has been modified to allow broadband runs for most of the models. Thus, for example, you can give a vector of frequencies to KRAKEN or SCOOTER and produce a single pressure file containing results for each of the frequencies. This has required a small change to the file formats for modfils, shdfils, grndfils, etc. In addition, all the Matlab codes have been modified to allow the '/' convention used in the Fortran versions. This option allows a user to either specify a full vector or just specify the upper and lower limits terminated by '/'. It then uses those limits to create a vector of equally spaced points.  

_November 2017_  
Updates and fixes to the noise routines in at/Matlab/noise. A routine has been added to construct the noise covariance matrix directly from the shdfil. The shdfil contains the complex pressure field vs. range and depth and the noise routine can use such files produced by KRAKEN, KRAKENC, SCOOTER, and BELLHOP.  

_March 2018_  
SimplePE was not updating the marching matrices in the case where there was a range-dependent SSP but a flat bottom. Fixed ...

## Frequently Asked Questions

_Why do I get an end-of-file in reading the very last line of the envfil?_

You left off a carriage return/line feed after the last line, or other appropriate end-of-line marker for your system.

_Why do I get a segmentation violation?_

You ran a case that required an outrageous amount of storage. The code is supposed to catch all such cases by detecting an error in the allocation of dynamic variables (or limits for the few arrays with fixed dimensions). However, we see cases where the compiler simply does not handle huge arrays properly.