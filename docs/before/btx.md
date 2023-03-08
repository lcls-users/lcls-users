
!!! note "Quicklink"
    - **btx** - [btx Github repository][1]
    - **documentation** - [btx code documentation][2]

# Introduction to *btx*

*btx* is a resource for analyzing experiments performed at LCLS, both during and after beamtime. To facilitate rapid analysis, common data processing steps have been organized into tasks that can be easily accessed from the eLog or command-line without users having to write new code. This is a primarily Python-based package to which contributions are welcome, as *btx* is intended as a library to serve the user community at LCLS. Currently most tasks are geared toward SFX data analysis, but we intend to expand the scope to encompass other experiments, such as SPI and SAXS/WAXS.

## Usage 

To run *btx*, we first need to generate a configuration file that lists the desired tasks and defines their corresponding parameters. This file follows yaml format; a partial example file is shown below:
```
setup:
  queue: 'anaq'
  root_dir: '/cds/data/drpsrcf/mfx/mfxx49820/scratch/btx'
  exp: 'mfxx49820'
  det_type: 'epix10k2M'

fetch_mask:
  dataset: '/data/data'

fetch_geom:

build_mask:
  thresholds: -10 5000
  n_images: 20
  n_edge: 1
  combine: True
```
The entries under `setup` define experiment parameters, while the remainder of the file lists individual tasks. Using any text editor, generate this configuration file in a `yaml` subdirectory within your experiment scratch folder:
```
${SIT_PSDM_DATA}/<instrument>/<experiment>/scratch/<my_directory>/yaml
```
where ${SIT_PSDM_DATA} is one of the following:
```
/sdf/data/lcls/drpsrcf/ffb # S3DF cluster
/cds/data/drpsrcf # PCDS FFB cluster
/cds/data/psdm # PCDS psana cluster
```
For more information on these clusters, see the Computing Accounts and Resources page.

The listed tasks can then be run either from the elog (which makes it possible to chain multiple tasks together in a streamlined fashion) or command line as described below.

### From the command-line

To make the executable accessible, add the following line to your `~/.bashrc` for analysis on the ffb or ana clusters:
```
export PATH=/cds/sw/package/autosfx/btx/scripts:$PATH
```
or for analysis on S3DF:
```
export PATH=/sdf/group/lcls/ds/tools/btx/scripts:$PATH
```

A specific task can then be launched by running the following:
```
elog_submit.sh -c [path_to_yaml] -t [task] -n [n_cores]
```
If running this from the psana cluster, the following arguments should be added to the above command: `-f SLAC -q psanaq`. We recommend always launching analysis tasks from the same directory within your scratch directory so that all the SLURM log files accumulate at that specific path. More details about available tasks can be found in the relevant experiment page.

### From the eLog

## Contributing to the code

The repository can be cloned from the following [Github link](https://github.com/lcls-users/btx). The README.md describes guidelines for contributing to the code. New tasks should be added as separate functions to [tasks.py](https://github.com/lcls-users/btx/blob/main/scripts/tasks.py) and then can be run from the command-line as described above. To make a new task accessible from the eLog… (should this ability be accessible to users?)

[1]: https://github.com/lcls-users/btx
[2]: https://lcls-users.github.io/btx/