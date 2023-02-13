# Timing Ultrafast Pulses
If you are performing a pump-probe measurement requiring sub-picosecond time resolution, you will need to correct for the jitter in the X-ray arrival time. LCLS deploys a time tool to facilitate this. The theory of the tool is detailed [here](/before/timetool). Below you will find a step-by-step guide for setting up the tool, and ensuring it is calibrated and running well for your experiment.

## Controls
### Cross-correlation
Before running the time tool it needs to be established that "time zero" at the sample interaction point is the same as the time tool's. The beams on both the timing and pump arms are synchronized; however, the variable path that they travel needs to be accounted for. This accomplished through the use of delay stages. A cross-correlation measurement usually in cerium-yttrium aluminium garnet (Ce-YAG), will be used to confirm that the optical pulses arrive with the same x-ray pulse at both the sample IP and the time tool. The beam-line scientists and your point of contact will be able to confirm what material is available for you. The following section outlines the necessary procedure for performing the cross-correlation.

### Calibration Runs

## Software
If you are running the time tool software it is assumed you have gone through the procedure above to ensure temporal coincidence of your optical pulses. The remainder of this section will be employing [btx](https://www.github.com/lcls-users/btx) for retrieving and interacting with time tool data.

### Calibration
Before the time tool can be employed it must be calibrated. During the process of cross-correlation a "time zero" was determined. This involved setting the position of an optical delay stage. That position is now known as the temporal zero in the DAQ and distances can be set relative to it. By moving the delay stage so that the optical pulse arrives at discrete times before or after the X-ray pulse we can build up a calibration curve. Due to the jitter, for every nominal target time there will be a distribution of actual relative arrival times; however, by targetting a set of nominal delays we can fit a calibration curve through the centroids of these distributions. Again, refer to the theory [here](/before/timetool) if the concept and terminology do not make sense to you.

1. Determine the number corresponding to the time tool calibration run. Either the beam line scientist or person(s) working the DAQ controls can tell you if you do not know.
2. Edit the btx YAML file to include parameters for a new task ```calibrate_timetool```. Refer to the [btx introduction](/before/btxintro) if you are unclear on which file this is. This task takes three parameters:
    - ```run``` : The number of the calibration run.
    - ```order``` : Order of the polynomial to fit to the data.
    - ```figs``` : True/False - Diagnostic figures will be generated.
    A complete task entry may look like:
```yaml
calibrate_timetool:
  run: 17
  order: 2
  figs: True
```
3. Submit the task using the ```elog_submit.sh``` script.
```bash
$ elog_submit.sh -c ../yamls/myyaml.yaml
```

### Running the Timetool
Once the time tool has been calibrated a set of model parameters will be stored which can be used to correct the nominal time for any event in actual experimental data runs. In order to do this a separate task, ```timetool_correct```, is defined in the btx YAML file and submitted to the queue. This task takes four parameters:
```
- ```runs``` : A single run or range of runs to correct with the time tool.
- ```nominal``` : The nominal delay in picoseconds.
- ```model``` : The file location of the time tool calibration model coefficients. If left empty, btx will try to retrieve the most recent model. If none is available it will return the nominal time for each event with an error message in the logs. Refer to [this page](/before/timetool) for information on defining relative paths. Leaving this parameter blank is usually a good choice.
- ```figs``` : True/False - Diagnostic figures will be generated.
```

1. Edit the YAML file. A completed entry may look like the following:

```yaml
timetool_correct:
  runs: 15-60
  nominal: 1
  model:
  figs: True
```

2. Submit your task using ```elog_submit.sh```.
3. Make use of the diagnostic figures to ensure that timing does not drift throughout the experiment.
