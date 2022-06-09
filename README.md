# SelfDrivingCar
Final Year AI and Embedded Systems Project.

Requirements: 
Hardware:
Car Chassis: Eco-skeleton of car
Arduino Uno Microcontroller
Raspi Camera: For image detection
Raspberry pi Microcontroller
Portable Power Supply: To power the car
L298 Dual Channel H-Bridge: Controlling motors
USB cables and Jumper Wires: Connecting devices
Software and Services:
Open CV
Arduino IDE
Raspberry Pi GUI
Geany programming environment.
IP scanner
Remote Desktop Connection

Pre-requisites:
Installing opencv4
>sudo apt-get purge wolfram-engine
>sudo apt-get purge libreoffice*
>sudo apt-get clean
>sudo apt-get autoremove
>sudo raspi-config
>sudo reboot
>df -h
#update raspberry pi3
>sudo apt-get upgrade
>sudo apt-get update
>sudo nano /etc/dphys-swapfile
(set this value CONF_SWAPSIZE=2048)
>sudo /etc/init.d/dphys-swapfile stop
>sudo /etc/init.d/dphys-swapfile start
#install prerequisite
[compiler]     >sudo apt-get install build-essential cmake pkg-config                                               //developer package
               >sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
               >sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
               >sudo apt-get install libxvidcore-dev libx264-dev
[required]     >sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
[optional]     >sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
               >sudo apt-get install libgtk2.0-dev libgtk-3-dev (gui of open cv gtk package)
               >sudo apt-get install libatlas-base-dev gfortran (matrix function)
#create opencv directory and clone opencv and opencv_contrib
>mkdir opencv
>cd ~/opencv
>git clone https://github.com/opencv/opencv.git
>git clone https://github.com/opencv/opencv_contrib.git
#create build directory
>mkdir build
>cd build
#create make file using CMAKE (observe verbose, here you can find path for openCV install directories)
>cmake -D CMAKE_BUILD_TYPE=Release -DCMAKE_EXTRA_MODULES_PATH=../opencv_contrib/modules/ -D CMAKE_INSTALL_PREFIX=/usr/local ../opencv -DBUILD_opencv_java=OFF -DBUILD_opencv_python2=OFF -DBUILD_opencv_python3=OFF


Implementation:
This is a Self Driving Car lane management project.
The raspberry pi camera mounted on the car takes continuous frames in BRG format and each frame is analyzed by the algorithm. 
Each frame taken in has a certain area of importance in the shape of the trapezium. 
This area of importance is the only part of the image that will undergo analysis. 
The camera mounted is at an angle with the lens making an acute angle with the horizontal facing downwards. 
This is done so that we can convert the trapezium image into a false bird eye view image by skewing the area of importance i.e. skewing a trapezium into a rectangle. 
This bird eye view image helps us analyze the lane better. 
To get better accuracy as well as to get rid of unnecessary colors, we convert the Bird eye view BGR image into grayscale image. 
Now this grayscale image has 3 lines drawn in it which represent the lanes. 
Now when the car moves on the track, the white lines of the track which also appear as white in the grayscale are overlapped with the lines drawn in the grayscale image after calibration phase. 
This is how the car understands how to maintain a lane. 
Calibration is a very crucial step because it decides whether the car will work on the track or not, giving very little margin for error.
The entire working of motors is managed by the Arduino. 
The Arduino code contains instructions about what turn to take and how the turn needs to be taken and at what degree. 
The Arduino which is basically a slave device gets its instruction from Raspberry pi who is a master device responsible for running the above mentioned algorithm. 
There are basically 3 left turns and 3 right turns, forward, backward, u-turn and lane end commands coded in Arduino. 
Which turn is necessary in what situation is decided by what the car needs to do to make the lines in grayscale image overlap with the lanes.
When the car’s AI detects that there are no more lanes ahead, it executes the lane end command thus stopping the car in its tracks. 

