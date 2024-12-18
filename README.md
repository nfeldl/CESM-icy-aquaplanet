# CESM2-CAM6 aqupalanet with thermodynamic sea ice 

## Citation
If you use this model configuration, please cite the following paper:
England, M. R., N. Feldl, and I. Eisenman (2024), Sea ice perturbations in aquaplanet simulations: Isolating the physical climate responses from model interventions, *Environmental Research: Climate*, [doi:10.1088/2752-5295/ad9b45](https://doi.org/10.1088/2752-5295/ad9b45).

## Instructions
How to configure the CESM2-CAM6 aquaplanet GCM with thermodynamic sea ice.

A few changes were made to the source code before building the case.
Firstly, in ~/cesm2/cime/src/drivers/mct/shr/seq_infodata_mod.F90 I commented out the following to include a seasonal cycle.
```
if ( infodata%aqua_planet ) then
	infodata%aqua_planet_sst = 1
	! infodata%perpetual = .true.
	! infodata%perpetual_ymd = aqua_perpetual_ymd
endif
```
Secondly, in ~/cesm2/components/cice/src/source/ice_domain_size.F90 I changed the following lines to equals 1 so that the thermodynamic sea ice k_therm=0 could be run
```
nilyr     = 1
nslyr     = 1
```
I then setup a case with the following compset `1850_CAM60_SLND_CICE_DOCN%SOM_SROF_SGLC_SWAV`
The following lines were added to user_nl_cice. The choice of r_snw = 0.70 was set here, with ice dynamics turned off.
```
kdyn = 0
kitd = 1
prescribed_ice = .false.
tr_aero =  .true.
tr_fy =  .true.
tr_iage =  .true.
grid_file = '~/domain/domain.aqua.fv1.9x2.5.nc'
kmt_file = '~/domain/domain.aqua.fv1.9x2.5.nc'
grid_type = 'latlon'
grid_format = 'nc'
kstrength = 0
ice_ic = 'default'
ktherm = 0
r_snw = 0.7
```
A symmetrised file for the ozone and aerosols files were created and imposed. In user_nl_cam, add:
```
 fincl1 = 'ALDIF','ALDIR','ASDIF','ASDIR',
'CLDHGH','CLDICE','CLDLIQ','CLDLOW','CLDMED','CLDTOT','CLOUD','CONCLD','DCQ','DTCOND','DTV','FICE',
'FLDS','FLNS','FLNSC','FLNT','FLNTC','FLUT','FLUTC','FREQI','FREQL','FREQR','FREQS','FSDS',
'FSDSC','FSNS','FSNSC','FSNT','FSNTC','FSNTOA','FSNTOAC','FSUTOA','H2O','H2O_SRF','ICEFRAC',
'ICIMR','ICWMR','IWC','LANDFRAC','LHFLX','LWCF',
'OCNFRAC','OMEGA','OMEGAT','OMEGAQ','OMEGAU','OMEGAV','OMGAOMGA','PBLH','PHIS','PRECC','PRECL',
'PRECSC','PRECSL','PS','PSL','Q','QFLX','QREFHT','QRL','QRS','QT','RAINQM','RELHUM','SHFLX',
'SL','SNOWHICE','SNOWHLND','SNOWQM','SOLIN','SWCF','T','TAUX','TAUY','TGCLDCWP','TGCLDIWP',
'TGCLDLWP','TMQ','TREFHT','TS','TSMN','TSMX','U','U10','UU','V','VD01','VQ','VT',
'VU','VV','WSUB','Z3'

fexcl1 = 'ADRAIN','ADSNOW','AEROD_v','ANRAIN','ANSNOW','AODDUST','AODDUST1','AODDUST3',
'AODVIS','AQRAIN','AQSNOW','AREI','AREL','AWNC','AWNI','CCN3','CDNUMC','CLOUDCOVER_CLUBB',
'CLOUDFRAC_CLUBB','DF_H2O2','DF_H2SO4','DF_SO2','DMS','DMS_SRF','DTWR_H2O2','DTWR_H2SO4',
'DTWR_SO2','H2O2','H2O2_SRF','H2SO4','H2SO4_SRF','NUMICE','NUMLIQ','NUMRAI','NUMSNO',
'RCMINLAYER_CLUBB','RCMTEND_CLUBB','RCM_CLUBB','RELVAR','RHO_CLUBB','RIMTEND_CLUBB',
'RTP2_CLUBB','RTPTHLP_CLUBB','RVMTEND_CLUBB','SFDMS','SFH2O2','SFH2SO4','SFSO2','SFSOAG',          
'SFbc_a1','SFbc_a4','SFdst_a1','SFdst_a2','SFdst_a3','SFncl_a1','SFncl_a2','SFncl_a3','SFnum_a1',       
'SFnum_a2','SFnum_a3','SFnum_a4','SFpom_a1','SFpom_a4','SFso4_a1','SFso4_a2','SFso4_a3',        
'SFsoa_a1','SFsoa_a2','SO2','SO2_SRF','SOAG','SOAG_SRF','STEND_CLUBB','TAUBLJX','TAUBLJY',       
'THLP2_CLUBB','UM_CLUBB','UP2_CLUBB','UPWP_CLUBB','UTEND_CLUBB','VM_CLUBB',        
'VP2_CLUBB','VPWP_CLUBB','VTEND_CLUBB','WD_H2O2','WD_H2SO4','WD_SO2','WP2_CLUBB',    
'WP3_CLUBB','WPRCP_CLUBB','WPRTP_CLUBB','WPTHLP_CLUBB',  
'WPTHVP_CLUBB','ZM_CLUBB','ZT_CLUBB','bc_a1','bc_a1DDF','bc_a1SFWET','bc_a1_SRF','bc_a4',
'bc_a4DDF','bc_a4SFWET','bc_a4_SRF','bc_c1','bc_c1SFWET','bc_c4','bc_c4SFWET','dst_a1','dst_a1DDF',     
'dst_a1SF','dst_a1SFWET','dst_a1_SRF','dst_a2','dst_a2DDF','dst_a2SF','dst_a2SFWET','dst_a2_SRF',
'dst_a3','dst_a3DDF','dst_a3SF','dst_a3SFWET','dst_a3_SRF','dst_c1','dst_c1SFWET','dst_c2',      
'dst_c2SFWET','dst_c3','dst_c3SFWET','ncl_a1',
'ncl_a1DDF','ncl_a1SF','ncl_a1SFWET','ncl_a1_SRF','ncl_a2','ncl_a2DDF','ncl_a2SF',
'ncl_a2SFWET','ncl_a2_SRF','ncl_a3','ncl_a3DDF','ncl_a3SF','ncl_a3SFWET','ncl_a3_SRF',
'ncl_c1','ncl_c1SFWET','ncl_c2','ncl_c2SFWET','ncl_c3','ncl_c3SFWET','num_a1','num_a1DDF', 
'num_a1SF','num_a1SFWET','num_a1_SRF','num_a2','num_a2DDF','num_a2SF','num_a2SFWET','num_a2_SRF',    
'num_a3','num_a3DDF','num_a3SF','num_a3SFWET','num_a3_SRF','num_a4','num_a4DDF','num_a4SFWET',    
'num_a4_SRF','num_c1','num_c1SFWET','num_c2','num_c2SFWET','num_c3','num_c3SFWET','num_c4',     
'num_c4SFWET','pom_a1','pom_a1DDF','pom_a1SFWET','pom_a1_SRF','pom_a4','pom_a4DDF',       
'pom_a4SFWET','pom_a4_SRF','pom_c1','pom_c1SFWET','pom_c4','pom_c4SFWET',     
'so4_a1','so4_a1DDF','so4_a1SFWET','so4_a1_SRF','so4_a2','so4_a2DDF','so4_a2SFWET',
'so4_a2_SRF','so4_a3','so4_a3DDF','so4_a3SFWET','so4_a3_SRF','so4_c1','so4_c1SFWET',     
'so4_c2','so4_c2SFWET','so4_c3','so4_c3SFWET','soa_a1','soa_a1DDF',
'soa_a1SFWET','soa_a1_SRF','soa_a2','soa_a2DDF'

nhtfrq = 0
mfilt = 1

use_topo_file = .false.

prescribed_ozone_name      = 'OZONE'
prescribed_ozone_datapath     = '/data/groups/feldl/CESM_work/inputdata/atm/cam/ozone'
prescribed_ozone_file = 'apeozone_cam3_5_54.nc'
prescribed_ozone_cycle_yr     =    1990

micro_mg_nccons = .false.
micro_mg_nicons = .false.

ext_frc_cycle_yr = 1850
ext_frc_specifier = 'num_a1 -> ~/model_input/zero_emissions-cmip6_num_so4_a1_anthro-ene_vertical_1750-2015_0.9x1.25_c20170616.nc',
                    'so4_a1 -> ~/model_input/zero_emissions-cmip6_so4_a1_anthro-ene_vertical_1750-2015_0.9x1.25_c20170616.nc'

ext_frc_type = 'CYCLICAL'

srf_emis_cycle_yr =  1850
srf_emis_specifier = 'bc_a4 -> ~/model_input/emissions-cmip6_bc_a4_anthro_surface_1840-1859_1.9x2.5.nc',
         'bc_a4 -> ~/model_input/emissions-cmip6_bc_a4_bb_surface_1840-1859_1.9x2.5.nc',
         'DMS -> ~/model_input/emissions-cmip6_DMS_bb_surface_1840-1859_1.9x2.5.nc',
         'DMS -> ~/model_input/emissions-cmip6_DMS_other_surface_1840-1859_1.9x2.5.nc',
         'num_a1 -> ~/model_input/emissions-cmip6_num_so4_a1_bb_surface_1840-1859_1.9x2.5.nc',
         'num_a1 -> ~/model_input/emissions-cmip6_num_so4_a1_anthro-ag-ship_surface_1840-1859_1.9x2.5.nc',
         'num_a2 -> ~/model_input/emissions-cmip6_num_so4_a2_anthro-res_surface_1840-1859_1.9x2.5.nc',
         'num_a4 -> ~/model_input/emissions-cmip6_num_bc_a4_bb_surface_1840-1859_1.9x2.5.nc',
         'num_a4 -> ~/model_input/emissions-cmip6_num_bc_a4_anthro_surface_1840-1859_1.9x2.5.nc',
         'num_a4 -> ~/model_input/emissions-cmip6_num_pom_a4_anthro_surface_1840-1859_1.9x2.5.nc',
         'num_a4 -> ~/model_input/emissions-cmip6_num_pom_a4_bb_surface_1840-1859_1.9x2.5.nc',
         'pom_a4 -> ~/model_input/emissions-cmip6_pom_a4_anthro_surface_1840-1859_1.9x2.5.nc',
         'pom_a4 -> ~/model_input/emissions-cmip6_pom_a4_bb_surface_1840-1859_1.9x2.5.nc',
         'SO2 -> ~/model_input/emissions-cmip6_SO2_anthro-ag-ship-res_surface_1840-1859_1.9x2.5.nc',
         'SO2 -> ~/model_input/emissions-cmip6_SO2_anthro-ene_surface_1840-1859_1.9x2.5.nc',
         'SO2 -> ~/model_input/emissions-cmip6_SO2_bb_surface_1840-1859_1.9x2.5.nc',
         'so4_a1 -> ~/model_input/emissions-cmip6_so4_a1_anthro-ag-ship_surface_1840-1859_1.9x2.5.nc',
         'so4_a1 -> ~/model_input/emissions-cmip6_so4_a1_bb_surface_1840-1859_1.9x2.5.nc',
         'so4_a2 -> ~/model_input/emissions-cmip6_so4_a2_anthro-res_surface_1840-1859_1.9x2.5.nc',
         'SOAG -> ~/model_input/emissions-cmip6_SOAGx1.5_anthro_surface_1840-1859_1.9x2.5.nc',
         'SOAG -> ~/model_input/emissions-cmip6_SOAGx1.5_bb_surface_1840-1859_1.9x2.5.nc',
         'SOAG -> ~/model_input/emissions-cmip6_SOAGx1.5_biogenic_surface_1840-1859_1.9x2.5.nc'

srf_emis_type = 'CYCLICAL'
tracer_cnst_cycle_yr = 1850
tracer_cnst_datapath = '~/model_input'
tracer_cnst_file = 'tracer_cnst_CAM6chem_piControl_cycle_3D_monthly_c170901.nc'
tracer_cnst_filelist = ''
tracer_cnst_specifier = 'O3','OH','NO3','HO2'
tracer_cnst_type = 'CYCLICAL'
rad_diag_1 = 'A:Q:H2O', 'N:O2:O2', 'N:CO2:CO2', 'N:ozone:O3', 'N:N2O:N2O', 'N:CH4:CH4', 'N:CFC11:CFC11', 'N:CFC12:CFC12'

micro_mg_num_steps = 8

# In the above changes, the input files for aerosols and ozone have been hemispherically symmetrised. 

# If you want to change the CO2 concentration, add the following, this is the 1850 default but can be changed
co2vmr = 284.7e-6
```
The following changes were made which determine the depth of the mixed layer and the climatological Q-flux input
```
./xmlchange LND_DOMAIN_PATH=/home/maenglan/domain/
./xmlchange LND_DOMAIN_FILE=domain.aqua.fv1.9x2.5.nc
./xmlchange DOCN_MODE=som
./xmlchange DOCN_SOM_FILENAME=som.forcing.aquaplanet.Qflux4xCO2annualNHSH_h30_sstQOBS_corrected.2degFV.nc
```
The following lines were added to the user_nl_cpl which gives reasonable orbital parameter values
```
orb_eccen = 0.
orb_obliq = 23.45
orb_mvelp = 0.
orb_mode  = 'fixed_parameters'
```
In L314 of env_build.xml the following change from prognostic to thermo_only were made (not sure if totally necessary)
`<entry id="CICE_MODE" value="thermo_only">`

The following line was added to the user_nl_docn file
`domainfile = '~/domain/domain.aqua.fv1.9x2.5.nc'`

After that the case was built, and submitted as normal.

