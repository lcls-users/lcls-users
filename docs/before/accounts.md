
!!! note "Quicklinks"
    - **Account** - [Create a UNIX account][6]
    - **Computing** - [Access and Resources][1]
    - **Data Analysis** - for [LCLS-I][2] and for [LCLS-II][3]
    - **FFB docs** - [PCDS docs][8]
    - **S3DF docs** - ["Running at S3DF" for LCLS][4] and [official S3DF documentation][5].


# Step 1 - Get a computer account

To access SLAC computing resources, you need a valid SLAC UNIX account. A detailed description of the steps required to create one can be found [here][6].

!!! note "Enabling access to the S3DF"
    In order to use S3DF, it is necessary to enable your S3DF account using your SLAC UNIX account. Detailed information [in the S3DF documentation here][7].


# Step 2 - Access and process data
Now that you can access the computer systems at SLAC, it is time to get yourself oriented. 
It is useful for that to understand how the data flows from the beamline to storage, *a.k.a* to understand the LCLS Data Systems, built with 3 successive levels:

- **1 &#x3e;  DAQ+DRP - Data acquisition and reduction**:<br>
provides diagnostics within <1s. *(Experts only)*
- **2 &#x3e;  FFB - Fast Feedback**:<br> 
provides diagnostics and results within <1min. *(All)*
- **3 &#x3e;  Offline Analysis**:<br> 
for intensive compute during and after the beamtime. *(All)*

*TODO: figure to describe the network and data infrastructure.*

The following briefly describe how to access the FFB and various offline resources. Jump to: [FFB](#ffb) - [S3DF](#s3df) - [NERSC](#nersc).

### &#x2192; on the FFB - Fast Feedback System {: #ffb}

First, get in the system:
```
ssh <username>@pslogin.slac.stanford.edu
```
From there, you can access the FFB nodes:
```
ssh psffb
```
The experiments and their data can be found at the following path:
```
/cds/data/drpsrcf/<instrument>/<experiment>
```
!!! danger "Data import/export"
    Note that the FFB nodes are not exposed to external networks. If you need to transfer data, you can go through `psexport.slac.stanford.edu`.

*TODO: a few sentences on batch, interactive and jupyter.*


!!! note "More FFB info"
    For more information about the Fast Feedback System, check out the PCDS documentation [here][8].


### &#x2192; at S3DF - SLAC Shared Science Data Facility {: #s3df}
First, get in the system:
```
ssh <username>@s3dflogin.slac.stanford.edu
```


### &#x2192; at NERSC - National Energy Research Scientific Computing Center {: #nersc}

[1]: https://confluence.slac.stanford.edu/pages/viewpage.action?pageId=92183280
[2]: https://confluence.slac.stanford.edu/display/PSDM/LCLS+Data+Analysis
[3]: https://confluence.slac.stanford.edu/display/LCLSIIData/LCLS-II+Data+Acquisition+and+Analysis
[4]: https://confluence.slac.stanford.edu/display/PCDS/Running+at+S3DF
[5]: https://s3df.slac.stanford.edu/public/doc/#/
[6]: https://confluence.slac.stanford.edu/display/PCDS/Accounts
[7]: https://s3df.slac.stanford.edu/public/doc/#/accounts-and-access
[8]: https://confluence.slac.stanford.edu/display/PCDS/Fast+Feedback+System