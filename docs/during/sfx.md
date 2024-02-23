# SFX analysis toolkit {: #top}

<a name="toc"></a> **Jump to:**
- [`Geometry from AgBe run`](#btx-geom)
- [`Running Cheetah`](#cheetah)
- [`Running Crystfel`](#crystfel)

---
## Getting geometry from silver behenate run {: #btx-geom}

### Setup `btx`
Ask your instrument scientist or data POC to setup `btx` in the eLog. Make sure that the `geometry_agbe` workflow is defined in the Workflow Definition tab as in the figure below.

|                                                                        ![BTX workflow definition in the eLog](images/btx-def.png)                                                                        | 
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:| 
|                                                                                 __BTX workflow definition in the eLog.__                                                                                 |

### Trigger Geometry optimization workflow in the eLog

Click on the Workflow Controls tab and trigger the workflow for the desired run.

| ![BTX workflow controls from the eLog](images/btx-controls.png) | 
|:---------------------------------------------------------------:| 
|            __BTX workflow controls from the eLog.__             |

### Monitor results

The measured distance between sample and detector will eventually be reported in the Workflow controls tab and fitting plots will can be found in the "Summaries" page (go to ***runs > rXXXX*** where XXXX is the run number).

---
## Running Cheetah {: #cheetah}

### Launch Cheetah

0. ssh to psana interactive node on S3DF.
1. go to central installation directory: `cd /sdf/group/lcls/ds/tools/om`
2. source cheetah environment: `source setup-cheetah.sh`
3. launch the GUI: `cheetah_gui.py`

### Setup experiment

1. when prompted, create new experiment or open existing experiment by pointing to your experiment's scratch folder (e.g. `/sdf/data/lcls/ds/<instrument>/<experiment>/scratch`)
2. go to ***File > Unlock command operations***
3. go to ***File > Start crawler***
4. Profit!

| ![Cheetah](images/cheetah.png) | 
|:------------------------------:| 
|          __Cheetah.__          |


---
## Running Crystfel {: #crystfel}

### Launch Crystfel

0. ssh to psana interactive node on S3DF.
1. load the crystfel module: 
```bash
export MODULEPATH=$MODULEPATH:/sdf/group/lcls/ds/tools/crystfel/modulefiles
module load crystfel/0.10.2
crystfel
```

### Setup experiment

Load data and point to the run folder under Cheetah's hdf5 directory. Also point to the Cheetah geometry file.

| ![Crystfel](images/crystfel.png) | 
|:--------------------------------:| 
|          __Crystfel.__           |
