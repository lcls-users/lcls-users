# Routine beamtime analysis

LCLS offers several tools to help you analyze your experimental data. 
The chart below attempts at articulating them based on whether they are more generic tools like `psana` or more experiment specific like `OM` 
and whether they are mostly used for live analysis during beamtimes like `AMI` or for slightly delayed feedback or even offline like `smalldata` and `btx`. 
The `eLog` provides a unifying entrypoint from your browser.

| ![Data Analysis Tools](images/data_analysis_tools.png) | 
|:--:| 
| *__LCLS Data Analysis.__ TBD.* |


## The `btx` library and workflow manager {: #btx}

!!! note "Quicklink"
    - **btx** - [btx Github repository][1]
    - **documentation** - [btx code documentation][2]

*btx* is a resource for analyzing experiments performed at LCLS, both during and after beamtime. To facilitate rapid analysis, common data processing steps have been organized into tasks that can be easily accessed from the eLog or command-line without users having to write new code. This is a primarily Python-based package to which contributions are welcome, as *btx* is intended as a library to serve the user community at LCLS. Currently most tasks are geared toward SFX data analysis, but we intend to expand the scope to encompass other experiments, such as SPI and SAXS/WAXS.

| ![btx worfklow](images/btx_sfx_workflow.png) | 
|:--:| 
| *__Example of SFX workflow implemented with `btx`.__ 1) Define the Airflow workflow within `btx`, eventually making use of `tppwrw` to deploy containers. 2) Setup the workflows in the eLog. 3) Trigger the workflows manually or automatically from the eLog. 4) Check the results interactively in the eLog Summary panel.* |

### Usage

#### Preparing the configuration file

To run *btx*, we first need to generate a configuration file that lists the desired tasks and defines their corresponding parameters. This file follows yaml format; a partial example file is shown below:
```yaml
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
```bash
${SIT_PSDM_DATA}/<instrument>/<experiment>/scratch/<my_directory>/yaml
```
where `${SIT_PSDM_DATA}` is one of the following:
```bash
/sdf/data/lcls/drpsrcf/ffb # S3DF cluster
/cds/data/drpsrcf # PCDS FFB cluster
/cds/data/psdm # PCDS psana cluster
```
For more information on these clusters, see the [Computing Accounts and Resources Page](/before/accounts/).

The listed tasks can then be run either from the elog (which makes it possible to chain multiple tasks together in a streamlined fashion) or command line as described below.

#### From the command-line

To make the executable accessible, add the following line to your `~/.bashrc` for analysis on the PCDS clusters:
```bash
export PATH=/cds/sw/package/autosfx/btx/scripts:$PATH
```
or for analysis on S3DF:
```bash
export PATH=/sdf/group/lcls/ds/tools/btx/scripts:$PATH
```
This change can be applied by either running `source ~/.bashrc` or opening a new terminal.

We recommend always launching analysis tasks from the same directory within your scratch directory so that all the SLURM log files accumulate at that specific path. For example, we typically use the following hierarchy:
```bash
${SIT_PSDM_DATA}/{instrument}/{exp}/scratch/btx/
├── yaml/
|  └── default_config.yaml  # Experiment YAML file
└── launchpad # <- Launch files from here
```
If launching from within the `launchpad` directory, as above, a specific task can be run as follows:
```bash
elog_submit.sh -c ../yaml/default_config.yaml -t [my_task] -n [n_cores]
```
If running this from the psana cluster, the following arguments should be added to the above command: `-f SLAC -q psanaq`. Command-line arguments for the `elog_submit.sh` script follow:
```
| Flag  | Meaning                                             | Usage                |
| -f    | Facility: SRCF_FFB (FFB), SLAC (psana), or S3DF     | -f <facility>        |
| -q    | Queue to use.                                       | -q <queue>           |
| -a    | SLURM account, only relevant for S3DF               | -a <account>         |
| -t    | Task to run. Must match the name in the YAML file.  | -t <task>            |
| -c    | Path to the configuration file in YAML format.      | -c <config_file>     |
| -e    | Name of the experiment.                             | -e <experiment_name> |
| -r    | Run to process. If supplied override YAML value.    | -r <run_number>      |
```
A related executable, `submit_range_run.sh`, will launch the same task across a series of runs. This script can be run in the same fashion as `elog_submit.sh` and with the same flags, except an additional `-s` flag should be added that determines the last run number to process. 

More details about available tasks can be found in the relevant experiment pages.

#### From the eLog

### Contributing to the code

The repository can be cloned from this [Github link](https://github.com/lcls-users/btx). The README.md describes guidelines for contributing to the code. New tasks should be added as separate functions to [tasks.py](https://github.com/lcls-users/btx/blob/main/scripts/tasks.py) and then can be run from the command-line as described above. To make a new task accessible from the eLog… (should this ability be accessible to users?)

[1]: https://github.com/lcls-users/btx
[2]: https://lcls-users.github.io/btx/