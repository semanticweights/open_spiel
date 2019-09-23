# Windows Installation using Windows Subsystem for Linux (WSL)

## Purpose of this document

Defines the installation steps to get OpenSpiel running in a Windows 10
environment using WSL. Note that WSL does not include GPU support, so will run
on CPU only.

## Process

This process has been written for Windows 10, and tested on Windows 10 build
1903 (March 2019).

1.  Install the Windows Subsystem for Linux:

    Run the following command in Windows Powershell:

    ```
       Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
    ```

2.  Install Ubuntu Linux from the Windows Store. Currently this is version
    18.04::

    Open up the Windows Store. Search for Ubuntu. Open up Ubuntu and press "Get"
    to install this.

3.  First time run of Ubuntu:

    Click on the Start Button and choose the Ubuntu icon. Wait until the distro
    installs. Provide a username and password for the default user account. Note
    that this account is a member of the Linux administrators (sudo) group so
    choose a secure username and password combination.

4.  Update / Upgrade packages (optional step)

    ```
       sudo apt-get update
       sudo apt-get upgrade
    ```

5.  Run through the first part of the OpenSpiel installation

    ```
       git clone https://github.com/deepmind/open_spiel.git
       cd open_spiel
       ./install.sh # you will be prompted for the password created at stage 3. Press Y to continue and install. During installation press Yes to restart services during package upgrades
       pip3 install --upgrade -r requirements.txt # Install Python dependenciesmake

    ```

6.  Now need to upgrade make version as the version of make which comes with
    Ubuntu 18.04 is not high enough to build OpenSpiel. (Note, this step won't
    be necessary if the version of Ubuntu in the Windows store gets upgraded to
    19.04)

    ```
       cd ..
       wget http://www.cmake.org/files/v3.12/cmake-3.12.4.tar.gz
       tar -xvzf cmake-3.12.4.tar.gz
       cd cmake-3.12.4/
       ./configure
       make
       sudo make install
       sudo update-alternatives --install /usr/bin/cmake cmake /usr/local/bin/cmake 1 --force
       cd ../open_spiel

    ```

7.  Finally, continue with the installation and run tests.

    ```
       mkdir build
       cd build
       # Note: Python version installed should be >= Python_TARGET_VERSION specified here
       CXX=g++ cmake -DPython_TARGET_VERSION=3.6 -DCMAKE_CXX_COMPILER=g++ ../open_spiel
       make -j12 # The 12 here is the number of parallel processes used to build
       ctest -j12 # Run the tests to verify that the installation succeeded
    ```