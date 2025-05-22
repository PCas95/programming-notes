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

list envs:
conda env list

3. **Enable Bash's Tab Completion for Conda**

The official tab completion feature, previously available via the `argcomplete` package, was deprecated starting with `conda` version 4.4 and has not been reinstated in subsequent versions. While native support is absent, Bash autocompletion for `conda` commands and environment names can still be achieved in Bash versions higher than 3 using the community-maintained `conda-bash-completion` package. This package has to be installed in the `base` environment, but it works also outside of it, as long as the Bash completion script is sourced correctly in the `.bashrc` file.

- Install `conda-bash-completion` once in the `base` environment (it's only needed in one place and is treated as one of the "core" Conda utilities that need to be installed in `base` to be available):

    ```sh
    conda activate base
    conda install -c conda-forge conda-bash-completion
    ```

- Modify and source the `~/.bashrc` file:
    - Find your conda install path by running `echo $CONDA_PREFIX` **while in the `base` environment**. Take note of or copy it.
    - Add the following code after the `conda init` block:
        ```sh
        # Enable conda command autocompletion
        _conda_completion_path="$HOME/miniconda3/etc/profile.d/bash_completion.sh"  # Adjust if needed
        if [ -f "$_conda_completion_path" ]; then
            source "$_conda_completion_path"
        fi
        ```
        > Replace `$HOME/miniconda3` with the actual path from `$CONDA_PREFIX` if it's different.
        > 
        > **Note:** `"$HOME/miniconda3/etc/profile.d/bash_completion.sh"` should work just fine for any miniconda installation, since `$CONDA_PREFIX` usually expands to `$HOME/<conda_version>`. Conda installation path, however, can be modified at installation, so it *could* be beneficial to "hardcode" it by replacing `$HOME/miniconda3/` with the full string value. (**NEEDS CONFIRMATION: I would rather use the `$HOME` variable...**
    - Reload the shell config file by sourcing it and/or closing and rebooting all terminals:
        ```sh 
        source ~/.bashrc
        ```
        > Note: sometimes sourcing manually the file does not work alone and closing and restarting the terminal is necessary.

