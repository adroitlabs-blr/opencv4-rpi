# opencv4-rpi
Optimize opencv4 for Raspberry pi ( raspbian stretch )

$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install build-essential cmake pkg-config -y
$ sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev -y
$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev -y
$ sudo apt-get install libxvidcore-dev libx264-dev -y
$ sudo apt-get install libgtk2.0-dev libgtk-3-dev -y
$ sudo apt-get install libcanberra-gtk* -y
$ sudo apt-get install libatlas-base-dev gfortran -y
$ sudo apt-get install python2.7-dev python3-dev -y

download https://github.com/opencv/opencv/archive/4.0.1.zip
$ cd /home/pi

$ mkdir opencv_build

$ cd opencv_build
$ git clone https://github.com/opencv/opencv_contrib.git
$ unzip 4.0.1.zip
$ cd opencv-4.0.1
$ mkdir build_rpi3_release_fp_tbb
$ cd build_rpi3_release_fp_tbb

build with modules from opencv_contrib set OPENCV_EXTRA_MODULES_PATH=/home/pi/opencv_build/opencv_contrib/modules/
set BUILD_DOCS for building documents

Open the file /etc/dphys-swapfile and edit CONF_SWAPSIZE variable:

CONF_SWAPSIZE=1024 (change it back CONF_SWAPSIZE=100 after the build )

$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
$ htop


$ sudo rapi-config - disable GUI (to save RAM ) and  reboot 
 

$ cmake -DCMAKE_CXX_FLAGS="-DTBB_USE_GCC_BUILTINS=1 -D__TBB_64BIT_ATOMICS=0" -DENABLE_VFPV3=ON -DENABLE_NEON=ON -DBUILD_TESTS=OFF -DWITH_TBB=ON -DCMAKE_BUILD_TYPE=Release -DOPENCV_EXTRA_MODULES_PATH=/home/pi/opencv_build/opencv_contrib/modules/ ..
$ make -j4
build process stuck at 96%!

$ make -j1 ( use single core for build, slow but completes remaining build without any issue) 

$ sudo apt-get install checkinstall
$ echo "opencv 4.0.1 build_rpi3_release_fp_tbb" > description-pak
$ echo | sudo checkinstall -D --install=no --pkgname=opencv --pkgversion=4.0.1 --provides=opencv --nodoc --backup=no --exclude=$HOME

error: Makefile:3338: recipe for target 'cmake_check_build_system' failed

This is a bug in installwatch, a component of checkinstall 

giuliomoro - suggests  that it's bug with checkinstall itself, update checkinstall

$ cd 
$ git clone https://github.com/giuliomoro/checkinstall
$ cd checkinstall
$ make install

$ cd /home/pi/opencv_build/opencv-4.0.1/build_rpi3_release_fp_tbb

$ echo | sudo checkinstall -D --install=no --pkgname=opencv --pkgversion=4.0.1 --provides=opencv --nodoc --backup=no --exclude=$HOME

Done. The new package has been saved to

 /home/pi/opencv_build/opencv-4.0.1/build_rpi3_release_fp_tbb/opencv_4.0.1-1_armhf.deb
 You can install it in your system anytime using: 

      dpkg -i opencv_4.0.1-1_armhf.deb


$ sudo dpkg -i opencv_4.0.1-1_armhf.deb

Open the file /etc/dphys-swapfile and edit CONF_SWAPSIZE variable:

CONF_SWAPSIZE=100 

$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
$ htop

$ python3
>>> import cv2
>>> cv2.__version__
'4.0.1'
>>> 

