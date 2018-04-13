# Real-Time-Streaming-to-YouTube-RTMP-Sever-using-IP-Zavio-Camera
This will transfer an RTSP stream to RTMP server using Raspberry Pi 3. The ff-mpeg is used for this purpose. All the documentation is included on my github profile


Several customers at CCTV Camera Pros contacted me recently asking if it was possible 
to use a Zavio IP camera for YouTube live streaming. RTSP is the video streaming protocol
 built into most network IP cameras, including Zavio.
 After some quick research, I found that RTSP (Real Time Streaming Protocol) is not directly
 supported by YouTube. YouTube uses RTMP (RTMP Real-Time Messaging Protocol).

ffmpeg makes it possible !


make sure that you have an IP camera that makes available a URL to 
directly access an RTSP stream. All Zavio IP cameras provide this.



You should configure a static LAN IP address for your IP camera.


Example google search: “Linksys router IP reservation”. That should point you in the right direction.

Make note of the LAN IP address of your IP camera. It will be something like 192.168.0.100.

Consult the manual for your IP camera to get the port number and URL that 
is used for its RTSP video stream.

For Zavio IP cameras, you will use something like this.
rtsp://192.168.0.100:554/video.pro1

In this example, my IP camera’s IP address is 192.168.0.119. The RTSP port is 554 and the URL is /video.pro1.


Login to YouTube dashboard by going here. https://www.youtube.com/dashboard?o=U
Click on the Live Streaming > Stream now link on the left hand side menu.
On the Stream Now page, scroll down towards the bottom of the page to find the Encoder Setup section. 
You need the Server URL and Stream name/key values to configure the BASH script that will be used to start and monitor 
the FFmpeg process on your Raspberry Pi. Your stream URL will look something like this: rtmp://a.rtmp.youtube.com/live2
Your stream key will look something like this: d5gz-5d1p-143w-qbha




Pi Setup: Fresh NOOBS

Connect pi with same network of IP camera !


go to terminal:



Run this command to install some libraries that are needed for the installation.

sudo apt-get install git autoconf automake build-essential checkinstall libass-dev libgpac-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev librtmp-dev libspeex-dev libtheora-dev libtool libvorbis-dev pkg-config texi2html zlib1g-dev yasm


[X264 is a library needed by FFmpeg. Run this command to install the X264 libraries on Raspberry Pi.]


$ cd /usr/src
$ sudo git clone git://git.videolan.org/x264
$ cd x264
$ sudo ./configure --host=arm-unknown-linux-gnueabi ––enable-static ––disable-opencl
$ sudo make
$ sudo make install


FDK-AAC is another video library that FFmpeg needs to use. Just like the X264 libs, 
you should install FDK-AAC before compiling FFmpeg. Run these commands to install FDK-AAC on Raspberry Pi


$ cd /usr/src
$ sudo git clone --depth 1 git://github.com/mstorsjo/fdk-aac.git
$ cd fdk-aac
$ sudo autoreconf -fiv
$ sudo ./configure --disable-shared
$ sudo make
$ sudo make install


In my testing, I found that the FFmpeg process would stop from time to time for unknown reasons.

To compensate for this, I wrote a simple BASH script that checks to make sure FFmpeg is running, 

and if it is not, it will restart it. It is a very simple process monitoring script.

Here is the BASH script.



(see my screenshot) here !




Download the source code of my bash script from here: "startffmpeg.sh"
(provided in documentation files)

Edit the script and replace the values for these variables.

RTSP_URL - replace this with the URL that your IP camera uses. If you are using a Zavio IP camera, 
this value does not need to change.

YOUTUBE_URL - most likely this does not need to change, but confirm it in your YouTube dashboard.

YOUTUBE_KEY - this is almost like a password so protect it wisely. You must change this value. The above key is not real.

After you edit the script, save it and set the permissions of it to be executable. You can use this command: 

chmod 755 start.sh

You can test the script by running it with this command: sudo ./start.sh

When you run this command, you should see live video on the stream now page in your YouTube dashboard.

This is what the video stream looks like in the YouTube dashboard.








Setup Cron Job

Cron is a time based job scheduler for Unix and Linux systems. I configured a cron job to run the start.sh script every minute. The script checks to make sure the FFmpeg process is running. and if it is not, it restarts it. If FFmpeg is already running, the script does not do anything.

To setup the cron job on your Raspberry Pi.

Run this command.
crontab -e
This will open the cron configuration file for user "pi". Add the following line to the file.
* * * * * sudo /home/pi/start3.sh
Then save the file and exit the editor you are using.
Cron is now configured and the start.sh script will run every minute.
Even if your Raspberry Pi reboots, the cron will run and automatically start the FFmpeg video encoding process.

























