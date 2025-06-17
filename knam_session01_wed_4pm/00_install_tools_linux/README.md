# Instructions for Installing AmberTools and CHARMM in a Conda Environment (on Linux Command Line)

### In order to use CHARMM and AmberTools you will need to:
- **Create a conda environment capable of building CHARMM and AmberTools**
- **Obtain the CHARMM software (free to academics and government labs) from [AcademicCHARMM](https://academiccharmm.org/program). Follow the directions below to build a conda environment capable of installing CHARMM.**

> **_NOTE:_** It is advised that you read the entire README file before you start typing in the commands in the sequence they are written. Reading the file first will help you maneuver the installation process more easily.

---
### 1.0. Make a directory for the session in your local/personal computer 
```bash
mkdir icomse_knam_session_local
cd icomse_knam_session_local
```
### 1.1. Install PyMOL and VMD on the personal computer *(not on remote machine)*

- **You will need a base anaconda/miniconda installation: see [Anaconda installation guide](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).**

```bash
cd icomse_knam_session_local
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Follow the prompts to install Miniconda
# yes  → Accept license
# path → /path/to/icomse_knam_session_local/miniconda3
# no   → Do not run conda init during terminal startup
```

- **Create a conda environment for PyMOL and VMD and install them**:

```bash
eval "$(/path/to/icomse_knam_session_local/miniconda3/bin/conda shell.bash hook)"  # Change path accordingly
conda activate
conda create -n pymolvmdenv
conda activate pymolvmdenv
conda install -y -c conda-forge mamba
mamba install -y -c conda-forge pymol-open-source vmd
```

---

### 1.2. Download CHARMM and copy the source code to the remote machine

- **Visit [academiccharmm.org](https://academiccharmm.org)**
- **Complete registration and download the latest CHARMM source code via the emailed link. Your username will be your email; password is provided in the email.**
- **Copy the downloaded file to the remote machine:**

```bash
scp c49b2.tar.gz <username>@<remote_machine>:/scratch/<your_username>
```
---
### 2.0. Make a directory for the session on the remote machine

```bash
cd /scratch/<your_username>
mkdir icomse_knam_session
cd icomse_knam_session
```
---
### 2.1. Create Conda Environment on the Remote Machine

- **Install Miniconda on the remote machine** ([guide](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html)):

```bash
cd icomse_knam_session
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

# Follow the prompts
# yes  → Accept license
# path → /scratch/<your_username>/icomse_knam_session/miniconda3
# no   → Do not run conda init during terminal startup
```

---

### 2.2. Install AmberTools24 on the Remote Machine

```bash
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda create -y -n knamsessionenv python=3.12
conda activate knamsessionenv
conda install -y -c conda-forge mamba
module load gcc/13.2.0  # adjust per your system # check which version available with 'module avail gcc' #follow the same with mamba
mamba install -c conda-forge gcc=13.2.0 gxx=13.2.0 gfortran=13.2.0 openmpi dacase::ambertools-dac=24 make cmake=3.29.6 gawk fftw numpy scipy matplotlib scikit-learn pymbar ipython ipykernel
# binutils, sysroot_linux-64=2.17
```

- **Check if AmberTools24 is installed correctly:**

```bash
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate knamsessionenv
module unload openmpi-5.0.5
source $CONDA_PREFIX/amber.sh
module load openmpi-5.0.5 # load the openmpi=5 module available on the remote machine

which sander.MPI
which sqm

sander.MPI
sqm
```

---

### 2.3. Install CHARMM49b2 on the Remote Machine

```bash
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda activate knamsessionenv

module load gcc/13.2.0

cd /scratch/<your_username>
mv c49b2.tar.gz icomse_knam_session
cd icomse_knam_session
tar -xvzf c49b2.tar.gz
cd charmm
mkdir build_charmm
cd build_charmm

rm -rf *  # remove any previous build files
export FFTW_HOME=$CONDA_PREFIX

# Install CHARMM with mndo97
../configure -p ../c49b2_mndo97 --with-gnu --with-mndo97 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available


rm -rf * # remove any previous build files in build_charmm
export FFTW_HOME=$CONDA_PREFIX

# Install CHARMM with Gaussian
../configure -p ../c49b2_gauss --with-gnu --with-g09 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available

rm -rf * # remove any previous build files in build_charmm
export FFTW_HOME=$CONDA_PREFIX

# Install CHARMM with dftb
sed -i '380s/ || KEY_SCCDFTB==1//' ../source/nbonds/pme.F90
../configure -p ../c49b2_dftb --with-gnu --with-sccdftb --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available

```

- **Check if CHARMM49b2-mndo97 and CHARMM49b2-gaussian is installed correctly:**

```bash
eval "$(/scratch/<your_username>/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate knamsessionenv
module unload openmpi-5.0.5
module load openmpi-5.0.5 # load the openmpi=5 module available on the remote machine

/scratch/<your_username>/icomse_knam_session/charmm/c49b2_mndo97/bin/charmm
/scratch/<your_username>/icomse_knam_session/charmm/c49b2_gauss/bin/charmm
/scratch/<your_username>/icomse_knam_session/charmm/c49b2_dftb/bin/charmm

```

---

### 3.1. Install Post-Simulation Analysis Tools on the Remote Machine

- **MBAR Python Notebook and Scripts:**

```bash
cd icomse_knam_session
wget <github link for python notebook>
```

- **CATDCD (from VMD) to merge trajectories for viewing/analysis:**

```bash
cd icomse_knam_session
wget <github link>
```
