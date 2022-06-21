# Cuda_cuDNN_LinuxInstall
(Last updated 21st June 2022)
On Linux the recommended Pytorch version at the moment is 1.11.0 which runs best on Cuda 11.3
As a result in this guide we will install Cuda 11.3, the corresponding cuDNN and Pytorch 1.11.0. This being said, this should be a valid guide also for other Cuda versions.

This approach has worked successfully on two different machines running Ubuntu 20.04 with the 5.14.0-1042-oem kernel

## Install the Nvidia Driver
First of all we will install the desired Nvidia driver. You can skip this section if you already have one installed. However, installing it before hand can avoid a few conflicts a great hassle later down the line. Make sure that the driver you plan on installing is supported by the specific Cuda version that you will install (this can be checked here https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html).

By running:
```
ubuntu-drivers devices
```
We can see which GPU we have and also what are the drivers that ubuntu suggests we install and which between those is the recommended one.
In case you wanted a specific driver you can install it in the way you prefer (some useful links are in the text file).
Otherwise, if you have no specific needs I would suggest you install the recommended driver, this can be done by running:

```
sudo ubuntu-drivers autoinstall
```
We can then reboot our system and the driver should have been successfully installed. We can check this by making sure that the command below is recognised and produces a valid output.

```
nvidia-smi
```

## Installing Cuda
The differet releases of Cuda, with the corresponding guides and documentations can be found here https://developer.nvidia.com/cuda-toolkit-archive.
In this guide we will install Cuda 11.3.1

There are multiple ways to install Cuda. I personally followed the deb(local) installation, which is better explained below. However you can install Cuda in any way you prefer as I better discuss in the second paragraph.

### dev(local)
This installation can be achieved by executing the following sequence of commands in your terminal.
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.3.1/local_installers/cuda-repo-ubuntu2004-11-3-local_11.3.1-465.19.01-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-11-3-local_11.3.1-465.19.01-1_amd64.deb
sudo apt-key add /var/cuda-repo-ubuntu2004-11-3-local/7fa2af80.pub
sudo apt-get update
sudo apt install cuda-toolkit-11-3
```

### Alternative installations
Alternatively, the other ways of installing Cuda 11.3 that Nvidia itself recommends can be found here https://developer.nvidia.com/cuda-11-3-1-download-archive. You cand then simply follow the instructions.

However, whichever way you use, in case you were asked, make sure that instead of running:
```
sudo apt get -y install cuda
```
You actually run
```
sudo apt install cuda-toolkit-X-Y
```
Where you are trying to install cuda version X.Y. I propose the alternative method because If you have followed this guide you already have the driver onstalled, therefore you only need the toolkit itself. The proposed command makes sure that there are no conflicts.

## Adding Cuda to the .bashrc
Now we have to tell our machine where to find cuda. As a result we can add the following two lines in our .bashrc file found in the home directory (~/)
```
export PATH=/usr/local/cuda-11.3/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64
```
We can then source the apdated .bashrc file by running:
```
source ~/.bashrc
```
And then check that everything works by making sure that running the following command returns a valid output.
```
nvcc -V
```

## Installing cuDNN
We can start off by going to https://developer.nvidia.com/cudnn and click the 'Download cuDNN' button.

For this next step you will need an Nvidia account. You can either log in if you already have one or create one.
Once you are logged in you will have to complete a short survey and accept the terms and conditions.

Finally the burocratic part is finished and a few cuDNN versions will apper available for download. In the case of Cuda 11.3, our desired version of cuDNN is acyually not going to be displayed on this page, instead you'll have to click 'Archived cuDNN Releases'.

Once here one can chose the desired cuDNN version. In our case we will select 'cuDNN v8.2.1 for cuda 11.x' and then we will download the runtime libcudnn [cuDNN Runtime Library for Ubuntu20.04 x86_64 (Deb)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.1.32/11.3_06072021/Ubuntu20_04-x64/libcudnn8_8.2.1.32-1+cuda11.3_amd64.deb) and the developer libcudnn [cuDNN Developer Library for Ubuntu20.04 x86_64 (Deb)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.1.32/11.3_06072021/Ubuntu20_04-x64/libcudnn8_8.2.1.32-1+cuda11.3_amd64.deb)

Once those have been downloaded we can go into our Downloads folder and install those .deb packages as follows:
```
cd ~/Downloads
sudo dpkg -i libcudnn8_8.2.1.32-1+cuda11.3_amd64.deb
sudo dpkg -i libcudnn8-dev_8.2.1.32-1+cuda11.3_amd64.deb
```
And we are done!
We can check that everything was installed successfully by running on terminal:
```
apt list --installed | grep libcudnn
```
If the libcudnn* and libcudnn*-dev packages are shown everything has worked.

## Pytorch
Installing pytorch can be done by following the instructions on their [website](https://pytorch.org/)

## Checking everything
The final thing that we can do to check that everything really works is running python on the terminal and execute the following commands:
```
>>> import torch
>>> torch.cuda.is_available()
# Should be: True

>>> torch.cuda.device_count()
# Should be: a number >= 1

>>> torch.cuda.current_device()
# Should be: 0

>>> torch.cuda.get_device_name(0)
# Should be: the name of your GPU
```