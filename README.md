# my-ros2-cpp-alt-opencv

This repo shows how you can switch your ROS2 package to use a
different version of OpenCV installed locally.  See instruction for
opencv installation at the end.

## How to Compile the ROS2 package

First, put the source code tree directory inside your ROS2 workspace's `src/` directory.

### Option 1: Use system's OpenCV

```
rm -rf build/my-ros2-cpp-alt-opencv
colcon build --packages-select my-ros2-cpp-alt-opencv
```

### Option 2: Use locally installed OpenCV

Modify `CMakeLists.txt` and replace the path below with your local
opencv's installation directory.

```
  set(opencv3_install_dir "~/opencv3/install/")
```

Then, proceed to compile:


```
rm -rf build/my-ros2-cpp-alt-opencv
colcon build --packages-select my-ros2-cpp-alt-opencv --cmake-args -DUSE_ALT_OPENCV=1 
```


## How to Run:

### Option 1: Built with system's OpenCV

```
# source ROS 2 workspace environment
source install/setup.bash

# run the executable
ros2 run my-ros2-cpp-alt-opencv my_opencv
```

### Option 2: Built with locally installed OpenCV

Make sure you change example below with your locally installed opencv directory.

```
# source ROS 2 workspace environment
source install/setup.bash

# run the executable with the LD_LIBRARY_PATH prefix.  Remember to
# replace "~/opencv3/install" with your local opencv installation directory.
LD_LIBRARY_PATH=~/opencv3/install/lib/:$LD_LIBRARY_PATH \
  ros2 run my-ros2-cpp-alt-opencv my_opencv
```

## How to install OpenCV locally
First, download the source.  In this example, we are building OpenCV 3.4.0.

```
mkdir ~/opencv3/
wget https://github.com/opencv/opencv/archive/3.4.0.zip
wget https://github.com/opencv/opencv_contrib/archive/3.4.0.zip
unzip opencv.zip
unzip opencv_contrib.zip
mv opencv-3.4.0 opencv
mv opencv_contrib-3.4.0 opencv_contrib
```

Then, compile.  

```
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=~/opencv3/install \
    -D WITH_CUDA=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv3/opencv_contrib/modules \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D WITH_QT=ON \
    -D WITH_GTK=OFF \
    -D WITH_OPENGL=ON \
    -D BUILD_EXAMPLES=ON ../opencv/
```

You may encounter compilation errors due to the newer
C++ compiler catching more code errors.  Be sure to fix those error
and continue.  I had to fix a few places in the following files:

    opencv_contrib/modules/stereo/src/descriptor.cpp
    opencv/samples/cpp/cloning_gui.cpp
    samples/cpp/tutorial_code/photo/seamless_cloning/cloning_gui.cpp
    samples/cpp/tutorial_code/ImgProc/.../changing_contrast_brightness_image.cpp


Once everything builds, install the local opencv by:

``` 
make install
```

You should now see `~/opencv3/install/`

ref: https://pyimagesearch.com/2018/05/28/ubuntu-18-04-how-to-install-opencv/

