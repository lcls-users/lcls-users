# Running the Timetool
If you are performing a pump-probe measurement requiring sub-picosecond time resolution, you will need to correct for the jitter in the X-ray arrival time. LCLS deploys a time tool to facilitate this. The theory of the tool is detailed [here](/before/timetool). Below you will find a step-by-step guide for setting up the tool, and ensuring it is calibrated and running well to ensure reliable jitter correction for your experiment.

This page, similarly to others, is divided into two sections, [**Setup**](#setup) and [**Analysis**](#analysis). The **Setup** section of the page contains information on the procedure to perform optical pulse/X-ray pulse cross-correlation for establishing "time-zero" - when both pulses arrive at the sample simultaneously - as well as for collecting the dataset used for time tool calibration. The **Analysis** section details how to analyze the calibration dataset to establish a jitter correction model, as well as how to apply that model to create accurate time stamps for events corresponding to experimental data.

##  Setup {: #setup}

### Cross-correlation
Before running the time tool it needs to be established that "time zero" at the sample interaction point is the same as the time tool's. The beams on both the timing and pump arms are synchronized; however, the variable path that they travel needs to be accounted for. This is accomplished through the use of delay stages. A cross-correlation measurement usually in cerium-yttrium aluminium garnet (Ce-YAG), will be used to confirm that the optical pulses arrive with the same x-ray pulse at both the sample IP and the time tool. The beam-line scientists and your point of contact will be able to confirm what material is available for you. The following section outlines the necessary procedure for performing the cross-correlation.

**Procedure**:

(TBA with Controls)

### Calibration Runs
Before the time tool can be employed it must be calibrated. During the process of cross-correlation a "time zero" was determined. This involved setting the position of an optical delay stage. That position is now known as the temporal zero in the DAQ, and distances can be set relative to it. By moving the delay stage so that the optical pulse arrives at discrete times before, or after, the X-ray pulse we can build up a calibration curve. Due to the jitter, for every nominal target time there will be a distribution of actual relative arrival times; however, by targetting a set of nominal delays we can fit a calibration curve through the centroids of these distributions. Refer to the theory [here](/before/timetool) if the concept and terminology are unfamiliar to you. Below you will find the procedure for setting up a run to collect the necessary calibration data. Fitting the calibration curve will be covered in the following section, under **Analysis**.

**Procedure**:

(TBA Thursday 2023/03/02)

![Timetool - DAQ control interface](./material/DAQ_control_TT.png)
*DAQ control interface to the Timetool. In the `DAQ Control` panel, (1) click `Edit`, (2) click `OPAL1K_1` or some equivalent device name, (3) click on `TimeToolConfig`. This will open the window on the left.*

![Timetool - DAQ partition interface](./material/DAQ_select_TT.png)
*DAQ control interface to the partition. To select what is recorded in the DAQ partition, (1) click `Select`, (2) click `opal_tt` to record the timetool raw image data.*

[<p align="right">&#8673;Back to Top</p>](#)

## Analysis {: #analysis}

If you are running the time tool software it is assumed you have gone through the procedure above to ensure temporal coincidence of your optical pulses. The remainder of this section will be employing [btx](https://www.github.com/lcls-users/btx) for retrieving and interacting with time tool data.

### Calibration
In the second part of the **Setup** section, data was collected in a special calibration run. Using this data, the time tool can be calibrated to allow us to properly convert its readout into an absolute temporal correction for each recorded event. The procedure below outlines how to do this.

**Procedure**:

1. Determine the number corresponding to the time tool calibration run. Either the beam line scientist or person(s) working the DAQ controls can tell you if you do not know.
2. Edit the btx YAML file to include parameters for a new task ```calibrate_timetool```. Refer to the [btx introduction](/before/btxintro) if you are unclear on which file this is. This task takes three parameters:
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
    Certain parameters, such as ```figs``` above may take case-sensitive values.
    
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

### Running the Timetool
Once the time tool has been calibrated, a set of model parameters will be stored which can be used to correct the nominal time for any event in actual experimental data runs. An additional task has been defined in order to produce these corrections for you. Similarly to timetool calibration this task is defined in your btx YAML file. Note that you will likely need to run multiple calibration runs as the timing will drift over the course of your experiment. Make sure that you are using the most recent calibration for performing corrections. 

!!! danger "Timing Drifts"
    The instrument timing will drift over the course of your experiment. Make sure to frequently calibrate the timetool and only use recent calibration models for performing timetool corrections.

**Procedure**:

1. Determine the run, or runs, for which you would like to generate timetool corrections. Any number of contiguous runs can be analyzed in a single task submission, as long as all the runs in the specified range were collected with the **same nominal time delay**.
2. Create a new task, ```timetool_correct```, in the btx YAML file. This task takes four parameters:
    - ```runs``` : A single run or range of runs to correct with the time tool. Ranges should be specified with a hyphen. E.g. 17-21
    - ```nominal_ps``` : The nominal delay in picoseconds.
    - ```model``` : The file location of the time tool calibration model coefficients. If left empty, btx will try to retrieve the most recent model. If none is available it will return the nominal time for each event with an error message in the logs. Refer to [this page](/before/timetool) for information on defining relative paths. Leaving this parameter blank is a fine choice, as long as you have completed a timetool calibration task.
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
    Certain parameters, such as ```figs``` above may take case-sensitive values.

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
