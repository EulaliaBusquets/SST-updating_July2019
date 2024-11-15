15/11/2024
Information and namelists used for WRF July 2019 simulations in Manuscript Busquets et al. 2024:

WRF0 == Without updating SST
WRF1 == Updating SST

The namelists with the modifications commented for SST updating and RMOL extraction are "WRF1_namelist_v1.wps", "WRF1_namelist_v2.wps" and "WRF1_namelist.input". 

Modelling strategy:
For each period, 3 simulations of 48h were run, and data were collected from 24 to 48 hours. 
For example: 
- Simulation of 24/7/2019: 
	Start time: 23/7/2024 00:00
	End time:   25/7/2024 00:00
	Data used:  From 24/7/2024 00:00 to 24/7/2024 23:00



Sea surface temperature updating:
- Ungrib.exe in WPS module must be executed twice using two variable tables: Firstly decoding all fields exept SST and LANDSEA (with Vtable.ERA5) and using the prefix "ALL" (see "WRF1_namelist_v1.wps"); secondly, decoding only SST field (Vtable.SST) and using the prefix "SST" (see WRF1_namelist_v2.wps").
- For metgrid.exe in WPS module, execute it including "ALL" and "SST" prefixes in "fg_name" field (see "WRF1_HW-namelist_v2.wps"). 
- For real.exe and wrf.exe in WRF module, add/modify the fields "auxinput4_name", "auxinput4_interval", "io_form_auxinput4" from &time_control section of namelist.input file; include the field "sst_updating" from &physics section of namelist.input file (see WRF1_namelist.input). 

Further information and tutorial available at: https://www2.mmm.ucar.edu/wrf/OnLineTutorial/



For RMOL (the inverse of the Obukhov length) extraction: 
-When executing real.exe and wrf.exe in WRF module, add the files "rmol_d0X" in the same directory and add/modify the field "iofioelds_filename" and "ignore_iofields_warning" from &time_control section (see WRF1_namelist.input). 

Further information available at: http://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.0/users_guide_chap5.html#runtimeio


