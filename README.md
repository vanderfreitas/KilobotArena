# KilobotArena
## Augmented Reality for Kilobots (ARK)
### Installation

A GUI for running experiments using Kilobot Smart Arena with one single tracking camera. This is an adaptation of the code for four tracking cameras.

Ubuntu is the preferred OS for ARK

KilobotArena is a Qt program, and therefore uses the Qt build tools to generate the make file. There are two ways to do this - in both cases you need a recent version of Qt [v.5.6+](www.qt.io) installed - on Ubuntu the version in aptitude should do (`sudo apt-get install qtcreator` should install everything from Qt that you need).

You also need a CUDA supporting version of OpenCV 3 - you'll need to compile this yourself. We performed the installation as follows


```bash
# QT CREATOR
sudo apt-get install qtcreator


# Following the steps in https://gist.github.com/filitchp/5645d5eebfefe374218fa2cbf89189aa for OpenCV 3 with CUDA 8 installation.


# Nvidia Drivers with Compiz
# Start clean
sudo apt purge nvidia-*
# Add the PPA
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update


sudo apt-get install nvidia-367

# Download Cuda 8 from the link: https://developer.nvidia.com/cuda-80-download-archive
# Check whether the file name is the same as: cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb

cd ~/Downloads
sudo dpkg -i cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb
sudo apt update
sudo apt install cuda


# Add the following lines into the bashrc
vim ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/cuda/lib64
export PATH=$PATH:/usr/local/cuda-8.0/bin


# Reboot the computer

# Check whether the Video Card is installed and running properly: 
nvidia-smi


# We are using a AV Mako camera, then we have to include its support in the OpenCV installation. Go directly to the OpenCV setup this is not your case.
# We use the AVT Gige SDK for the camera. The new VIMBA SDK replaced the AVT Gige, but the OpenCV version we are using does not support it yet.
# We followed the steps from this link: https://github.com/sightmachine/SimpleCV/wiki/Allied-Vision-(AVT)-GigE-Camera-Installation-Guide-for-Ubuntu-Linux

# Download the SDK "PvAPI SDK for Linux v1.28" from: https://www.alliedvision.com/en/support/software-downloads.html
# Choose: "Legacy Software (SDKs, apps, adapters, and interfaces)"
# Unzip it at /opt with writing permission and:

cd AVT\ GigE\ SDK/inc-pc
sudo cp * /usr/local/include/

cd ../lib-pc/x64/4.4
sudo cp * /usr/local/lib/
cd ../../../bin-pc/x64
sudo cp *.so /usr/local/lib/
sudo cp SampleViewer CLIpConfig /usr/bin


# Open the /etc/ld.so.conf file:
sudo nano /etc/ld.so.conf
# Add the following line in the opened file:
/usr/local/lib


sudo ldconfig
sudo apt-get install libjpeg62

# END of AVT Gige SDK config ----------------------------------------------------------------------




# Opencv setup
sudo apt-get install cmake cmake-qt-gui


# Download the OpenCV from the specific fork:
mkdir ~/code
mkdir ~/code/opencv-3.1.0
cd ~/code/opencv-3.1.0
git clone https://github.com/daveselinger/opencv.git .
git checkout 3.1.0-with-cuda8



# Dependencies installation:

sudo apt install --assume-yes build-essential cmake git pkg-config unzip ffmpeg qtbase5-dev python-dev python3-dev python-numpy python3-numpy
sudo apt install libhdf5-dev
sudo apt install --assume-yes libgtk-3-dev libdc1394-22 libdc1394-22-dev libjpeg-dev libpng12-dev libtiff5-dev libjasper-dev
sudo apt install --assume-yes libavcodec-dev libavformat-dev libswscale-dev libxine2-dev libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt install --assume-yes libv4l-dev libtbb-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev
sudo apt install --assume-yes libvorbis-dev libxvidcore-dev v4l-utils


# Configure CMake:

cd ~/code/opencv-3.1.0
mkdir build
cd build


# WITH PVAPI support for GiGE Cameras
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_CUDA=ON -D WITH_CUBLAS=ON -D  WITH_PVAPI=ON -D PVAPI_LIBRARY="/usr/local/lib/libPvAPI.a" -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D BUILD_PERF_TESTS=OFF -D BUILD_TESTS=OFF -DCUDA_NVCC_FLAGS="-D_FORCE_INLINES" ..


# WITHOUT PVAPI support
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_CUDA=ON -D WITH_CUBLAS=ON -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D BUILD_PERF_TESTS=OFF -D BUILD_TESTS=OFF -DCUDA_NVCC_FLAGS="-D_FORCE_INLINES" ..


# Build and install OpenCV
cd ~/code/opencv-3.1.0/build
make -j $(($(nproc) + 1))
sudo make install

sudo ldconfig

```


# Practical comments

1) Make sure you have not used the kilogui before starting the KilobotArena, because it affects the OHC communication with the KilobotArena. In case you have used the kilogui, reboot the computer.

2) Change the camera dimensions in the kilobottracker_singlecamera.cpp, the defines IMG_WIDTH and IMG_HEIGHT, according to your camera configuration.

3) In the file kilobottracker_singlecamera.cpp, line 150, change the Rectangle accordingly in order to have the image adjusted to your arena. In case your camera is already focusing only the arena, comment the line:  image = image(Rect(370,70,1400,1400));


