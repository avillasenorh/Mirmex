# Parameter files

## Pre-processing

Parameter file `input_correction.xml`

```
<?xml version='1.0' encoding='UTF-8'?>

<processing_parameters>

    <!--preprocessing run: Name, description. Must be included.-->
    <prepname>sts-1_2014</prepname>
    <comment>Preprocessing with new cpp tool</comment>
    <!--print output-->
    <verbose>1</verbose>
    <!--save output to file if set to 1. If set to 0, print to screen.-->
    <outfile>1</outfile>
    <!--plot and save time series and psd at various processing stages-->
    <check>0</check>
    <!--update mode, will search for already existing files in the out directories and only process the others.-->
    <update>0</update>
    
    <!--*************************************************************************************************-->
    <!--input and output directories-->
    <!--*************************************************************************************************-->

    <input>
        <!--input directory for data to be processed-->
        <indirs>/scratch/snx3000/alexey/ants/phase01/DATA/raw/</indirs>
        <!--time window covered roughly. (YYYY)-->
        <startyr>2013</startyr>
        <endyr>2014</endyr>
    </input>

    <!--*************************************************************************************************-->
    <!--quality checks-->
    <!--*************************************************************************************************-->
   
    <quality>
        <min_length_in_sec>3600</min_length_in_sec>
        <maxgaplen>60</maxgaplen>
    </quality>

    <!--*************************************************************************************************-->
    <!--preprocessing-->
    <!--*************************************************************************************************-->
    
   
    <!--preprocessing on individual seismograms-->
    <!--The order corresponds to the actual order of execution-->
    <processing>
        <!--split traces into shorter segments-->
        <split>
            <doit>1</doit>
            <length_in_sec>524288</length_in_sec>
        </split>
        <!--trim to nearest second-->
        <trim>1</trim>
        <!--remove linear trend-->
        <detrend>1</detrend>
        <!--remove mean-->
        <demean>1</demean>
        <!--taper edges of seismogram for better filtering-->
        <taper>
            <doit>1</doit>
            <taper_width>0.05</taper_width>
        </taper>
        
        <!--downsampling-->
		<!-- The original sampling rates are needed, because traces with a wrong sampling rate (like 1.0000004, I have seen that) need to be sorted out. Separate different sampling rates by whitespaces.-->
		<!-- If the new sampling rate is different from the old one, data will be downsampled.-->
        <!--A lowpass filter is hardcoded into downsampling. It has a corner frequency of one quarter of the new sampling frequency.-->
        <!--When a large amont of downsampling is performed, do it in two steps! e. g. instead of by factor 20 decimate by 5 then 4.-->
        <!--put in intermediate steps separated by spaces, e. g. 4.0 1.0-->
		<!--IMPORTANT: Since correlation windows are cut by start and end time in seconds, don't decimate below 1 Hz in the preprocessing stage, or you will run into difficulties with the window selection for correlation.-->
        <decimation>
            <Fs_old>1.0</Fs_old>
            <Fs_new>1.0</Fs_new>
        </decimation>
        
        <!--remove instrument response-->
        <instrument_response>
            <doit>1</doit>
            <unit>VEL</unit>
            <respdir>/scratch/snx3000/alexey/ants/phase01/DATA/resp/</respdir>
            <freqs>0.001 0.002 0.32 0.4</freqs>
            <waterlevel>0.0</waterlevel>
        </instrument_response>
	</processing> 
  
</processing_parameters>
```

## Correlation and stacking

Parameter file `input_parstack.xml`.

```
<?xml version='1.0' encoding='UTF-8'?>

<stacking_parameters>
	
	
	<!-- NEW input: this environment variable is the one that contains the MPI rank. This is so that even on local systems, I do not have to use mpi4pi anymore, and the problem of knowing the rank is solved for both local and daint -->
    <rankvariable>ALPS_APP_PE</rankvariable>
	
    <!--print screen output-->
    <verbose>1</verbose>
	<!--select a test run. This saves an intermediate result at every time window (attention, needs ***LOTS*** (and lots and lots) of storage space)-->
    <check>1</check>
    <!--provide a name that will appear as 'stamp' on all correlations calculated in this run-->
    <corrname>test_cpp</corrname>
    <!--Describe briefly-->
    <comment>Run gpu correlation on Daint</comment>
    <!--Updating on a previous run? -->
    <update>1</update>
	
<!--**************************************************************************************************-->
	<!--Specifics for data distribution to cores-->
<!--**************************************************************************************************-->
    <data>
        <!--station ID list; containing entries in format net.sta.loc.cha -->
        <idfile>/scratch/snx3000/alexey/ants/phase01/INPUT/input_stations.txt</idfile>
        <!--How many station pairs for each core? Typically the number of files opened by that core is about n+1-->
        <npairs>4</npairs>
        <!-- Specify channel: LH, BH, VH... -->
        <channel>LH</channel>
        <!-- choose between Z and EN. 'All channels' is not implemented yet. 'EN' will provide you with RR, TT, and RT correlation -->
        <components>Z</components>
        <!-- Mix channels?-->
        <mix_cha>0</mix_cha>
    </data>

 <!--*************************************************************************************************-->
    <!--selection-->
 <!--*************************************************************************************************-->  
	
    <selection>
        <!--Input directory containing data. Can only handle one input directory at the moment.-->
        <indir>/scratch/snx3000/alexey/ants/phase01/DATA/processed/sts-1_2014/</indir>
        <!--Enter preprocessing run name(s). Put * for any preprocessing-->
        <prepname>sts-1_2014</prepname>   
    </selection>
    <!--*************************************************************************************************-->
    <!--Time-->
    <!--*************************************************************************************************-->
    <timethings>
        <!--Sampling rate.If different from sampling rate, data will be downsampled before correlation. -->
        <Fs>1.0</Fs>
        <!--Start date. Will only process files from this-->
        <startdate>20140101</startdate>
        <!--End date. Will only process files until this. Format yyyymmdd-->
        <enddate>20150101</enddate>
        <!--Length of the time windows to be correlated, in seconds-->
        <winlen>32768</winlen>
        <!--Overlap in SECONDS-->
        <!--Groos et al. recommend an overlap that is equal to the maximum lag-->
        <olap>6000</olap>
    </timethings>
    
   
    <!--*************************************************************************************************-->
    <!--Correlations-->
    <!--*************************************************************************************************-->
    
    <!--apply bandpass before correlating-->
    <bandpass>
        <doit>1</doit>
        <f_min>0.002</f_min>
        <f_max>0.05</f_max>
        <corners>3</corners>
        <!-- NEW input: Optional taper, useful to avoid filtering problems, ringing -->
        <taper>
            <doit>1</doit>
            <perc>0.05</perc>
        </taper>
        <!-- NEW input: A few additional options like whitening, glitch removal, onebit, which I do not consider important at the moment.-->
    </bandpass>
	
    <!--Type of correlations-->
    <correlations>
        <!--Autocorrelation yes or no-->
        <autocorr>0</autocorr>
        <!--Type of correlation: 'pcc','pcc','both'-->
        <corrtype>ccc</corrtype>
        <!-- NEW input: Normalize the correlations, or keep only the cross-coherency as given out by np. correlation, i.e. the Inverse Fourier transform of the product of the Fourier transform of one time series with the complex conjugate of the FT of the other -->
        <normalize>1</normalize> 
        <!--Maximum lag in seconds-->
        <max_lag>12000</max_lag>
        <!--For phase cross-correlation: Specify exponent (cf Schimmel et al. 2013)-->
        <pcc_nu>1</pcc_nu>
        <!--For phase weighted stack: Specify type of stack: 0 is time domain, 1 time-scale-domain (using continuous wavelet transform)-->
        <tfpws>0</tfpws>
    </correlations>  
        
</stacking_parameters>

```

