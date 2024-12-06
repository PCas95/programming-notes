# Notes on Conda

## How to Install Conda

Conda comes in 2 different distributions: Anaconda and Miniconda.
- Anaconda is more suited for server installations, since it needs more than 4.4 GB of disk space for installation and has many more packages and features available.
- Miniconda is a lightweight version which requires less then 0.5 GB but has a lower number of packages.

### Steps

1. **Download Miniconda Installer:** <https://www.anaconda.com/download/success>

2. **Run the Installer:**

  ```bash
  bash Miniconda3-latest-Linux-x86_64.sh
  ```

Follow the on-screen prompts to accept the license and select the installation directory.

Initialize Conda:
Run the initialization script:

source ~/.bashrc

Alternatively, restart your shell.

Verify Installation:
Confirm conda is installed:

    conda --version

Conda Commands to Create a New Environment

    Create the Environment:
    Replace env_name with the desired environment name and python=3.x with the required Python version:

conda create --name env_name python=3.x

Activate the Environment:

conda activate env_name

Install Additional Packages:
Add any required packages to the environment:

conda install package_name

Deactivate the Environment:

conda deactivate
