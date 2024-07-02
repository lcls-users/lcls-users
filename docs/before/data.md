# Data Loading and Processing

## Basics of psana

Psana design borrows ideas from multitude of other framworks such as pyana, myana, BaBar framework, etc. Its main principles are summarized here:
<blockquote>
support processing of both XTC and HDF5 data format
user code should be independent of specific data format
should be easy to use and extend for end users
support re-use of the existing analysis code
common simple configuration of user analysis code 
<blockquote>


```bash
import psana
```