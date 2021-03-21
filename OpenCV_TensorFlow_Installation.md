# This guide installs OpenCV 4.5.1 and TensorFlow on Jetson AGX Xavier
* Jetpack 4.5
* Python 3.6
* OpenCV 4.5.1
* TensorFlow 
# Dependencies check
First make sure get the following third-party dependencies
```
sudo apt-get install build-essential cmake git unzip pkg-config
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libgtk2.0-dev libcanberra-gtk*
sudo apt-get install python3-dev python3-numpy python3-pip
sudo apt-get install libxvidcore-dev libx264-dev libgtk-3-dev
sudo apt-get install libtbb2 libtbb-dev libdc1394-22-dev
sudo apt-get install libv4l-dev v4l-utils
sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
sudo apt-get install libavresample-dev libvorbis-dev libxine2-dev
sudo apt-get install libfaac-dev libmp3lame-dev libtheora-dev
sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt-get install libopenblas-dev libatlas-base-dev libblas-dev
sudo apt-get install liblapack-dev libeigen3-dev gfortran
sudo apt-get install libhdf5-dev protobuf-compiler
sudo apt-get install libprotobuf-dev libgoogle-glog-dev libgflags-dev
```
# Doenwnload OpenCV
Once finished the third-party dependencies, download the OpenCV from https://opencv.org/releases/
```
# download the latest version
cd ~
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
# unpack
unzip opencv.zip
unzip opencv_contrib.zip
mv opencv-4.5.1 opencv
mv opencv_contrib-4.5.1 opencv_contrib
# clean up the zip files
rm opencv.zip
rm opencv_contrib.zip
```

# Build make
Setup the working directory.
```
cd ~/opencv
mkdir build
cd build
```
Setup the Python3 environment: add “export OPENBLAS_CORETYPE=ARMV8” at the end of ~/.bashrc file. Then run the command on next page, once finished. It should show something like this:
![image](https://user-images.githubusercontent.com/16247078/111257254-ca6d4e00-85e8-11eb-9760-f154bf5c179d.png)

then run cmake with the following:
```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=~/Downloads/opencv_contrib/modules \
        -D EIGEN_INCLUDE_PATH=/usr/include/eigen3 \
        -D WITH_CUDA=ON \
        -D CUDA_ARCH_BIN=7.2 \
        -D CUDA_ARCH_PTX="" \
        -D WITH_CUDNN=ON \
        -D WITH_CUBLAS=ON \
        -D ENABLE_FAST_MATH=ON \
        -D CUDA_FAST_MATH=ON \
        -D OPENCV_DNN_CUDA=ON \
        -D ENABLE_NEON=ON \
        -D WITH_QT=OFF \
        -D WITH_OPENMP=ON \
        -D WITH_OPENGL=ON \
        -D BUILD_TIFF=ON \
        -D WITH_FFMPEG=ON \
        -D WITH_GSTREAMER=ON \
        -D WITH_TBB=ON \
        -D BUILD_TBB=ON \
        -D BUILD_TESTS=OFF \
        -D WITH_EIGEN=ON \
        -D WITH_V4L=ON \
        -D WITH_LIBV4L=ON \
        -D OPENCV_ENABLE_NONFREE=ON \
        -D INSTALL_C_EXAMPLES=OFF \
        -D INSTALL_PYTHON_EXAMPLES=OFF \
        -D BUILD_NEW_PYTHON_SUPPORT=ON \
        -D BUILD_opencv_python3=TRUE \
        -D HAVE_opencv_python3=ON \
        -D PYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
        -D OPENCV_GENERATE_PKGCONFIG=ON \
        -D BUILD_EXAMPLES=ON \
        -D PYTHON3_EXECUTABLE=$(which python3) \
        -D PYTHON_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
        -D PYTHON_INCLUDE_DIR2=$(python3 -c "from os.path import dirname; from distutils.sysconfig import get_config_h_filename; print(dirname(get_config_h_filename()))") \
        -D PYTHON_LIBRARY=$(python3 -c "from distutils.sysconfig import get_config_var;from os.path import dirname,join ; print(join(dirname(get_config_var('LIBPC')),get_config_var('LDLIBRARY')))") \
        -D PYTHON3_NUMPY_INCLUDE_DIRS=$(python3 -c "import numpy; print(numpy.get_include())") \
        -D PYTHON3_PACKAGES_PATH=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") ..
```
# Make
Run the following command on AGX Xavier
```
make -j7
```
one finished, it should show something like this:

![image](https://user-images.githubusercontent.com/16247078/111257387-0bfdf900-85e9-11eb-8fb9-e042da8991ce.png)

Notice the opencv_python3 is generated. Then run the following command to make install:
```
sudo make install
sudo ldconfig
# cleaning (frees 300 KB)
make clean
sudo apt-get update
```

# Clean the files to save space
```
sudo rm -rf ~/opencv
sudo rm -rf ~/opencv_contrib
```
#Check Jetson status
Install the jtop by using the following commands:
```
sudo -H pip3 install -U jetson-stats
sudo reboot
# start the app with the simple command
jtop
```
If everything went well, you should see this on the info page:

![image](https://user-images.githubusercontent.com/16247078/111257593-672feb80-85e9-11eb-832a-e7a24538af0e.png)

# Install TenserFlow from nVidia
Environment: Python 3.6 + JetPack 4.5
```
sudo apt-get install libhdf5-serial-dev hdf5-tools libhdf5-dev
sudo apt-get install zlib1g-dev zip libjpeg8-dev liblapack-dev 
sudo apt-get install libblas-dev gfortran
sudo apt-get install python3-pip
sudo pip3 install -U pip testresources setuptools
sudo pip3 install -U numpy future mock h5py keras_preprocessing 
sudo pip3 install -U keras_applications gast futures protobuf pybind11

# TF-2.x
$ sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v45 tensorflow
```
