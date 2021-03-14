# Introduction

Mirmex is a high‐performance tool for the computation of ambient seismic noise
correlations on CPU and GPU clusters. This is intended to address emerging
challenges in noise correlation studies with increasingly large data volumes.
Mirmex implements a parallelization scheme and strategies to efficiently harness
modern supercomputing resources, and shows that the use of GPUs can accelerate
the computation of noise correlations by one order of magnitude or more compared
with a homogeneous implementation on CPUs. In addition to reducing wall‐clock
time, Mirmex enables on‐the‐fly computations of large noise correlation datasets,
thereby eliminating the need for mass storage to archive results.

## Download

Source code for Mirmex can be downloaded from the ETH Seismology and Wave Physics
[software page](https://cos.ethz.ch/software/production/mirmex.html).

## How to run

### 1. Input files

The bash shell script `setup_mirmex_dirs.sh` creates the skeleton directory
structure for an ambient noise correlation project:

    $ setup_mirmex_dirs.sh <path_to_project_dir>

The script generates the following directories:

- `DATA/raw`: continuous data in SAC or miniSEED format
- `DATA/resp`: continuous response files in RESP format
- `DATA/stationxlm`: continuous station metadata in stationXLM format
- `INPUT`: contains station file `input_stations.txt`
- `INPUT/PROCESSING`: contains parameter files in XML format
  (`input_correction.xml` and `input_parstack.xml`)

The file `input_stations.txt` contains one line for each station in `NET.STA.LOC.` format:

    IU.WCI.10.
    II.TLY.00.
    G.KIP.00.
    IU.KMBO.00.
    BK.CMB.00.
    IU.LCO.00.
    IU.KBS.10.


### 2. Preprocessing

Data preprocessing (decimation, removing mean, trend, and instrument response,
...) is done using the program `par_ic_bat` that can be called from the slurm
job `par_ic_bat.sbatch`:

```bash
#!/bin/bash -l

#SBATCH --account=s868
#SBATCH --nodes=4
#SBATCH --ntasks=48
#SBATCH --ntasks-per-node=12
#SBATCH --ntasks-per-core=1
#SBATCH --cpus-per-task=1
#SBATCH --constraint=gpu
#SBATCH --time=6:00:00

module load daint-gpu
module load craype-accel-nvidia60

srun ./bin/par_ic_bat ./INPUT/PROCESSING/input_correction.xml
```

### 3. Correlation and stacking

Cross correlation and stacking of the pre-processed waveform data is done
using the program `par_st_gpu_bin` that can be called from the slurm job
`par_st_bat.sbatch`:

```bash
#!/bin/bash -l

#SBATCH --account=s868
#SBATCH --nodes=32
#SBATCH --ntasks=32
#SBATCH --ntasks-per-node=1
#SBATCH --ntasks-per-core=1
#SBATCH --cpus-per-task=1
#SBATCH --constraint=gpu
#SBATCH --time=6:00:00

module load daint-gpu
module load craype-accel-nvidia60

srun --wait=600 ./bin/par_st_gpu_bin ./INPUT/PROCESSING/input_parstack.xml
```

### 4. Output files

- `DATA/processed`: contains output files from data pre-processing
- `DATA/processed/out`: reports
- `DATA/processed/<tag>`: miniSEED files with continuous data segments
- `DATA/processed/xmlinput`: input XLM parameter files used for this `<tag>`

- `DATA/correlations`: contains output files from cross-correlation and stacking
- `DATA/correlations/out`: reports
- `DATA/correlations/<label>`: correlations in SAC format (and phase weights is PWS is used)
- `DATA/correlations/xmlinput`: input XLM parameter files used for this `<label>`


## Publication

Fichtner, A., Ermert, L., & Gokhberg, A. (2017). Seismic Noise Correlation on
Heterogeneous Supercomputers. Seismological Research Letters, 88(4), 1141–1145,
doi:[10.1785/0220170043](http://doi.org/10.1785/0220170043).
