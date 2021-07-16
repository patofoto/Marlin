**In platformio.ini change:**

default_envs = STM32F103RET6_creality




**In Configuration.h change:**

#define DEFAULT_AXIS_STEPS_PER_UNIT \
  {                                 \
    80, 80, 405.5, 141.2        \
  }

#define MANUAL_X_HOME_POS -5.5
#define MANUAL_Y_HOME_POS -2.3

#define DEFAULT_Kp 23.79
#define DEFAULT_Ki 1.99
#define DEFAULT_Kd 71.04

#define DEFAULT_bedKp 17.57
#define DEFAULT_bedKi 1.06
#define DEFAULT_bedKd 194.37




**For BLTouch change:**

#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN

#define BLTOUCH

#define NOZZLE_TO_PROBE_OFFSET \
  {                            \
    -40.4, -10.9, -1.76        \
  }

#define AUTO_BED_LEVELING_UBL

// Set the number of grid points per dimension.
#define GRID_MAX_POINTS_X 5

#define Z_SAFE_HOMING

#if ENABLED(Z_SAFE_HOMING)
#define Z_SAFE_HOMING_X_POINT ((X_BED_SIZE - 0) / 2) // X point for Z homing
#define Z_SAFE_HOMING_Y_POINT ((Y_BED_SIZE - 0) / 2) // Y point for Z homing
#endif

// Enable the M48 repeatability test to test probe accuracy
#define Z_MIN_PROBE_REPEATABILITY_TEST


**In Configuration_adv.h change:**

#define BABYSTEP_ZPROBE_OFFSET

#if PROBE_SELECTED && !IS_KINEMATIC
#define PROBING_MARGIN_LEFT 0
#define PROBING_MARGIN_RIGHT 48
#define PROBING_MARGIN_FRONT 5
#define PROBING_MARGIN_BACK 15
#endif

#define ADVANCED_PAUSE_FEATURE
#define FILAMENT_LOAD_UNLOAD_GCODES  







**Slicer Code**

**Start G-Code**
; Ender 3 Custom Start G-code
G92 E0 ; Reset Extruder
G28 ; Home all axes

;M420 S1 ; Get and/or set bed leveling state

M420 L1 ; Load the mesh stored in slot 1 (from G29 S1)
G29 J   ; No size specified on the J option tells G29 to probe the specified 3 points
        ; and tilt the mesh according to what it finds.

G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X0.1 Y20 Z0.3 F5000.0 ; Move to start position
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; Move to side a little
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line
G92 E0 ; Reset Extruder
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X5 Y20 Z0.3 F5000.0 ; Move over to prevent blob squish


**End G-Code**
G91 ;Relative positioning
G1 E-2 F2700 ;Retract a bit
G1 E-2 Z0.2 F2400 ;Retract and raise Z
G1 X5 Y5 F3000 ;Wipe out
G1 Z10 ;Raise Z more
G90 ;Absolute positioning

G1 X0 Y210 ;Present print
M106 S0 ;Turn-off fan
M104 S0 ;Turn-off hotend
M140 S0 ;Turn-off bed

M84 X Y E ;Disable all steppers but Z





**OctoPrint Settings**

**BLTouch Probing G Code**
G28       ; home all axes
M155 S30  ; reduce temperature reporting rate to reduce output pollution
M190 S60  ; (optional) wait for the bed to get up to temperature
G29 P1    ; automatically populate mesh with all reachable points
G29 P3    ; infer the rest of the mesh values
G29 P3    ; infer the rest of the mesh values again
@BEDLEVELVISUALIZER	; tell the plugin to watch for reported mesh
M420 S1 V ; enabled leveling and report the new mesh
M500      ; save the new mesh to EEPROM
M155 S3   ; reset temperature reporting
M300 S1000 P500 ; chirp to indicate bed mesh leveling is done and saved