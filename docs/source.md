# Source files

The source code is inside the directory `pkg` and organized in the following
directories:

- `ant`: pre-processing, correlation and stacking codes
- `f2c`: math functions (abs, sin, cos, sqrt for real, double, complex)
- `obs`: seismological tools (evalresp, libmseed, SAC, geodetics, ...)
- `sci`: math tools (fft, random numbers, ...)
- `xml`: reading of XML files (used for parameter files)

Inside each of these directories there are normally 4 subdirectories.
For example for `obs`:

- `obs/bin`: test binaries (except for `ant/bin` that contains the processing codes)
- `obs/lib`: libraries
- `obs/prj`: ?
- `obs/src`: source code

## `ant`

Main programs for data pre-processing, correlation and stack for CPU and GPU.

- `par_ic`: processing ambient noises records
- `par_st`: parallel cross correlation and stacking with CPU
- `par_st_gpu`: parallel cross correlation and stacking with GPU

## `f2c`

Mathemetical functions for different data types.

- `arithchk`
- `include`
- `libf2c`

## `obs`

Seismological tools: remove instrument response, read and write miniSEED and SAC files,
filtering, calculating geodetical distances.

- `evalresp`
- `include`
- `libmseed`
- `obs_core`
- `obs_geodetics`
- `obs_mseed`
- `obs_sac`
- `obs_signal`
- `test_obs_geodetics`
- `test_obs_signal`

## `sci`

Mathematical tools: random numbers, FFT, etc.

- `cephes`
- `cmplx_roots_sg`
- `fftpack`
- `include`
- `minpack2`
- `num_core`
- `num_lib`
- `num_random`
- `num_testing`
- `randomkit`
- `sci_fft`
- `sci_optimize`
- `sci_signal`
- `sci_special`
- `test_sci_fft`
- `test_sci_optimize`
- `test_sci_signal`

## `xml`

Read XML files.

- `core`
- `include`
- `sxb`
- `test_sxb`
- `unicode`
- `uri`
- `xmlprs`
