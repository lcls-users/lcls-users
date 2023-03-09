# Reanalyzing Timing Data
In most cases, the timestamps produced by the timetool during your experiment are accurate and reliable. These values should have been computed by the DAQ and written into the corresponding xtc files. Nonetheless, in certain instances you may want to revisit or reprocess the data, for example if:

- The timetool needed to be realigned.
- A new calibration was not applied during the beam time.
- You are very familiar with the timetool and would like to try to further improve the temporal resolution through custom analysis.

The timetool images were saved along with the rest of your experimental data, so it is possible to revisit them in your own time. A number of tools are provided by SLAC in order to facilitate this type of offline analysis. The following page will describe two such methods catered to different use cases:

1. [**The TimeTool Module**](#TimeTool) - Using this tool you can reprocess your data using a new calibration. Basic familiarity with Python is required. The underlying analysis routines are written in C++ and not easily modified.
2. [**btx**](#btx) - btx provides an all-Python entry point to timing analysis, for those interested in the underlying implementation. Standard analyses can be run easily from the command line by specifying btx tasks. More advanced users may like to clone the repository and edit in custom functionality.


## Using the TimeTool module {: #TimeTool}

A brief summary of `TimeTool` usage is provided below. For more information, please refer to this [confluence page](https://confluence.slac.stanford.edu/display/PSDM/TimeTool).

### Calibration

**Procedure**:

### Jitter Correction

**Procedure**:

[<p align="right">&#8673;Back to Top</p>](#)

## Using btx {: #btx}
The remainder of this page will explain how to use [btx](https://www.github.com/lcls-users/btx) to retrieve and interact with timetool data. The first two sections explain how to submit btx tasks from the command line to create a new time tool calibration model and apply it to your data. The final section provides a small introduction to extending the source code for custom analysis.

### Calibration
Data should have been collected in a special calibration run. Using this data, the time tool can be calibrated to allow us to properly convert its readout into an absolute temporal correction for each recorded event. The procedure below outlines how to do this.

**Procedure**:

1. Determine the number corresponding to the timetool calibration run. Timetool calibration runs should be noted in the eLog. If not, either the beam line scientist or person(s) who worked the DAQ controls can tell you if you do not know.
2. Edit the btx YAML file to include parameters for a new task ```calibrate_timetool```. Refer to the [btx introduction](/before/analysis) if you are unclear on which file this is. This task takes three parameters:
    - ```run``` : The number of the calibration run.
    - ```order``` : An integer specifying the order of the polynomial to fit to the data. Second-order (i.e. quadratic) polynomials are typically used at LCLS.
    - ```figs``` : True/False - Diagnostic figures will be generated. This option is case-sensitive - **capitalize** the first letter.
    A complete task entry may look like:
```yaml
calibrate_timetool:
  run: 17
  order: 2
  figs: True
```

!!! danger "Case-sensitivity"
    Certain parameters, such as ```figs``` above, may take case-sensitive values.
    
<ol start=3>
<li>Submit the task using the <code>elog_submit.sh</code> script.</li>
</ol>
```bash
$ elog_submit.sh -c ../yamls/myyaml.yaml -t calibrate_timetool
```
<ol start=4>
<li>Results are stored in two subdirectories of a <code>timetool</code> folder. The timetool folder itself is a subdirectory of the root directory (<code>root_dir</code>) specified in the <code>setup</code> portion of your YAML file. Refer to the btx documentation for more information.</li>
</ol>
```
timetool/
    ├── calib/
    │   ├── DelaysFit_r00{run}.out # Nominal delays for the calibration run
    │   ├── EdgesFit_r00{run}.out # Detected edges used for fitting model
    │   └── r00{run}.out # Model
    └── figs/
     ├── EdgesHist_r00{run}.png # 1D histogram of edges used for fitting the model
     └── TTCalib_r00{run}.png # 2D histogram of nominal delay vs detected edge with model plotted over it
```
Diagnostic figures are stored in the ```figs``` subdirectory. The fitted calibration is stored in the ```calib``` subdirectory in a plain text file named ```r{run}.out``` where ```{run}``` is the number of the calibration run. The model is stored as polynomial coefficients in descending order. This file is what is used for performing timetool corrections to experimental data as explained below.

### Jitter Correction
Once the timetool has been calibrated, a set of model parameters will be stored which can be used to correct the nominal time for any event in actual experimental data runs. An additional task has been defined in order to produce these corrections for you. Similarly to timetool calibration this task is defined in your btx YAML file. Note that you may have run multiple calibration runs throughout your experiment as the alignment can drift over time. Make sure that you are using the most recent calibration for performing corrections. 

!!! danger "Timing Drifts"
    The instrument alignment will drift over time, requiring recalibration. Make sure to always use the most recent calibration model for performing timetool corrections.

**Procedure**:

1. Determine the run, or runs, for which you would like to generate timetool corrections. Any number of contiguous runs can be analyzed in a single task submission, as long as all the runs in the specified range were collected with the **same nominal time delay**.
2. Create a new task, ```timetool_correct```, in the btx YAML file. This task takes four parameters:
    - ```runs``` : A single run or range of runs to correct with the timetool. Ranges should be specified with a hyphen. E.g. 17-21
    - ```nominal_ps``` : The nominal delay in picoseconds.
    - ```model``` : The file location of the timetool calibration model coefficients. If left empty, btx will try to retrieve the most recent model. If none is available it will return the nominal time for each event with an error message in the logs. Refer to [this page](/before/timetool) for information on defining relative paths. Leaving this parameter blank is fine, as long as you have completed a timetool calibration task.
    - ```figs``` : True/False - Diagnostic figures will be generated. This option is case-sensitive - **capitalize** the first letter.


    A completed entry may look like the following:

```yaml
timetool_correct:
  runs: 15-60
  nominal: 1
  model:
  figs: True
```

!!! danger "Case-sensitivity"
    Certain parameters, such as ```figs``` above, may take case-sensitive values.

<ol start=3>
<li>Submit your task using <code>elog_submit.sh</code>.</li></ol>
```bash
$ elog_submit.sh -c ../yamls/myyaml.yaml -t timetool_correct
```
<ol start=4>
<li>Repeatedly submit the task with different sets of runs as needed. Monitor diagnostic figures to ensure that timing is not drifting.</li></ol>

As with calibration, results will be stored in two subdirectories of the ```timetool``` folder. Figures will be stored in the ```figs``` sudirectory. The ```corrections``` subdirectory will contain a file ```r00{run}.out``` where ```{run}``` is the run number, or range of runs, analyzed with a single task submission. Results in this file are stored in a two-column, space-delimited format. Each row of the file contains a timetool correction for a single event. A uniqe event identifier of the format ```seconds-nanoseconds-fiducial``` is followed by the timetool corrected time stamp (i.e. the actual delay).
```
timetool/
├── corrections/
│   └── r00{run}.out
└── figs/
     └── EdgesHist_r00{run}.png
```

[<p align="right">&#8673;Back to Top</p>](#)
