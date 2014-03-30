`pyrmsynth` - Python based RM Synthesis code including RMCLEAN
==============================================================

*Current version:* 1.2.1
*Updated on:* 2014-03-29

`pyrmsynth` performs RM synthesis, either simply by Fourier transformation 
(to produce a dirty image) or using the RMCLEAN method as described by 
Heald, et al. (2009).  It uses FFTs for the Fourier inversion and, as far as  
known to the authors, this is the only RM synthesis software around that does
this. The Numpy FFTs are themselves quite fast, but in order to use them, the
data first need to be placed on a regularly spaced lambda^2 grid. For this, the
data are "gridded" by convolution with a Kaiser-Bessel Window function and
sampling at regular intervals, as described in e.g. Beatty, Nishimura, and
Pauly (IEEE Transactions in Med. Imaging, Vol 24, No. 6, 2005). This procedure
also naturally allows for the handling of non-regularly spaced frequencies.

The gridding procedure, which requires a convolution, is quite slow when 
implemented in pure Python, so I was re-implemented the gridding routines using
Cython, which converts python-eque code into C code that can be compiled and 
imported into Python.

The result is a package that performs fast RM Synthesis and RM CLEAN imaging
while still providing the flexibility of a Python interface.

pyrmsynth contains an application `rmsynthesis.py` for processing the lines of 
sight in a "stack" of sky images, i.e. many polarized sky images at different 
frequencies. This application was written with [LOFAR](http://www.lofar.org) 
processing in mind, but should be generally useful as long as your images are
provided as a set of FITS files generaged by CASA (or something compatible).

pyrmsynth also can be used as a more generic library for writing your own 
RM synthesis applications. The `rm_tools` sub-package contains efficient 
classes for RM synthesis and RM CLEAN. You can easily write your own scripts 
to do file I/O and use this package to do the actual RM synthesis computations
in an efficient manner.

Code usage:

python rmsynthesis.py <input parameter file>

The rmsynthesis.py software, for the moment, works on sets of FITS files. Each
FITS file contains images from a single sub-band, or some other subset of the
observed frequencies. As a default, the code assumes all Stokes parameters
to be saved in one FITS file. There is an additional option that allows
for the handling of separately save Q and U FITS files. Simply put all FITS
files in a single directory and the software will read them all in, stack them
into a single data cube, and perform RM synthesis along each line of sight. If
user defined frequency weights should be included, you have to provide a text
file that gives a weight to be applied to each frequency. The name of this file
MUST be "weight.txt".

The software reads in a parameter file. An example can be found in the file
rmsynth.par. Copy this file elsewhere and edit to your heart's content. All of
the parameters listed in the sample file must be included. A description of the
various options is included in the comments in the .par file.

In addition to the parameter file, there are a couple of options that you can
set when running the code. Type rmsynthesis.py -h if you need help. Right now,
the options are

Options:

  --version        show program's version number and exit
  
  -h, --help       show this help message and exit
  
  -p, --plot_rmsf  Plot the RMSF as soon as it is computed.
  
  -V, --stokes_v   Produce a Stokes V cube after reading the fits files.
  
  -s, --separate_stokes
                   Indicate that the Stokes Q and U input images are stored in
                   separate FITS files.
                   
  -f, --freq_last  Indicate that NAXIS4 is the frequency axis.
  
  -r, --rest_freq  Indicate that the frequency for an image is given in the
                   RESTFREQ header keyword

For more detailed information, please refer to the pyrmsynth
wiki (https://github.com/mrbell/pyrmsynth/wiki/Using-rmsynthesis.py).
Any bugs and issues can be reported to the developers via the github issue
tracker.
                   
`pyrmsynth` is licensed under the [GPLv3](http://www.gnu.org/licenses/gpl.html).
