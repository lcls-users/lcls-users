# How to open and use the pyFAI GUI

**How to open pyFAI GUI Method 1:**

On your local computer, do this:
create the file ~/.ssh/config
copy paste the below in it
Host s3df
  Hostname s3dflogin.slac.stanford.edu
  User philips
  ForwardAgent yes
  ForwardX11 yes
  ForwardX11Trusted yes

Host psana
  Hostname psana
  User philips
  ProxyCommand ssh s3df -W %h:%p
  ForwardAgent yes
  ForwardX11 yes
  ForwardX11Trusted yes

open terminal
ssh psana
Enter your password twice
enter: source /sdf/group/lcls/ds/ana/sw/conda1/manage/bin/psconda.sh
enter: pyFAI-calib2

**How to open pyFAI GUI Method 2 (Openn an S3DF OnDemand Desktop):**
https://s3df.slac.stanford.edu/pun/sys/dashboard
Under interactive apps: click on S3DF
hit launch and then wait aabout 30 sec and then click launch S3DF 
this should take you to an OnDemand Desktop
open a terminal in the new desktop by going down and hitting the black icon with a dollar sign in the top right and a dash in the middle 
enter: source /sdf/group/lcls/ds/ana/sw/conda1/manage/bin/psconda.sh (right click to paste)
enter: pyFAI-calib2

**Once you are in here is the tutorial for how to use pyFAI GUI:**
  https://www.silx.org/doc/pyFAI/latest/usage/cookbook/calib-gui/index.html

**For this SLAC Specific project**
Experiment Settings:
Energy (keV): 9.603044822272608
Wavelength (m): 1.2910925615868107e-10
Calibrant: AgBh (Geh) 
If they dont have your detector which they most likely will not then you have to manually input the  Detector specs by hitting the three dots and then under Custom detectors click manual definition:
Detector size specs collected from:
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7206547/ and `Detector Shape (fs dim, ss dim): (5632, 384)`
Size(h x w): 5632x384 (px)
Pixel size(h x w): 100 x 100 (μm) 
The size of the image that you select and the pixel size should match
image file: calib_max_flat_r0008.npy
path: sdf/home/p/philips/Philip/powder/calib_max_flat_r0008.npy
click next

**Mask:**
you can zoom in by highlighting a certain section and then unzoom by hittnig the x over the magnifying glass in the top left. 
mask certain pixels that are noise and do not contribute to the respective rings

**Peak Picking:**
Click on the peaks of the differnt rings and make sure that they are correctly numbered

**Geometry fitting:**
Make sure that the guessed geometry of the circles is accurate and then hit next. iF it not accurate then go back and mask different particles that are causing these inaccuracies. 

**Cake & Integration:**
Inspect the graph and confirm that the peaks correctly align with the scattering angles vertical lines
