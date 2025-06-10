# Instructions for installling AmberTools and CHARMM in a conda environment (on Linux command line)
### In order to use CHARMM and AmberTools you will need to:
- **Create a conda environment capable of building CHARMM and AmberTools**
- **Obtain the CHARMM software (free to academics and government labs) from [AcademicCHARMM](https://academiccharmm.org/program). Follow the directions below to build a conda environment capable of installing CHARMM.**

**_NOTE:_**  It is advised that you read the entire README file before you start typing in the commands in the sequence they are written. Reading the file first will help you maneuver the installation process more easily.

### 1.1. Install PyMOL and VMD in the personal computer (if not already available) #THIS IS NOT ON REMOTE MACHINE

- **You will need a base anaconda/miniconda installation: see [anaconda installation](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).**

```bash
mkdir icomse_knam_session_local
cd icomse_knam_session_local
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Follow the prompts to install Miniconda
## yes ! for accepting the license
## /path/to/icomse_knam_session_local/miniconda3 for installation path
## no  ! for running conda init during terminal startup
```
- **Create a conda environment for PyMOL and VMD and install them**<p>
```bash
# If already in any conda environment, deactivate all of them with 'conda deactivate' until you exit all conda environments
eval "$(/path/to/icomse_knam_session_local/miniconda3/bin/conda shell.bash hook)" # change this path to your installed path
conda activate
conda create -n pymolvmdenv
conda activate pymolvmdenv
conda install -y -c conda-forge mamba
mamba install -y -c conda-forge pymol-open-source vmd
```

### 1.2. Downlaod CHARMM and copy the source code to the remote machine
- **Visit academiccharmm.org**<p>
- **complete the registration and downlaod the latest CHARMM source code via the link sent to the email. The username will be your email and passwork will be in the email from CHARMM**<p>
- **Copy the downloaded file to the remote machine**<p>
```bash 
scp c49b2.tar.gz <username>@<remote_machine>:/scratch/<your_username>
```

### 2.1. Creating conda environment in the remote machine
- **You will need a base anaconda/miniconda installation: see [anaconda installation](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).**

```bash
cd /scratch/<your_username> # change this to your scratch directory
mkdir icomse_knam_session
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

# Follow the prompts to install Miniconda
#========================================
## yes ! for accepting the license
## /scratch/<your_username>/icomse_knam_session/miniconda3 for installation path
## no  ! for running conda init during terminal startup

```

### 2.2. Install AmberTools24 on the remote machine
```bash
# If already in any conda environment, deactivate all of them with 'conda deactivate' until you exit all conda environments
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda create -y -n knamsessionenv python=3.12
conda activate knamsessionenv
conda install -y -c conda-forge mamba
# check which version available with 'module avail gcc'
module load gcc/13.2.0 # load the gcc module available on the remote machine
mamba install -c conda-forge gcc=13.2.0 gxx=13.2.0 gfortran=13.2.0 openmpi dacase::ambertools-dac=24 make cmake=3.29.6 binutils gawk sysroot_linux-64=2.17  fftw numpy scipy matplotlib scikit-learn pymbar ipython ipykernel
```
- **Check if AmberTools24 is installed correctly**<p>
```bash
# If already in any conda environment, deactivate all of them with 'conda deactivate' until you exit all conda environments
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate knamsessionenv
module unload openmpi-5.0.5 # if already loaded
source $CONDA_PREFIX/amber.sh
module load openmpi-5.0.5 # load the openmpi module available on the remote machine
# Check if AmberTools is installed correctly
which sander.MPI
which sqm

sander.MPI
sqm

```

### 2.3. Install CHARMM49b2-mndo97 on the remote machine
```bash
# If already in any conda environment, deactivate all of them with 'conda deactivate' until you exit all conda environments
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda activate knamsessionenv

module load gcc/13.2.0 # load the gcc module available on the remote machine

cd /scratch/<your_username>
mv c49b2.tar.gz icomse_knam_session
cd icomse_knam_session
tar -xvzf c49b2.tar.gz 
cd charmm
mkdir build_charmm
cd build_charmm
export FFTW_HOME=$CONDA_PREFIX
../configure -p ../install_charmm --with-gnu --with-mndo97 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install
```
- **Check if CHARMM49b2-mndo97 is installed correctly**<p>
```bash
# If already in any conda environment, deactivate all of them with 'conda deactivate' until you exit all conda environments
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate knamsessionenv
module unload openmpi-5.0.5 # if already loaded
module load openmpi-5.0.5 # load the openmpi module available on the remote machine

# Check if CHARMM49b2-mndo97 executable is installed correctly
/scratch/<user_name>/icomse_knam_session/charmm/install_charmm/bin/charmm
```

### 3.1. Install post simulation analysis tools in the remote machine

- **MBAR Python Notebook and Scripts**
```bash
cd icomse_knam_session
wget <github link for python notebook>
```

- **CATDCD (from vmd) program to merge the simulations to view reaction simulation and additional analyses**
```bash
cd icomse_knam_session
wget <github link>
```
