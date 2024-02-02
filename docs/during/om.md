!!! note "Quicklink"
    - **OM** - [Official documentation][1]

# OM - Realtime Experiment Monitoring {: #top}

---
## Running OM {: #lcls}

!!! note "Instructions may change"
    The instructions below are valid as of LCLS Run 22 (first half of calendar year 2024). Some details of the setup may change slightly in the near future. Refer back to this page at the beginning of each run for potential updates.

### Prepare workspace {: #prepare_workspace}

(This section should take no more than 5-10 minutes)

At the beginning of your first shift you need to prepare OM's working directory. This requires access to the operator account for the hutch you will be running the experiment at (`mfxopr`, `cxiopr`, ...). The instructions below will be done from the perspective of `mfxopr`. You may need to subsitute the correct operator or machine names for your hutch to run the commands. The full set of commands without explanatory text can be copied at the end of this block - names will still need to be edited.

!!! note "Skip Ahead"
    If OM was requested ahead of time it is possible that these steps would have been followed by beamline staff or a member of the data systems team. In this case you can skip directly to [launching OM](#launch_om) below. Note that it may still be a good idea to update geometry or mask files.

1. SSH into `mfx-monitor` with the operator account: `ssh mfxopr@mfx-monitor`. If you are using a terminal on a hutch control room machine you can simply use: `ssh mfx-monitor`
    - If you are not using a hutch machine you may need to do this in two steps. Note you must have special permission to login as `mfxopr`. If you do not, please use a control room terminal.
        1. `ssh mfx-monitor`
        2. `ssh mfxopr@mfx-monitor`
    - `mfx-monitor` is the default machine for launching OM; however, confirm with beamline staff if it is preferable to use `mfx-daq`.
2. Move to the OM directory: `cd ~mfxopr/OM-GUI`
3. Prepare the working directory by copying the template - replace replace `<exp_name>` with your experiment name, e.g. `mfxx1001122`:
    - `cp -r template_2023 <exp_name>`
4. Change to the workspace directory. If using an `ePix10k2M` detector use `<exp_name>/om-workspace`. If using a `Rayonix` detector use `<exp_name>/om-workspace-rayonix`
     - `cd om-workspace[-rayonix]`
5. There is a geometry (`.geom`) and a mask (`.h5`) file in this workspace directory. Update these files to the most recent versions. Ask beamline staff for where that may be located. If you need to perform any conversions see notes at the bottom of this [page](#gotchas).
     - `cp <new_geom>.geom epix10k2M.geom # Copy new geometry to replace epix10k2M.geom or rayonix.geom`
     - `cp <new_mask>.h5 epix10k2M.h5 # Copy new mask to replace epix10k2M.h5 or rayonix.h5`
6. Modify the `run_om.sh` script in your workspace directory to use the proper machines for OM to run on.
     - By default, OM will use `daq-mfx-mon02`, `daq-mfx-mon03`, `daq-mfx-mon04`, and `daq-mfx-mon05` to run on. Confirm with beamline staff that this is okay. Check where other things are running. Use `wherepsana -d` to see machines.
     - If you need to change the machines, modify the very last line of the script: `--host daq-mfx-mon02,daq-mfx-mon03,daq-mfx-mon04,daq-mfx-mon05 $(pwd)/monitor_wrapper.sh`. **Note: There is no space between the commas and the hostnames.**

<details>
    <summary> Combined commands </summary>
SSH to appropriate machine as operator

```bash
ssh mfxopr@mfx-monitor # if on control room terminal simply ssh mfx-monitor
# If the above doesn't work, try it in two steps
# ssh mfx-monitor
# ssh mfxopr@mfx-monitor
```

Setup the working directory - example for `rayonix` detector. Fill in `EXPNAME` where appropriate. Ask beamline staff for the locations of geometry files and masks.

```bash
cd ~mfxopr/OM-GUI
EXP_NAME=MYEXP1234 # REPLACE with experiment name
cp -r template_2023 $EXP_NAME
cd om-workspace-rayonix
cp <geom_file_location> rayonix.geom
cp <mask_file_location> rayonix.h5
nano run_om.sh # Double check the last line for appropriate machines.
```
</details>

### Launching OM {: #launch_om}
Once the folder has been appropriately setup, there are a number of processes which need to be launched.

1. SSH back to the appropriate machine. As above, this is usually `mfx-monitor`; however, it may be `mfx-daq` depending on what the beamline staff say.
2. Launch the monitor process: `screen ~mfxopr/OM-GUI/<exp_name>/om-workspace[-rayonix]/run_om.sh`
    - The use of `screen` is preferred. This will allow other people to reattach to the session from another terminal to e.g. restart the monitor if there are any issues.
    - To reattach to the session use `screen -xr`. Do **NOT** use `-dr` -> This will detach all clients.
3. Launch the main GUI elements:
    - Main GUI: `./~mfxopr/OM-GUI/<exp_name>/om-workspace[-rayonix]/run_gui.sh`
    - Frame Viewer: `./~mfxopr/OM-GUI/<exp_name>/om-workspace[-rayonix]/run_frame_viewer.sh`
4. Optionally, start the parameter tweaker. This is only recommended for advanced users with extensive experience with the algorithm and the meaning of relevant parameters.
    - `./~mfxopr/OM-GUI/<exp_name>/om-workspace[-rayonix]/run_parameter_tweaker.sh`
    - Note, making modifications using the GUI interface of the parameter tweaker will NOT update the monitor process.
    - To update the parameters, after a more optimal set has been found using the GUI interface, copy the parameters into the appropriate locations in the `monitor.yaml` file. This is located in the same directory as all the other scripts to launch OM components. The monitor process must be restarted.
---

## Potential Issues {: #gotchas}
### Geometry
Geometry conventions lead to no shortage of potential issues at LCLS. Please pay careful attention to the formats you are using with OM.
!!! danger "Converting geometries"
    If you need to create a new mask be aware that OM and `psana` use different formats and conventions. In order to convert a `psana` mask into an OM mask first:

    1. Flatten the first coordinate of the array. E.g. An array of shape `8x16x16` becomes an array of shape `128x16`.
    2. For OM, a value of 1 is a pixel which we want to keep, and a value of 0 is a pixel we want to mask.

[1]: https://www.ondamonitor.com/html/index.html

[*(back to top)*](#top)
