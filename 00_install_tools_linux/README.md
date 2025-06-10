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

eval "$(/path/to/icomse_knam_session_local/miniconda3/bin/conda shell.bash hook)" # change this path to your installed path
conda activate
```
- **Create a conda environment for PyMOL and VMD and install them**<p>
```bash
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

### 1. Creating conda environment in the remote machine
- **You will need a base anaconda/miniconda installation: see [anaconda installation](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).**

```bash
cd /scratch/<your_username> # change this to your scratch directory
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Follow the prompts to install Miniconda
## yes ! for accepting the license
## /scratch/<your_username>/miniconda3 for installation path
## no  ! for running conda init during terminal startup

eval "$(/scratch/<your_username>/miniconda3/bin/conda shell.bash hook)" # change this path to your installed path
conda activate
```

- **Make a conda environment**<p>
```bash
# check 'module avail gcc' to see available versions on remote machine
module load gcc/13.2.0
conda create -y -n knamsessionenv python=3.12 # note python >= 3.12 for ambertools24
```

- **Activate this environment**<p>
```bash
conda activate knamsessionenv
```

- **Install mamba as a faster conda**<p>
```bash
conda install -y -c conda-forge mamba
```


### 2. Install AmberTools and CHARMM, MBAR-analysis dependencies in the remote machine

- **Install dependencies for CHARMM MBAR-analysis along with Ambertools**<p>

```bash

mamba install -y -c conda-forge gcc=13.2.0 gxx=13.2.0 gfortran=13.2.0 make cmake=3.29.6 binutils gawk openmpi=5.0.6 sysroot_linux-64=2.17  fftw dacase::ambertools-dac=24 numpy scipy matplotlib scikit-learn pymbar ipython ipykernel
```

### 3. CHARMM-mndo97 installation once conda environment is installed and active with dependencies in the remote machine
- **Visit academiccharmm.org and download the latest CHARMM source code**<p>

- **Unzip the downloaded file in session folder in ypur scratch directory on remote machine**<p>
```bash
mkdir icomse_knam_session
mv c49b2.tar.gz icomse_knam_session
cd icomse_knam_session
tar -xvzf c49b2.tar.gz 
```

- **Go to CHARMM source root and build CHARMM-mndo97 with configure**
```bash
conda activate knamsessionenv
cd charmm # in icomse_knam_session
mkdir build_charmm
cd build_charmm
rm -rf * ## remove any previous build files
export FFTW_HOME=$CONDA_PREFIX ##in csh## setenv FFTW_HOME $CONDA_PREFIX
../configure -p ../install_charmm --with-gnu --with-mndo97 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft
#make -j4
make install
```


### 4. Install post simulation analysis tools in the remote machine

- **MBAR Python Notebook and Scripts**
```bash
wget <github link for python notebook>
```

- **CATDCD (from vmd) program to merge the simulations to view reaction simulation and additional analyses**
```bash
cd icomse_knam_session
wget <github link>
```
