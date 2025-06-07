# Instructions for installling AmberTools and CHARMM in a conda environment (on Linux command line)
### In order to use CHARMM and AmberTools you will need to:
- **Create a conda environment capable of building CHARMM and AmberTools**
- **Obtain the CHARMM software (free to academics and government labs) from [AcademicCHARMM](https://academiccharmm.org/program). Follow the directions below to build a conda environment capable of installing CHARMM/pyCHARMM.**

**_NOTE:_**  It is advised that you read the entire README file before you start typing in the commands in the sequence they are written. Reading the file first will help you maneuver the installation process more easily.


### 1. Creating conda environment
- **You will need a base anaconda/miniconda installation: see [anaconda installation](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).**

- **Make a conda environment**<p>
```bash
conda create -y -n <name_of_environment> python=3.12 # note python >= 3.12 for ambertools24
```

- **Activate this environment**<p>
```bash
conda activate <name_of_environment>
```

- **Install mamba as a faster conda**<p>
```bash
conda install -y -c conda-forge mamba
```


### 2. Install AmberTools and CHARMM, MBAR-analysis dependencies

- **Install dependencies for CHARMM MBAR-analysis along with Ambertools**<p>

```bash
mamba install -y -c conda-forge gcc=12 gxx=12 gfortran=12 make cmake=3.29.6 binutils gawk openmpi=5 sysroot_linux-64=2.17  fftw dacase::ambertools-dac=24 numpy scipy matplotlib scikit-learn pymbar ipython ipykernel
```

### 2. CHARMM-mndo97 installation once conda environment is installed and active with dependencies.
- **Visit academiccharmm.org and download the latest CHARMM source code**<p>

- **Unzip the downloaded file in session folder**<p>
```bash
mkdir icomse_knam_session
cd icomse_knam_session
tar -xvzf <charmm_source_code>.tar.gz
```

- **Go to CHARMM source root and build CHARMM-mndo97 with configure**
```bash
conda activate <name_of_environment>
cd <charmm_root> # cd charmm
mkdir build_charmm
cd build_charmm
export FFTW_HOME=$CONDA_PREFIX ##in csh## setenv FFTW_HOME $CONDA_PREFIX
../configure -p ../install_charmm --with-gnu --with-mndo97 --without-mkl --without-openmm --without-qchem --without-quantum --without-colfft
make -j4
make install
```


### 3. Install post simulation analysis tools

- **MBAR Python Notebook and Scripts**
```bash
wget <github link for python notebook>
```

- **CATDCD (from vmd) program to merge the simulations to view reaction simulation and additional analyses**
```bash
cd icomse_knam_session
wget <github link>
```
