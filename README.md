# Gio's Gantry Twist Utility

Klipper module to visualize and adjust gantry twist for printers running the Beacon Eddy Current Surface Scanner

## Overview

This module has 2 modes: analysis and compensation.

Analysis mode samples the bed and generates 3 dashboards with various graphs to help diagnose gantry alignment issues by visualizing the Z-offset measurements from Beacon's contact and proximity modes. It leverages the `BEACON_OFFSET_COMPARE` command to calculate deltas across your print bed.

Ideally, the delta between contact and proximity measurements should be zero (or at least consistent) across the bed. Variations indicate that the Beacon probe and nozzle are operating on different planes, leading to probing inaccuracies and first-layer issues.

Compensation mode works out and applies the Z compensation values for Klipper's native gantry twist compensation module `[axis_twist_compensation]`.

### Requirements

- Klipper
- Beacon probe
- SSH access to your printer
- A printer with internet access (only if using the recommended installation script below)

### Compatibility

This module can work with any printer running Klipper but was designed and tested on a QIDI Plus 4. As such, the installation guide and the default settings are tailored for it. If you install/run on a different printer, please review the installation and settings carefully.

## Installation

Access your printer via SSH to execute the following parts

### Backup your configuration and klipper objects

> [!CAUTION]
> Be sure to update the value for `reason`, or else you might be overwriting a previous backup.

On a command shell (`ssh`) to the printer, run the following

```bash
date=$(date +'%Y%m%d')
reason='beforegiosgantrytwist'
mkdir -p /home/mks/qidi-klipper-backup-${date}-${reason}
(cd /home/mks; tar cvf - klipper printer_data/config) | (cd /home/mks/qidi-klipper-backup-${date}-${reason}; tar xf -)
```

So you have something to fall back to if something fails.

### Install Gantry Twist Utility

 1. Install the python modules needed

```bash
cd /home/mks && wget -O - https://raw.githubusercontent.com/omgitsgio/gios_gantry_twist_utility/62a083f4c8356c34b2a8c6e02d26e93530addf10/install.sh | bash
```

> [!NOTE]
> This procedure requires `sudo` access, you will need to have the password for user `mks` at your disposal (if you use certificate based SSH access for instance).

<details>
 
<Summary>Logging example</Summary>

A successful install looks like this:

```log
mks@mkspi:~/printer_data/config/CustomMacros$ cd /home/mks && wget -O - https://raw.githubusercontent.com/omgitsgio/gios_gantry_twist_utility/62a083f4c8356c34b2a8c6e02d26e93530addf10/install.sh | bash
--2026-02-22 11:09:01--  https://raw.githubusercontent.com/omgitsgio/gios_gantry_twist_utility/62a083f4c8356c34b2a8c6e02d26e93530addf10/install.sh
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.109.133, 185.199.111.133, 185.199.108.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3772 (3.7K) [text/plain]
Saving to: ‘STDOUT’

-                                                    100%[======================================================================================================================>]   3.68K  --.-KB/s    in 0s

2026-02-22 11:09:01 (15.5 MB/s) - written to stdout [3772/3772]


=============================================
- Gantry Twist Utility module install script -
=============================================

[sudo] password for mks:
[PRE-CHECK] Klipper service found! Continuing...

libopenblas-dev is already installed
libatlas-base-dev is already installed
[DOWNLOAD] Downloading Gio's Gantry Twist Utility module repository...
Cloning into 'gantry_twist_utility'...
remote: Enumerating objects: 55, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 55 (delta 0), reused 0 (delta 0), pack-reused 54 (from 1)
Unpacking objects: 100% (55/55), done.
[DOWNLOAD] Download complete!

[SETUP] Installing/Updating Gantry Twist Utility dependencies...
Requirement already satisfied: pip in ./klippy-env/lib/python3.7/site-packages (24.0)
Requirement already satisfied: matplotlib in ./klippy-env/lib/python3.7/site-packages (from -r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (3.5.3)
Requirement already satisfied: numpy in ./klippy-env/lib/python3.7/site-packages (from -r /home/mks/gantry_twist_utility/requirements.txt (line 2)) (1.21.6)
Requirement already satisfied: scipy in ./klippy-env/lib/python3.7/site-packages (from -r /home/mks/gantry_twist_utility/requirements.txt (line 3)) (1.7.3)
Requirement already satisfied: cycler>=0.10 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (0.11.0)
Requirement already satisfied: fonttools>=4.22.0 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (4.38.0)
Requirement already satisfied: kiwisolver>=1.0.1 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (1.4.5)
Requirement already satisfied: packaging>=20.0 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (24.0)
Requirement already satisfied: pillow>=6.2.0 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (9.5.0)
Requirement already satisfied: pyparsing>=2.2.1 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (3.1.4)
Requirement already satisfied: python-dateutil>=2.7 in ./klippy-env/lib/python3.7/site-packages (from matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (2.9.0.post0)
Requirement already satisfied: typing-extensions in ./klippy-env/lib/python3.7/site-packages (from kiwisolver>=1.0.1->matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (4.7.1)
Requirement already satisfied: six>=1.5 in ./klippy-env/lib/python3.7/site-packages (from python-dateutil>=2.7->matplotlib->-r /home/mks/gantry_twist_utility/requirements.txt (line 1)) (1.17.0)

[INSTALL] Linking Gantry Twist Utility module to Klipper extras
[POST-INSTALL] Restarting Klipper...
[POST-INSTALL] Restarting Moonraker...
mks@mkspi:~$
```
</details>

 2. Create a new directory and/or a new configuration file

**Directory**
```bash
cd /home/mks/printer_data/config
mkdir -p custom
```

**File** 

Create file `custom/GiosGantryTwist.cfg` for example, taken from [this discord message](https://discord.com/channels/1184400034641477722/1435048301597556796/1444830142235938816):
```cfg
[gantry_twist_utility]
## Operation mode (0 = analysis, 1 = compensation):
# mode: 0

# graphs_folder: ~/printer_data/config/Gantry_twist_analysis

## Mesh boundaries to probe. When in compensation mode, X values will be used for start_x and end_x.
# min_x: 22.0
# max_x: 283.0
# min_y: 22.0
# max_y: 283.0
# calibrate_y: 152.5

## Points per axis (grid_size * grid_size).
## When in compensation mode, this will be the sample size along X-axis.
grid_size: 5

## Test temps
bed_temp: 0
hotend_temp: 0

#  Extra delay at each point to avoid out-of-range samples.
point_delay: 2

## Retries for a point when BEACON_OFFSET_COMPARE fails (out-of-range).
## Edge points can be unstable; 5+ recommended.
## Failed points are non-fatal; analysis continues.
max_retries: 10

[axis_twist_compensation]
## The speed (in mm/s) of non-probing moves during the calibration.
#X The default is 50.
speed: 50

## The height (in mm) that the head should be commanded to move to
## just prior to starting a probe operation. The default is 5.
horizontal_move_z: 5

## Defines the minimum X coordinate of the calibration
## This should be the X coordinate that positions the nozzle at the starting
## calibration position.
calibrate_start_x: 20

## Defines the maximum X coordinate of the calibration
## This should be the X coordinate that positions the nozzle at the ending
## calibration position.
calibrate_end_x: 280

## Defines the Y coordinate of the calibration
## This should be the Y coordinate that positions the nozzle during the
## calibration process. This parameter is recommended to
## be near the center of the bed
calibrate_y: 152.5
```

> [!TIP]
> For more configuration options please refer to [sample_config_complete.cfg](sample_config_complete.cfg).

 3. Include the custom configuration file into `printer.cfg` file with the rest, at the start of the file.
 
```gcode
[include ./custom/GiosGantryTwist.cfg] 
```

When you're done editing, be sure to commit the changes to the filesystem

```bash
sync
```

and then power-cycle your printer.

> [!WARNING]
> Power-cycle means to turn your printer off at the power switch, wait 10s, and then turn it back on. It does **NOT** mean just save and restart in the Fluidd webUI.  

### Cleanup

The install script is no longer needed, so you can remove it. Through SSH run:

```
rm -f /home/mks/install.sh
```

## Usage

Just send to your console:

```
GANTRY_TWIST_UTILITY
```

If no settings are declared, it will run with the config above by default (hence in analysis mode).

You can specify some arguments from console which will override the config file:
```
MODE, BED_TEMP, HOTEND_TEMP, GRID_SIZE, MAX_RETRIES, CALIBRATE_Y
```

### Compensation Mode

To automatically calculate and apply axis twist compensation values:

```
GANTRY_TWIST_UTILITY MODE=1
```

This will:
1. Sample along the X-axis at the center Y position as per config
2. Calculate compensation values
3. Update your `[axis_twist_compensation]` configuration
4. Prompt you to run `SAVE_CONFIG` to persist the changes

### Save and restart Klipper

Run the `SAVE_CONFIG` command in the Klipper console. This will restart Klipper and add a section at the bottom of the `printer.cfg` with `#*#` prefixes on the lines.

> [!NOTE]
> This written-to-file configuration is now applied to _every_ print in the future, until you either a) add the command to the `PRINT_START` macro (further below) or b) run it once manually again, including the `SAVE_CONFIG`.

<details>

<Summary>Example of a configuration</Summary>

```
#*# [axis_twist_compensation]
#*# z_compensations = -0.032596, -0.039812, -0.032902, -0.037653, -0.043206, -0.041615, -0.053408, -0.060687, -0.055672, -0.063146
#*# compensation_start_x = 22.0
#*# compensation_end_x = 283.0
```
 
</details>

### Analysis Mode

This mode will probe the bed as per settings/arguments and run 3 dashboards to help visualize the offset data.

Please note that this is all experimental and it's not an exact science, but it has helped me understand that in my case axis twist compensation would have been sufficient to obtain decent results.

### Output Interpretation

The analysis generates three visualization images that help you understand your gantry's twist characteristics:

#### 1. beacon_offset_analysis_3d_meshes_overlay
Shows overlaid contact and proximity meshes from four different angles, helping you visualize the difference between the nozzle and probe perspectives.

![3D Meshes Overlay](docs/beacon_offset_analysis_3D_meshes_overlay_20251031_234553.png)

#### 2. beacon_offset_analysis_3d_meshes&offset
Displays:
- Two separate mesh graphs (contact and proximity)
- An overlay view at 45° angle
- A 3D visualization of the deltas (bottom right)

![3D Meshes & Offset](docs/beacon_offset_analysis_3D_meshes&offset_20251031_234553.png)

#### 3. beacon_offset_analysis
The top-left plot immediately reveals patterns in the extent of the delta values, making it easy to identify bias and twist characteristics.

The X and Y Trend Analysis plots on the right display delta values along their respective axes. The script highlights statistically significant trends and ranges exceeding 50µm. In the example below, note the clear sloping trend on the X-axis and the wide ranges on the Y-axis. While this indicates aggressive gantry twist, the consistent, symmetrical X-axis pattern is ideal for Klipper's X-axis compensation.

The bottom graph in the middle groups the measurements by row or column (depending on the sampling direction) and can help confirm offset patterns.

![Beacon Offset Analysis](docs/beacon_offset_analysis_20251031_234553.png)

### Understanding Gantry Twist Impact on First Layer

Gantry twist directly affects first-layer quality by creating inconsistent Z-offsets across the bed. The following are good examples of some patterns that may occur:

#### X-Axis Twist Pattern
When your gantry exhibits twist along the X-axis, you'll see variation in the offset deltas as you move left-to-right across the bed. This is the most common twist pattern on CoreXY printers.

![X-Axis Beacon Offset First Layer](docs/x_beacon_offset_first_layer.png)

#### XY Combined Twist Pattern
More complex twist patterns can occur across both X and Y axes, creating diagonal or corner-biased variations that are harder to compensate for mechanically. Generally speaking, you can reduce or remove the effect of one of the axis twists by choosing a beacon/probe mount with zero X or Y offset.

![XY Combined Beacon Offset First Layer](docs/xy_beacon_offset_first_layer.png)

#### X-Axis Twist with Compensation
When gantry twist compensation is applied, the first layer becomes more consistent, but the underlying mechanical issue remains.

![X-Axis with Gantry Twist Compensation](docs/x_beacon_offset_gTwist_first_layer.png)

## Compensation mode

This mode actually alters the functioning of the printer. It takes the analysis and applies (exact opposite) counterbalance so to speak.

The command stays the same, but a parameter `MODE=1` needs to be added (compensation mode), instead of no parameter (which defaults to `0`, or analysis mode). 

Every print will apply the grantry twist compensation. Becuse

### Adjust PRINT_START macro if you want to

Running the command with `MODE=1`, combined with `SAVE_CONFIG` will permanently store the configuration at that point, and will persist through reboots. However, if you swap plates a lot, or your print at different temperatures a lot, or if you use a new plate once, you _MAY_ want to decide that you want to create a one-off configuration for only just this print. Or just do it every print.

You will have to execute the `MODE=1` command, and _NOT_ also do `SAVE_CONFIG`. This needs to be done after G29 (getting the Z=0 values), and after a plate heatsoak if you do that.

an example [taken from this discord message](https://discord.com/channels/1184400034641477722/1435048301597556796/1444840906439262278):

```
M190 S{bedtemp}                                                # Wait for print bed to reach target temperature
M104 S140                                                      # Set nozzle to 140 so any remaining filament stuck to nozzle is softened
M109 S140                                                      # Wait untill nozzle reaches 140C
M118 Performing gantry twist utility...                        # Put text in the Console
GANTRY_TWIST_UTILITY MODE=1 BED_TEMP={bedtemp} GRID_SIZE=5     # Apply the Gantry twist module on the bed temperature and a low grid size.
```

Which does:

 1. Heat bed to bed temperature
 2. Set nozzle to cool down to 140
 3. Wait for the nozzle to cool down to 140.
 4. Echo into the console that you are performing the gantry twist
 5. Perform the gantry twist, with compensation active, at the bed temperature, and check 5 points

<details>

<Summary>A more detailed example</Summary>

Please note that `M118` is replaced by `RESPOND MSG=` as that's Qidi's style.

Do **NOT** use this verbatim, as you will not have the full macro and variables in place to leverage `soak_vars` pieces!!
  
```
    G0 X5 Y5 F6000                      # Move print head to front-left in case of any oozing
    M104 S140                           # Set nozzle to 140 so any remaining filament stuck to nozzle is softened
    M190 S{bedtemp}                     # Wait for print bed to reach target temperature

    {% if filament_type != "PLA" and filament_type != "PETG" and filament_type != "TPU" %}
      {% if bedtemp > 75 %}
        M104 S0                             # Ensure hotend is fully off to minimise any oozing before headsoaking the bed
        RESPOND MSG="Bed Temp Target higher than 75C, soaking"
        G4 P{soak_vars.soaktime_initial} ; Soak for 10 minutes (600000 miliseconds)
        RESPOND MSG="Regular soaking ended"
        {% if curr_bed_type == "Engineering Plate" %}
           RESPOND MSG="Bed type is Engineering, soaking some more!"
           G4 P{soak_vars.soaktime_extra} ; Extra soak 20 minutes for Darkmoon CFX Plate
           RESPOND MSG="Soaking some more ended"
        {% endif %}
      {% endif %}
    {% endif %}

    M109 S140                           # Wait untill nozzle reaches 140C to do the tapping on the plate
    G29                                 # Perform Z-offset, and bed meshing measurements
    RESPOND MSG="Performing gantry twist utility"
    GANTRY_TWIST_UTILITY MODE=1 BED_TEMP={bedtemp} GRID_SIZE=5 # Apply the Gantry twist module on the bed temperature and a low grid size.
```
</details>

After this you can start a print like any time before, it will just take a little bit more time to commence.

<details>

<Summary>A snippet of logging</Summary>

```
14:33:47 echo: Nozzle cleared
14:33:48 echo: Nozzle cooled
14:46:47 echo: Bed Temp Target higher than 75C, soaking
14:51:46 echo: Regular soaking ended
14:52:00 // Run Current: 0.69A Hold Current: 0.69A
14:52:01 // Run Current: 1.15A Hold Current: 1.15A
......[[omitted]]......
14:54:55 // Sampled 60420 total points over 2 runs
14:54:55 // Samples binned in 196 clusters
14:54:57 // Mesh calibration complete
14:54:57 // Bed Mesh state has been saved to profile [kamp]
// for the current session.  The SAVE_CONFIG command will
// update the printer config file and restart the printer.
14:54:57 echo: Performing gantry twist utility
14:54:57 // ============================================================
14:54:57 // GANTRY TWIST UTILITY
14:54:58 // Mode: Compensation
14:54:58 // ============================================================
14:54:58 // Heating bed to 95.0°C and hotend to 0.0°C
14:55:56 // Homing all axes...
......[[omitted]]......
14:58:19 // ============================================================
14:58:19 // Axis compensation points collection complete: 5/5 points successful
14:58:19 // ============================================================
14:58:19 // Returning to center...
14:58:23 // Turning off heaters...
14:58:23 // Computing axis twist compensation values...
14:58:23 // Applying axis twist compensation...
14:58:23 // New compensation range: start_x=22.0, end_x=283.0
14:58:23 // New Z compensations: [0.0007931287281038243, 0.0014428648435697903, -0.002179701558722112, -0.01839409542842847, -0.022579158086106672]
14:58:23 // Axis twist compensation applied successfully. Please SAVE_CONFIG to apply changes.
14:58:50 // Filament width sensor Turned On
14:58:50 // Filament width measurements cleared!
14:58:50 // Filament dia (measured mm): 1.8999999999999995
14:58:50 echo: Last File: first_layer_test_ABS_22m12s.gcode
14:58:54 // Moving filament tip 0.0mms
14:58:54 // KAMP purge starting at -0.93201, 260.0 and purging 30.0mm of filament, requested flow rate is 4.0mm3/s.
14:59:15 // pressure_advance: 0.032000
// pressure_advance_smooth_time: 0.030000
15:22:33 // Setting Filament Offset to -0.090mm
15:22:33 // Filament width sensor Turned Off
15:22:36 Done printing file
```
 
</details>

## Uninstall

 1. Remove the `include` line in `printer.cfg` file
 2. Remove then `[axis_twist_compensation]` section in the `SAVE_CONFIG` part in the `printer.cfg` file, lines that start with  `#*#`
 3. Remove the file `custom/GiosGantryTwist.cfg`
 4. Remove the directory `/home/mks/gantry_twist_utility`

Power-cycle the printer as per step 3 in the installation guide.

## Current Status & Future Plans

### Stability
The script is reliable with default settings or minor variations of them. However, it hasn't been extensively stress-tested with edge cases and misuse, so be careful if you change the defaults.

### Future Improvements
The analysis part is very rudimentary. Suggestions are greatly appreciated.
Potential improvements include:
- Enhanced console output with twist detection alerts
- Automated analysis of twist patterns and severity
- Improved graph layouts and visual storytelling
- Additional visualization types for clearer data interpretation

## Credits
A lot of the problem-solving was possible by taking inspiration from <https://github.com/Frix-x/klippain-shaketune>.

The install script was mostly a readaptation of <https://github.com/qidi-community/ShakeTune-For-Plus4/blob/cd94df7b4b2ac4f6a56c27dfaf9f40dbff463335/install.sh>.

## Support
Please refer to the QIDI community on GitHub: <https://github.com/qidi-community> or join the Discord channel: <https://discord.gg/FtDkxKsX>. Feel free to also contact me on GitHub.
