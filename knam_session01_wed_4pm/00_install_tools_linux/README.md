# Instructions for Installing PyMOL, VMD, AmberTools, CHARMM and WHAM(Grossfield) (on Linux Command Line)


> **_NOTE:_** It is advised that you read the entire README file before you start typing in the commands in the sequence they are written. Reading the file first will help you maneuver the installation process more easily.

---
## 1. INSTALLATION ON LOCAL/PERSONAL MACHINE (Linux Command Line)

### 1.0. Make a directory for the session in your local/personal computer 
```bash
mkdir icomse_knam_session_local
cd icomse_knam_session_local
```
### 1.1. Install PyMOL, VMD and WHAM on the personal computer *(not on remote machine)*

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
conda create -n analysisenv
conda activate analysisenv
conda install -y -c conda-forge mamba
mamba install -y -c conda-forge pymol-open-source vmd matplotlib numpy ipython ipykernel 
```

## 2. INSTALLATION ON REMOTE MACHINE (Linux Command Line)

### 2.1. CONDA INSTALLATION
```bash
cd /scratch/axa5186
mkdir icomse_knam_session
cd icomse_knam_session
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Follow the prompts to install Miniconda
# yes  → Accept license
# path → /scratch/axa5186/icomse_knam_session/miniconda3
# no   → Do not run conda init during terminal startup
```
---
### 2.2. CREATE CONDA ENVIRONMENT FOR DEPENDENCIES
```bash
eval "$(/scratch/axa5186/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda create -n knamsessionenv python=3.13
conda activate knamsessionenv
conda install -c conda-forge mamba
# Note the compatible gcc and openmpi versions with: 'module avail gcc' and 'module avail openmpi'
module load gcc/13.2.0
mamba install -c conda-forge  gcc=13.2 gxx=13.2 gfortran=13.2 openmpi=5.0.5 flex bison boost cmake=3.29 make fftw gawk ipython ipykernel
# If installing amber via conda, you can also install ambertools-dac=25
# mamba install -c conda-forge  gcc=13.2 gxx=13.2 gfortran=13.2 openmpi=5.0.7 cmake=3.29 make fftw gawk ipython ipykernel dacase::ambertools-dac=25
```
---
### 2.3. INSTALL AMBERTOOLS25
```bash
# srun --ntasks=16 --cpus-per-task=1 --time=1:00:00 --pty bash 
# deactivate any/all active conda environment by repeating `conda deactivate` 
module purge
eval "$(/scratch/axa5186/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda activate knamsessionenv
module load gcc/13.2.0 # adjust per your system '

cd /scratch/axa5186/icomse_knam_session
# Download AmberTools25 from https://ambermd.org/GetAmber.php and copy it to the session directory
tar -xvf ambertools25.tar.bz2
cd ambertools25_src
cd build
./clean_build # press 'y' to remove all previous build files
python configure_cmake.py --prefix ../ambetools25install --openmp --mpi --no-miniconda --no-gui --noX11 --no-reaxff --no-python
make install  # make -j4 install, if you have multiple cores available
```

### 2.4. INSTALL CHARMM: c49b2-mndo97, c49b2-gauss, c49b2-dftb
```bash
# deactivate any/all active conda environment by repeating `conda deactivate` 
module purge
eval "$(/scratch/axa5186/icomse_knam_session/miniconda3/bin/conda shell.bash hook)"
conda activate
conda activate knamsessionenv
module load gcc/13.2.0  # adjust per your system 

cd /scratch/axa5186/icomse_knam_session
# Download CHARMM c49b2 from https://brooks.chem.lsa.umich.edu/register/ and copy it to the session directory
tar -xvzf c49b2.tar.gz
cd charmm
mkdir build_charmm
cd build_charmm


# c49b2_mndo97,  CHARMM with mndo97
#----------------------------------
rm -rf *  # remove any previous build files in build_charmm
../configure -p ../c49b2_mndo97 --with-gnu --with-mndo97 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available

# c49b2_gauss,  CHARMM with Gaussian
#-----------------------------------
rm -rf *  # remove any previous build files in build_charmm
../configure -p ../c49b2_gauss --with-gnu --with-g09 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available

# c49b2_dftb,  CHARMM with dftb
#-----------------------------------
rm -rf *  # remove any previous build files in build_charmm
sed -i '380s/ || KEY_SCCDFTB==1//' ../source/nbonds/pme.F90
../configure -p ../c49b2_dftb --with-gnu --with-sccdftb --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft --without-cuda --without-opencl
make install # make -j4 install, if you have multiple cores available
```
---
-  **CHECK INSTALLATION**
```bash
# deactivate any/all active conda environment by repeating `conda deactivate`
module purge
module load openmpi/5.0.5 # load the openmpi=5 module available on the remote machine  

# Check if AmberTools25 is installed correctly:
source /scratch/axa5186/icomse_knam_session/ambertools25_src/ambertools25install/amber.sh

sander.MPI 

sqm

# Check if CHARMM49b2-mndo97 and CHARMM49b2-gaussian is installed correctly:
/scratch/axa5186/icomse_knam_session/charmm/c49b2_mndo97/bin/charmm

/scratch/axa5186/icomse_knam_session/charmm/c49b2_gauss/bin/charmm

/scratch/axa5186/icomse_knam_session/charmm/c49b2_dftb/bin/charmm
```
---
### 2.5. INSTALL WHAM

```bash
cd icomse_knam_session
wget http://membrane.urmc.rochester.edu/sites/default/files/wham/wham-release-2.1.0.tgz
tar -xvzf wham-release-2.1.0.tgz
cd wham-release-2.1.0
mkdir build
cd build
cmake ..
cmake --build
```