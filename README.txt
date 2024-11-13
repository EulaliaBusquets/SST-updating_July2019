7/11/2024
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



Land-sea mask:
(Applied in both updated and non-updated simulations)

-When the WRF model is initialized with ERA5 reanalysis data, the land-sea mask is created by interpolating the model's direct output to the standard 0.25º grid (from the ERA5 API). This mask has non-binary values and, therefore, does not clearly define the coastline.
-A new land-sea mask (called "land_sea_mask" in WRFX_namelist.wps files) has been created using the ERA5 SST field. Where SST is defined, grids are interpreted as "sea"; otherwise, they are interpreted as "land."
-After creating the land_sea_mask, it must be decoded with the Vtable using ungrib.exe. Additionally, in the METGRID.TBL file, it must be specified that SST is interpolated according to the land_sea_mask.

Changes in METGRID.TBL file:
name=SST
interp_option=sixteen_pt+four_pt+wt_average_4pt+wt_average_16pt+search
masked=land
interp_land_mask = land_sea_mask(1)
interp_water_mask = land_sea_mask(0)
missing_value=-1.E30
fill_missing=0.
fill_lev=200100:SKINTEMP(200100)
flag_in_output=FLAG_SST
========================================
name=LSM_SST ; output_name=LANDSEA
========================================

land_sea_mask and used in the simulations is included at the GitHub repostory ("land_sea_mask" file). 



Sea surface temperature updating:
- Ungrib.exe in WPS module must be executed twice using two variable tables: Firstly decoding all fields exept SST and LANDSEA (with Vtable.ERA5) and using the prefix "ALL" (see "WRF1_namelist_v1.wps"); secondly, decoding only SST field (Vtable.SST) and using the prefix "SST" (see WRF1_namelist_v2.wps").
- For metgrid.exe in WPS module, execute it including "ALL" and "SST" prefixes in "fg_name" field (see "WRF1_HW-namelist_v2.wps"). 
- For real.exe and wrf.exe in WRF module, add/modify the fields "auxinput4_name", "auxinput4_interval", "io_form_auxinput4" from &time_control section of namelist.input file; include the field "sst_updating" from &physics section of namelist.input file (see WRF1_namelist.input). 

Further information and tutorial available at: https://www2.mmm.ucar.edu/wrf/OnLineTutorial/



For RMOL (the inverse of the Obukhov length) extraction: 
-When executing real.exe and wrf.exe in WRF module, add the files "rmol_d0X" in the same directory and add/modify the field "iofioelds_filename" and "ignore_iofields_warning" from &time_control section (see WRF1_namelist.input). 

Further information available at: http://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.0/users_guide_chap5.html#runtimeio


