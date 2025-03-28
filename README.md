#                     NeuCBOT v-3.0                      
Author: Shawn Westerdale

email: shawest@princeton.edu

Date: 8 Mar 2025

github: https://github.com/shawest/neucbot

# NeuCBOT (Neutron Calculator Based On TALYS)

## TABLE OF CONTENTS

1. [About NeuCBOT](#about)
  - i [General](#general)
  - ii [Dependencies](#dependencies)
  - iii [Directory Structure](#directory-structure)
2. [Setup](#setup)
  - i [Accessing the code](#accessing-the-code)
  - ii [Accessing (&alpha;,n) reaction databases](#accessing-alphan-reaction-databases)
3. [Usage](#usage)
  - i [The Basics](#basics)
  - ii [Material Composition Files](#material-composition)
  - iii [Isotope List Files](#isotope-lists)
  - iv [Alpha List Files](#alpha-lists)
4. [Output](#output)
5. [Citations](#citations)

----------------------------------------------------------
----------------------------------------------------------
## 1. <a id="about">About NeuCBOT</a>
### i. <a id="general">General</a>

NeuCBOT is a tool for calculating (&alpha;,n) yields and 
neutron spectra for arbitrary materials under alpha
exposure for arbitrary lists of alpha energies or in the
presence of alpha-emitting contaminants.

The underlying physics, calculations, and validation of this
code is presented in
[S. Westerdale, P.D. Meyers, _Radiogenic Neutron Yield Calculations for Low Background Experiments_, arXiv:1702.02465, 6 Feb 2017](https://arxiv.org/abs/1702.02465)
and
[S. Westerdale, _A Study of Nuclear Recoil Backgrounds in Dark Matter Detectors_, Ph.D. thesis, Princeton University (2016)](http://web.archive.org/web/20161216231000/https://www.princeton.edu/physics/graduate-program/theses/Westerdalethesis.pdf)

The core of the calculations performed with this tool is 
the nuclear reaction database generated by TALYS-1.95 [\[1\]](#1). 
NeuCBOT v-1.0 used a database generated by TALYS-1.6; this database can be accessed as an alternative, if specified as a run option.

By default, we proivde data for every naturally occurring
isotope for alpha energies ranging from 0 to 10 MeV in 
0.01 MeV intervals.

If the user wishes to expand their copy of the database to
include alpha energies or isotopes not included in the
default database, the user may install TALYS on their local
machine and run NeuCBOT with the -t option. 

NeuCBOT uses a SRIM-generated [\[2\]](#2) stopping power database
to determine the stopping power of the user-specified 
material, and uses this stopping power to integrate over
the alpha energies as they slow down.

If the user wants to perform these calculations for a set
of alpha-emitting contaminants, they can specify the 
isotopes and their concentrations. NeuCBOT looks up decay
data for these isotopes from the ENSDF [\[3\]](#3) database and 
saves them in a local database for future use.

### ii. <a id="dependencies">Dependencies</a>
This software is written in python. 

NeuCBOT was written specifically for Python 2.6.6 and upadated to Python 
3.12.6. To the extent that some scripts included in this package rely on
bash, was written for bash 4.1.2.

This code itself doesn't do anything particularly fancy, and
so it will likely work on systems running different versions
of Python and bash. However, it is possible that changing syntax
with different versions of Python and bash may require small
tweaks to be made to the NeuCBOT code.

### iii. <a id="directory-structure">Directory Structure</a>

Databases are stored in the ./Data directory. Within this 
directory there are the following subdirectories:

* ./Data/StopingPowers/ : SRIM-generated stopping powers for each element
* ./Data/Decays/        : Downloaded ENSDF files with alpha decay data for given isotopes
* ./Data/abundances.dat : Natural abundances of every isotope, from [\[4\]](#4)
* ./Data/Isotopes/      : (&alpha;,n) reaction data library

Within the ./Data/Isotopes/ directory, there is a 
subdirectory for each element, and a subdirectory within 
this subdirectory for each isotope of that element.

Within an isotope's directory, there is a direcotry called
NSpectra/ which contains outgoing neutron spectra for 
alpha particles of a given energy undergoing the 
(&alpha;,n) reaction on this isotope. 

The TalysOut/ direcotry contains the output from TALYS,
from each simulated alpha particle at a given energy upon the
isotope. Files in this directory include cross sections for
the various alpha-induced nuclear reactions that may occur,
including the (&alpha;,n) reaction, as well as cross sections
for producing various excited nuclei and gammas at various
energies. These files may be of particular interest to people
wishing to explore gammas that may be correlated with 
(&alpha;,n) neutrons.

Data obtained from JENDL is stored in the JendlOut/ direcotry, 
which contains folders named /MT␣/. This naming is taken from 
the ENDF6 format and is used to determine the reaction type.
JENDL dataset provides information on the partial reactions, 
where 
* MT50 stands for (&alpha;,n<sub>0</sub>) of reaction with the 
residual nucleus in ground state
* MT51 stands for (&alpha;,т<sub>1</sub>) of reaction with the 
residual nucleus in the $1^{\text{st}}$ exited state

&emsp;&emsp;&emsp; $\cdot \cdot \cdot$

* MT[50+X] stands for (&alpha;,n<sub>X</sub>) of reaction with 
the residual nucleus in the $\text{X}^{\text{st}}$ exited state

* MT4 stands for (&alpha;,n) reaction in general and exactly 
equal to a summ of all partial reactions 

Each /MT␣/ directory contains differential by alpha energy 
cross section file called /cross-section, which is used for 
neutron yeild calculations. This files were structured in 
the TALYS format so the neucbot programm could make all the 
calculations with minimal changes.

Also each /MT␣/ directory contains double differential by 
alpha and by neutron energies cross section files called 
/outputE␣, for each alpha energy. It is used for calculating
neutron spectrum.

JENDL doesn't contain data suitable for NeuCBOT, so JENDL 
data was translated to required type using side github 
project called [JENDL5-to-NeuCBOT](https://github.com/iv-gonch/JENDL5-to-NeuCBOT). This project was done by Ivan Goncharenko,
e-mail: iv.gonch.0907@gmail.com.

For questions or comments, feel free to send an email.

----------------------------------------------------------
----------------------------------------------------------
## 2. <a id="setup">Setup</a>
### i. <a id="accessing-the-code">Accessing the code</a>

The code for NeuCBOT is stored in 
<https://github.com/shawest/neucbot>.

To access the code, you can either download it directly by 
clicking the "Clone or download" button and choosing 
"Download ZIP", or checking out the repository with the 
command

```bash
git clone https://github.com/shawest/neucbot.git
```

This will create a directory called neucbot in your current
directory.

### ii. <a id="accessing-alphan-reaction-databases">Accessing (&alpha;,n) reaction databases</a>

There are two options for obtaining (&alpha;,n) reaction 
databases.

Databases for all naturally occurring isotopes ranging from
0 to 10 MeV alpha energies have been precompiled. These 
databases have been grouped together by element are are 
storred as gzipped tarballs on github at

<https://github.com/neucbot-datasets>

Databases can be downloaded from this github repository and
unzipped with the command
```bash
tar -xvzf X.tar.gz
``` 
for .tar.gz files or 
```bash
tar -xvjf X.tar.bz2
```
for .tar.bz2 files, where X is the chemical symbol of the 
element. These unzipped files should be stored in 
./Data/Isotopes/ in order to be read by NeuCBOT.

README files in each element's database gives details of how 
the cross sections were calculated, including the version of 
TALYS. 
Repositories with element names correspond to v-1.0, made with 
TALYS-1.6; those labeled _v2 were made with TALYS-1.95.

For convenience, NeuCBOT comes with a bash script in the 
./Scripts directory called download_element.sh. This script
takes the chemical symbol of an element as an argument, and
downloads the TALYS-1.95 database for that element, setting it 
up properly. This script can be run from ./neucbot directory 
with the command:
```bash
./Scripts/download_element.sh X
```
where X is the chemical symbol of the element. 

Data fron JENDL-5 can be downloaded similarly by running the 
command:
```bash
./Scripts/download_jendl_data.sh X
```

Data from TALYS-1.6 can be downloaded similarly by running the 
command:
```bash
./Scripts/download_element_v1.sh X
```

If NeuCBOT is run with the -d option, it will automatically 
run download_element.sh for each element missing from your 
local database, using the calculations corresponding to the 
latest versions.
Versions of the database can optionally be specified after the 
-d argument (e.g. "-d v2" or "-d j" or "-d v1").

Alternatively, you can generate your own database,if you 
have TALYS installed on your computer. To do so, run 
NeuCBOT with the -t option. Doing so will save input files
in a directory called TalysInput, in the isotope's database
directory.

This option allows you to calculate (&alpha;,n) yields and
spectra for isotopes and alpha energies not included in
this database, though it should be noted that this will 
typically slow down the code.

The TALYS software can be downloaded from the homepage at
<http://www.talys.eu/>

----------------------------------------------------------
----------------------------------------------------------
## 3. <a id="usage">Usage</a>
### i. <a id="basics">The Basics</a>

NeuCBOT can be run from the command line of any unix-based
operating system with the command

```bash
python3 ./neucbot.py [list of options]
```
where the list of options is a series of parameters given
to NeuCBOT. Each option starts with a hyphen and is followed
by any arugments needed by that option.

A list of arguments is given below, follwed by parameters
required by that option (written in square brackets) and
a description of what that option does in parentheses.

* -h \[<i>no arguments</i>\] (print help message)
* -l \[alpha list file name\] (file with a list of alpha energies to be used)
* -c \[decay chain file name\] (file with a list of alpha-emitting contaminants)
* -m \[material composition file name] (file with a description of the material composition)
* -s \[alpha step size in MeV\] (the step size to be used when integrating over the alpha energy, minimum of 0.01)
* -t \[<i>no arguments</i>\] (tells NeuCBOT to run TALYS for reactions not in libraries)
* -d \[<i>no arguments</i>\] (if an element is missing from the (&alpha;,n) database, automatically run the download_element.sh script to download the element's database)
* -o \[output file name\] (name of text file to store output to)
* --print-alphas \[<i>no arguments</i>\] (prints a list of alpha energies being used)
* --print-alphas-only \[<i>no arguments</i>\] (same as --print-alphas, but aborts after printing)
* --force-recalculation \[<i>no arguments</i>\] (if TALYS is installed in your machine, run it for each alpha energy and each isotope, overwriting pre-existing entries in the database if necessary)

In order to run NeuCBOT, the user must provide a material 
description (-m material_file_name) and either an alpha 
energy list (-l alpha_list_name) OR a list of contaminants 
in your decay chain of interest (-c contaminants_list_name).

All other options are optional. 

If the -o option is not specified, all output will be displayed to the terminal. 

If the -s option is not specified, a default step size of 0.01 MeV will be assumed.

TALYS/JENDL databases can be select by adding letter "t" or 
"j" (accordingly) both in uppercase and lowercase.
If database is not selected or there is no JENDL data, the 
calculations will use data from TALYS.

JENDL database provides conducting calculations for partial 
reactions. For now running such calculations is in development 
and not user friendly: you should manually change arguments 
MT=4 in function ```getIsotopeDifferentialNSpecJENDL()``` on 
the 563th line and in function ```readTotalNXsectJENDL()``` 
on the 567th line to MT=50/51/etc.

NeuCBOT comes with acrylic as an example material, stored in ./
Materials/Acrylic.dat. 

We also provide the <sup>232</sup>Th, <sup>235</sup>U, and 
upper and lower <sup>238</sup>U decay chains (split above 
<sup>226</sup>Ra), as well as the <sup>210</sup>Pb decay chain 
as example decay chains. These chains are stored in ./Chains/.

Examples of alpha lists can be seen in ./AlphaLists/ where we 
include alpha energies emitted by several isotopes found in 
the decay chains.

Example usage of neucbot with both of these options is given below

```bash
python3 ./neucbot.py -m Materials/Acrylic.dat -c Chains/Th232Chain.dat
```
or
```bash
python3 ./neucbot.py -m Materials/Acrylic.dat -l AlphaLists/Rn220Alphas.dat
```

It should be noted that the order of these options does not 
matter.

If any isotopes listed in the material composition description 
are not present in the (&alpha;,n) reaction library, NeuCBOT 
will throw an error.

### ii. <a id="material-composition">Material Composition Files</a>
This section describes the anatomy of a material composition 
file. One such file must be given to NeuCBOT as an argument to
the -m option.

These text files consist of four columns. 

The first column is the chemical symbol of an element in 
the material. Capitalization does not matter.

The second column is the mass number of this isotope. If
0 is specified, it will be assumed that all naturally 
occurring isotopes of this element are present at their
natural abundances, as reported in [\[4\]](#4).

The third column is the percent mass of the specified
element or isotope.

The fourth column is the basename: empty or "t" for TALYS and
"j" for JENDL.

For example
> c 12 45
>
> c 13 55

would describe a material that is made of 45% <sup>12</sup>C
and 55% <sup>13</sup>C, by mass.

More realistically,

> c 0 59.984
>
> o 0 31.962
>
> h 0 8.054

is the composition of acrylic, assuming carbon, oxygen, and
hydrogen isotopes are all present according to their natural
abundances.

After reading a material composition file, NeuCBOT normalizes
the composition so that all isotopes add up to 100%. The
user may therefore specify mass fractions as percentages or
decimals, as they prefer.

All lines in this file that start with a \# are skipped
by NeuCBOT, allowing the user to leave comments in these 
files.

### iii. <a id="isotope-lists">Isotope List Files</a>
Decay chains are specified in text files with two columns.

The first column specifies the name of the isotope, which
is the chemical symbol followed by the mass number, and
the second column is the percent of decays of the chain
in which the specified isotope occurs, relative to the 
top of the chain.

It is important to note that the second column must be
given in <b>percent</b> probability of the isotope 
appearing.

As an example, the <sup>232</sup>Th decay chain is specified
as

> Th232 100
>
> Th228 100
>
> Ra224 100
>
> Rn220 100
>
> Po216 100
>
> Bi212 35.94
>
> Po212 64.06

Similar to the material composition description, lines that
start with a \# are skipped by NeuCBOT.

### iv. <a id="alpha-lists">Alpha List Files</a>
Alpha list files are structured very similiarly to isotope 
list files.

Each text file has two columns. The first column specifies
an alpha energy in MeV, and the second column specifies the 
<b>percent</b> porbability of an alpha of that energy being
produced.

For example, <sup>216</sup>Po alphas can be described with
a file that reads as

> 6.7783 99.9981
>
> 5.985 0.0019

Like in the two above cases, lines that start with a \# 
are skipped by NeuCBOT.

----------------------------------------------------------
----------------------------------------------------------
## 4. <a id="output">Output</a>
The output of NeuCBOT is hopefully mostly straightforward to 
understand. 

As NeuCBOT runs, it prints the alpha energy every 10 keV, so 
its progress can be tracked. It will also output any relevant 
warnings it may encounter while running. The most likely 
warnings will be missing isotopic data in the (&alpha;,n) 
database. If the alpha energies simulated are all under 10 MeV 
and only naturally occuring isotopes are being simulated, the 
simplest solution is to run NeuCBOT with the -d option, an it 
will acquire (&alpha;,n) data automatically. Otherwise, the 
user should install TALYS and run with the -t option.

The output provides the total neutron yield, calculated by 
integrating all of (&alpha;,n) cross sections over the tracks 
of the alphas as they slow down. The units used here are the 
number of neutrons produced per decay of the entire decay 
chain or list of alpha energies being simulated. So if a chain 
has three alpha-emitting isotopes, NeuCBOT tells you the 
expected number of neutrons after all three isotopes have 
decayed with their given branching ratios.

NeuCBOT also gives a breakdown of the contribution of each 
isotope in the target material to the total neutron yield.

Due to effects of binning the neutron energy spectrum and 
inaccuracies of Riemann integration, it is possible that the 
integral of the neutron energy spectrum will be slightly 
different from the total neutron yield. These differences are 
typically under ~1%. We therefore include the integral of the 
neutron energy spectrum in the output.

Lastly, the neutron energy spectrum is printed. By default, 
the spectrum is binned into 100 keV bins and the spectrum is 
given in units of $\ \cfrac{\text{neutron}}{\text{decay} \cdot 100\ \text{keV}}\ $.

----------------------------------------------------------
----------------------------------------------------------
## 5. <a id="citations">Citations</a>
0. <a id="1">A. Koning, et al. TALYS-1.6. http://www.talys.eu/</a>
1. <a id="2">J. Ziegler, et al. SRIM-2008. http://www.srim.org/</a>
2. <a id="3">J. K. Tuli, et al. Evaluated Nuclear Structure Data File (ENSDF), 1996.</a>
3. <a id="4">P. De Bievre and P.D.P. Taylor, Int. J. Mass Spectrom. Ion Phys. 123, 149 (1993).</a>
