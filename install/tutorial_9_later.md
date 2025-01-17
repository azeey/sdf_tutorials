# Download and Install SDFormat

## Binary Installation

### Ubuntu

On Ubuntu systems, `apt-get` can be used to install `sdformat`:

    sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
    wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
    sudo apt-get update

    sudo apt install libsdformat<#>-dev libsdformat<#>

Be sure to replace `<#>` with a number value, such as 9 or 10, depending on
which version you need.

### macOS

On macOS, add OSRF packages:

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew tap osrf/simulation

Install sdformat:

    brew install sdformat<#>

Be sure to replace `<#>` with a number value, such as 9 or 10, depending on
which version you need.

### Windows

Install [Conda package management system](https://docs.conda.io/projects/conda/en/latest/user-guide/install/download.html).
Miniconda suffices.

Create if necessary, and activate a Conda environment:

    conda create -n ign-ws
    conda activate ign-ws

Install `sdformat`:

    conda install libsdformat --channel conda-forge

You can view all the versions with

    conda search libsdformat --channel conda-forge

and install a specific minor version with

    conda install libsdformat=9.3.0 --channel conda-forge

## Source installation

### Ubuntu

#### Prerequisites

Make sure you have removed the Ubuntu pre-compiled binaries before installing from source

    sudo apt-get remove 'libsdformat*' 'sdformat*'

If you have previously installed from source, be sure you are installing to the same path location or that you have removed the previous installation from source version manually.

#### Install Required Dependencies

Install prerequisites. A clean Ubuntu system will need:

##### build tools, ruby for building xml schemas, tinyxml, and boost:

    sudo apt -y install \
      $(sort -u $(find . -iname 'packages-'`lsb_release -cs`'.apt' -o -iname 'packages.apt' | grep -v '/\.git/') | tr '\n' ' ')

#### Build And Install SDFormat

This section describes how to install SDFormat into `/usr`,
which greatly simplifies the process of linking to and loading `libsdformat`
by downstream software.
This may be suitable for use inside a docker container,
but care should be taken when installing to `/usr` since installing the
debian packages would overwrite the version that you built from source,
and it can be very difficult to debug problems that arise from this system state.
It is often safer to install to a local folder and set the `LD_LIBRARY_PATH`
and other environment variables appropriately.
To simplify this type of installation, a
[catkin workspace](http://gazebosim.org/tutorials?tut=install_from_source&cat=install#Installinacatkinworkspace)
or
[colcon workspace](https://index.ros.org/doc/ros2/Tutorials/Colcon-Tutorial/)
may be used along with the corresponding build tools that generate
shell scripts for setting the necessary environment variables.

#### Installing into `/usr`

1. Clone the repository into a directory in your home folder:

        mkdir ~/sdf_source
        cd ~/sdf_source/
        git clone https://github.com/ignitionrobotics/sdformat

2. Change directory into the sdformat repository and switch to the sdf<#> branch

        cd sdformat
        git checkout sdf<#>

   **Note: Your cloned repository should be on the latest release branch by default, which is the recommended branch for stability. You can find the bleeding edge code in the <tt>main</tt> branch.**

3. Create a build directory and go there

        mkdir build
        cd build

4. Build and install

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        # make install will work as the root user in a docker container
        # otherwise you may need to use `sudo make install`
        make install

sdformat supported cmake parameters at configuring time:

  * `USE_INTERNAL_URDF` (`bool`) [default `False`] <br/>
    Use an internal copy of urdfdom 1.0.0 instead of look for one
    installed in the system
  * `USE_UPSTREAM_CFLAGS` (`bool`) [default `True`] <br/>
    Use the sdformat team compilation flags instead of the common set defined
    by cmake.


#### Uninstalling Source-based Install

If you need to uninstall SDF or switch back to a debian-based install of SDF when you currently have installed SDF from source, navigate to your source code directory's build folders and run make uninstall:

    cd ~/sdf_source/sdformat/build
    # make uninstall will work as the root user in a docker container
    # otherwise you may need to use `sudo make uninstall`
    make uninstall

### macOS

#### Prerequisites

Clone the repository

    git clone https://github.com/ignitionrobotics/sdformat -b sdf<#>

Be sure to replace `<#>` with a number value, such as 9 or 10, depending on
which version you need.

Install dependencies

    brew install --only-dependencies sdformat<#>

#### Build from Source

1. Configure and build

        cd sdformat
        mkdir build
        cd build
        cmake .. # Consider specifying -DCMAKE_INSTALL_PREFIX=...
        make

2. Optionally, install and uninstall

        sudo make install

  To uninstall the software installed with the previous steps:

    cd build/
    sudo make uninstall

### Windows

#### Prerequisites

Install [Conda package management system](https://docs.conda.io/projects/conda/en/latest/user-guide/install/download.html).
Miniconda suffices.

Create if necessary, and activate a Conda environment:

    conda create -n ign-ws
    conda activate ign-ws

Install prerequisites:

    conda install urdfdom --channel conda-forge

Install Ignition dependencies:

You can view lists of dependencies:

    conda search libsdformat --channel conda-forge --info

Install dependencies, replacing `<#>` with the desired versions:

    conda install libignition-math<#> libignition-tools<#> --channel conda-forge

#### Build from Source

This assumes you have created and activated a Conda environment while installing the Prerequisites.

1. Configure and build

        mkdir build
        cd build
        cmake .. -DBUILD_TESTING=OFF  # Optionally, -DCMAKE_INSTALL_PREFIX=path\to\install
        cmake --build . --config Release

2. Install

        cmake --install . --config Release
