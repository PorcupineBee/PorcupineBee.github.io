---
title: How to Install Seismic Unix on WSL
description: A step-by-step guide to installing Seismic Unix on Ubuntu WSL
date: 2025-05-06
tags: [seismic unix, SU, seismic, wsl, XLaunch, Xming, X server, linux, tutorial, Ubuntu, Ubuntu 20.04]
layout: post
---
# How to Install Seismic Unix on WSL Like a Boss

> A Beginner's Guide to Installing Seismic Unix on Windows with WSL

Seismic Unix (SU) is a powerful open-source seismic data processing package widely used in geophysics. In this guide, I'll walk you through installing SU on Windows using WSL (Windows Subsystem for Linux), making the process accessible even for beginners.

## 1. Install and Set Up WSL with Ubuntu

First, let's install the Windows Subsystem for Linux with Ubuntu:

1. Open PowerShell as Administrator (right-click on PowerShell and select "Run as administrator")
2. Run the following command to install WSL with Ubuntu:
   ```
   wsl --install -d Ubuntu
   ```
3. Restart your computer when prompted
4. After restart, Ubuntu will launch automatically and ask you to:
   - Create a username (choose something you'll remember)
   - Set a password (you'll need this for sudo commands)
5. Wait for the installation to complete

## 2. Install Xming X Server

To display graphical interfaces from WSL, we need an X server:

1. Download Xming from [https://sourceforge.net/projects/xming/](https://sourceforge.net/projects/xming/)
2. Run the installer with default settings
3. After installation, launch XLaunch from the Start menu
4. Follow these configuration steps:
   - Select "Multiple windows" → Click "Next"
   - Select "Start no client" → Click "Next"
   - Check "No Access Control" 
   - Enter additional parameters: `-ac -1`
   - Click "Next" → Click "Finish"

Xming is now running in the background, ready to display graphics from your WSL environment.

## 3. Download Seismic Unix

Let's get the Seismic Unix package:

1. Download the SU package from [https://nextcloud.seismic-unix.org/index.php/s/LZpzc8jMzbWG9BZ](https://nextcloud.seismic-unix.org/index.php/s/LZpzc8jMzbWG9BZ)
2. Choose version `cwp_su_all_44R26.tgz` (recommended for stability)
3. Note where the file downloads (typically your Windows Downloads folder)

## 4. Install Essential Packages in WSL

Before installing Seismic Unix, we need to set up the required dependencies:

Open your Ubuntu WSL terminal and run these commands one by one:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt-get install -y build-essential
sudo apt-get install -y gfortran
sudo apt install -y libx11-dev
sudo apt install -y libxt-dev
sudo apt install -y libxmu-dev libxi-dev
sudo apt install -y libc6
sudo apt install -y gedit
```

These packages provide:
- `build-essential`: Basic compilation tools including gcc
- `gfortran`: GNU Fortran compiler for scientific code
- `libx11-dev`, `libxt-dev`, `libxmu-dev`, `libxi-dev`: X11 libraries for graphical display
- `gedit`: A simple text editor (similar to Notepad)

## 5. Set Up Seismic Unix Installation

Now let's prepare the environment and extract the SU package:

```bash
# Create directory for SU installation
cd ~
mkdir cwp

# Move the downloaded file to the cwp directory
# Replace username with your Windows username
mv /mnt/c/Users/username/Downloads/cwp_su_all_44R26.tgz .

# Extract the package
gunzip cwp_su_all_44R26.tgz
tar -xvf cwp_su_all_44R26.tar

# Note your current directory path
pwd
```

Write down the path displayed by the `pwd` command - you'll need it in the next step. It should look something like `/home/yourusername/cwp`.

## 6. Configure Environment Variables

Now we need to set up environment variables:

```bash
# Open the bash configuration file
gedit ~/.bashrc
```

If that doesn't work, try:
```bash
sudo gedit ~/.bashrc
```

Add these lines at the end of the file:
```bash
# Set the CWPROOT variable
export CWPROOT=/home/yourusername/cwp

# Set the PATH variable
export PATH=$PATH:$CWPROOT/bin:.
```

**Important:** Replace `/home/yourusername/cwp` with the path you noted from the `pwd` command.

Save and close the file, then apply the changes:
```bash
source ~/.bashrc
```

## 7. Configure and Install Seismic Unix

Now let's compile and install SU:

```bash
# Navigate to source directory
cd ~/cwp/src

# Set appropriate permissions
sudo chmod uog+rw /home/yourusername/cwp/src

# Find the right configuration file
cd config
ls
```

You'll see several Makefile configuration files for different systems. Choose the one for the latest Ubuntu version that's closest to your WSL Ubuntu version:

```bash
# For example, if using Ubuntu 20.04 or later:
mv Makefile.config_Linux_Ubuntu_20.04 Makefile.config

# Return to src directory and replace the default config
cd ..
rm Makefile.config
mv config/Makefile.config .

# Edit the configuration file
gedit Makefile.config
```

Find the line containing `XDRFLAG = -DSUXDR` and change it to:
```
XDRFLAG = # -DSUXDR
```

Save and close the file.

## 8. Compile and Test Seismic Unix

Finally, let's compile and test SU:

```bash
# Make sure you're in the src directory
cd ~/cwp/src

# Install the main package
make install
```

During installation, you'll be asked some questions:
- Do you want to use the software? → Type `y`
- Do you want to install the package? → Type `y`
- Do you want to install non-standard code? → Type `n`

After the main installation completes:
```bash
# Install X-tools components
make xtinstall
```

## 9. Verify the Installation

Let's test if everything is working properly:

```bash
# Set the DISPLAY variable for X server
export DISPLAY=:0

# Run a simple test
suplane | suxwigbe
```

If a window appears showing seismic traces, congratulations! You've successfully installed Seismic Unix.

## Troubleshooting Tips

- **X display not working?** Make sure Xming is running and you've set `export DISPLAY=:0` in your terminal.
  
- **Compilation errors?** Check that all dependencies were installed correctly with `sudo apt install -y build-essential gfortran libx11-dev libxt-dev libxmu-dev libxi-dev libc6`.

- **Command not found errors?** Make sure your `.bashrc` file has the correct path and that you've run `source ~/.bashrc`.

- **Permission issues?** Try running the problematic commands with `sudo`.


## Conclusion

You now have a fully functioning Seismic Unix installation on your Windows computer through WSL! This powerful seismic processing toolkit will allow you to analyze and visualize seismic data right from your Windows machine.

For more information on using Seismic Unix, consult the official documentation or explore the example scripts included in the package.

Happy processing!