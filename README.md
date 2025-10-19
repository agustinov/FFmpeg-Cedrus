## FFmpeg-Cedrus

FFmpeg-Cedrus is a FFmpeg fork with the Allwinner open-source Cedrus H264 hardware encoder.
Supports the Allwinner H3 CPU. Tested on Orange Pi PC Plus, Orange Pi Lite.
Optimal for Ubuntu 16.04 Xenial with the legacy Linux kernel 3.4.
On Armbian with the mainline Linux kernel 5.x/6.x the Cedrus H264 hardware encoder may not work.
For mainline Linux you can search for FFmpeg with the proprietary blob CedarX libraries.


## References

https://github.com/alcantor/FFmpeg/tree/sunxi-cedrus added the base Allwinner Sunxi Cedrus H264 encoder to FFmpeg 2.3.

https://github.com/stulluk/FFmpeg-Cedrus added h264enc from jemk/cedrus.

https://github.com/uboborov/ffmpeg_h264_H3 added the Allwinner H3 support and made diff for stulluk/FFmpeg-Cedrus.

https://github.com/gtalusan/FFmpeg-Cedrus merged the diff files from uboborov/ffmpeg_h264_H3 back into FFmpeg.

https://github.com/avafinger/ffmpeg-3.3.4_cedrus264 ported to FFmpeg 3.3.

https://github.com/danielkucera/FFmpeg/tree/cedrus264 ported to FFmpeg 3.4.

https://github.com/divis1969/FFmpeg/tree/2.8-cedrus reworked the Cedrus H264 encoder to use the libcedrus API in FFmpeg 2.8.

https://github.com/agustinov/FFmpeg-Cedrus fixed the green artefact at the frame bottom on 1080p resolution, 
and merged all the listed above FFmpeg-Cedrus forks into the one repository.
FFmpeg 2.3-cedrus, 2.8-libcedrus, 3.4-cedrus branches are available.


## Usage

## 1. Downloading

Clone FFmpeg-Cedrus from GitHub:

	git clone https://github.com/agustinov/FFmpeg-Cedrus.git
	cd FFmpeg-Cedrus
	
You can stay on the main 2.3-cedrus branch or switch to the 3.4-cedrus or 2.8-libcedrus branch.

Or manually download (select the 2.3-cedrus, 3.4-cedrus or 2.8-libcedrus branch) and unzip the FFmpeg-Cedrus zip package.

	unzip FFmpeg-Cedrus-*branch*.zip
	cd FFmpeg-Cedrus-*branch*


## 2. Installing dependencies

Update your Linux packages list:

	sudo apt-get update

Install common libraries used in FFmpeg:

	sudo apt-get install libmp3lame-dev libpulse-dev libv4l-dev libx264-dev

If you want to use hardware decoding, install additional libraries:

	sudo apt-get install libpixman-1-dev libvdpau-dev libxext-dev libxt-dev

Also install the Allwinner Sunxi libraries for hardware decoding or for the 2.8-libcedrus branch
(adding the Allwinner Sunxi repository may be required):

	sudo apt-get install libcedrus1, libvdpau-sunxi1

Or manually download the libcedrus, libvdpau-sunxi libraries sources, then build and install them.

For the 2.3-cedrus branch you can use the standard Linux-sunxi libcedrus and libvdpau-sunxi libraries
from https://github.com/linux-sunxi/libcedrus and https://github.com/linux-sunxi/libvdpau-sunxi.

For the 3.4-cedrus and 2.8-libcedrus branches you need to use the modified libcedrus and libvdpau-sunxi libraries
from https://github.com/agustinov/libcedrus and https://github.com/agustinov/libvdpau-sunxi:

	sudo apt-get install libpixman-1-dev libxt-dev libxext-dev
	git clone https://github.com/agustinov/libcedrus.git
	cd libcedrus
	make
	sudo make install
	cd ..
	git clone https://github.com/agustinov/libvdpau-sunxi.git
	cd libvdpau-sunxi
	make
	sudo make install
	cd ..

If you want to build ffplay, you also need to install the development version of SDL.
For the 2.3-cedrus and 2.8-libcedrus branches install libsdl1.2-dev:

	sudo apt-get install libsdl1.2-dev

For the 3.4-cedrus branch install libsdl2-dev:

	sudo apt-get install libsdl2-dev

All dependencies must be installed before configuring the FFmpeg build.
If you have already configured the FFmpeg build and then installed additional dependencies,
you need to run ./configure again (with the same options) and then rebuild FFmpeg.


## 3. Building FFmpeg-Cedrus

After installing all dependencies, configure FFmpeg before building (2.3-cedrus or 3.4-cedrus branch):

	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libmp3lame --enable-libpulse --enable-libv4l2 --enable-libx264 --enable-hwaccels --enable-vdpau

If you want to build the 2.8-libcedrus branch, use the additional --enable-libcedrus option:

	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libmp3lame --enable-libpulse --enable-libv4l2 --enable-libx264 --enable-hwaccels --enable-vdpau --enable-libcedrus

If you have GCC version 11 or higher and get the error "C compiler test failed", see the config.log file.
If you see message "cc1: error: ‘-mfloat-abi=hard’: selected architecture lacks an FPU", use the --cpu=armv7-a+fp option:

	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libmp3lame --enable-libpulse --enable-libv4l2 --enable-libx264 --enable-hwaccels --enable-vdpau --cpu=armv7-a+fp

If you get the error: "/usr/bin/ld: libavcodec/file.o: relocation R_ARM_THM_MOVW_ABS_NC against 'symbol' can not be used when making a shared object; recompile with -fPIC 
libavcodec/file.o: error adding symbols: Bad value",
you can use the --disable-shared --enable-static options for a static build 
or clean the previously built files and try using the additional --enable-shared --enable-pic --extra-ldflags=-Wl,-Bsymbolic options for a shared build:

	make distclean
	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libmp3lame --enable-libpulse --enable-libv4l2 --enable-libx264 --enable-hwaccels --enable-vdpau --enable-shared --enable-pic --extra-ldflags=-Wl,-Bsymbolic

After configuration, build FFmpeg (this may take a long time, over an hour):

	make

If you get "make: Warning: File has modification time 123456789 s in the future", just update your system time.

To speedup the building process, use the -j2 option (use 2 CPU cores):

	make -j2

Using 4 CPU cores (make -j4) is not recommended on systems without a cooler due to the risk of CPU overheating.

After building FFmpeg, install it:

	sudo make install

When you have installed FFmpeg, run it:

	ffmpeg

If you want to run compiled FFmpeg on another system or have installed another FFmpeg, it may be useful to build a static application without external dependencies.
Use the --disable-shared --enable-static options for a static build of FFmpeg to avoid conflicts with your currently installed FFmpeg. 

	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libmp3lame --enable-libpulse --enable-libv4l2 --enable-libx264 --enable-hwaccels --enable-vdpau --disable-shared --enable-static
	make

After building your local FFmpeg, you can run it from the local build path without installing:

	./ffmpeg


## 4. Encoding video from file

Typical usage for encoding video from a file:

	ffmpeg -s 1280x720 -f rawvideo -pix_fmt nv12 -i inputfile.nv12 -pix_fmt nv12 -c:v cedrus264 -r 30 -qp 20 encoded_file.mp4

FFmpeg options:

	-s 1280x720 (or -video_size 1280x720) sets the resolution according to the input file
	-f rawvideo sets unencoded raw video
	-pix_fmt nv12 sets the pixel format according to the input file
	-i inputfile sets the file input
	-pix_fmt nv12 is the only pixel format supported by the Cedrus encoder
	-c:v cedrus264 (or -vcodec cedrus264) sets the Allwinner Cedrus H264 hardware encoder
	-r 30 sets the framerate (fps)
	-qp 20 sets the video quality in the range 2-30 (instead of the bitrate), omitting this option may result in poor video quality

If encoding from the AVI inputfile, FFmpeg automatically gets the video format, resolution, framerate, time from the input file.

If you access to the system via SSH terminal or using server Linux without desktop and get "VE open error", 
root privileges (sudo) may be required:

	sudo ffmpeg -s 1280x720 -f rawvideo -pix_fmt nv12 -i inputfile.nv12 -pix_fmt nv12 -c:v cedrus264 -r 30 -qp 20 encoded_file.mp4


## 5. Encoding video from camera

Typical usage for encoding video from a camera:

	ffmpeg -f v4l2 -channel 0 -s 640x480 -i /dev/video0 -pix_fmt nv12 -c:v cedrus264 -r 30 -t 15 -qp 20 test_camera.mp4

FFmpeg options:

	-f v4l2 is the only format supported by the camera
	-channel 0 can be omitted
	-s 640x480 (or -video_size 640x480) sets the resolution according to your camera's capabilities
	-i /dev/video0 sets the camera input
	-c:v cedrus264 (or -vcodec cedrus264) sets the Allwinner Cedrus H264 hardware encoder
	-pix_fmt nv12 is the only pixel format supported by the Cedrus encoder
	-r 30 sets the framerate (fps)
	-t 15 sets the time in seconds (you can omit this option and stop by Ctrl+C)
	-qp 20 sets the video quality in the range 2-30 (instead of the bitrate), omitting this option may result in poor video quality

If you access to the system via SSH terminal or using server Linux without desktop and get "VE open error", 
root privileges (sudo) may be required:

	sudo ffmpeg -f v4l2 -s 640x480 -i /dev/video0 -pix_fmt nv12 -c:v cedrus264 -r 30 -t 15 -qp 20 test_camera.mp4


## 6. Decoding video from file

Typical usage for decoding video from a file:

	ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo decoded_file.avi

FFmpeg options:

	-i inputfile.mp4 sets the file input
	-hwaccel vdpau sets the VDPAU hardware decoder
	-c:v rawvideo disables encoding and copying video without decoding

If decoding from the MP4 inputfile, FFmpeg automatically gets video format, resolution, framerate, time from the input file.

Video decoding is realized not by the hardware decoder built into FFmpeg, but with the VDPAU library.
So you need to ensure that you have installed dependencies: 
libpixman-1-dev, libvdpau-dev, libxext-dev, libxt-dev, libcedrus1, libvdpau-sunxi1,
maybe: SDL (for ffplay), X11 core (for server Linux without desktop).

If you get the error "No device available for decoder: device type vdpau needed for codec h264",
make sure that your FFmpeg includes the h264_vdpau decoder.

In the 2.3-cedrus and 2.8-libcedrus branches the h264_vdpau decoder must be listed in ffmpeg -codecs:

	ffmpeg -codecs | grep -e h264 -e vdpau

In the 3.4-cedrus and 2.8-libcedrus branches the vdpau hardware accelerator must be listed in ffmpeg -hwaccels:

	ffmpeg -hwaccels

If you access to the system via SSH terminal and get the error "Cannot open the X11 display", 
DISPLAY=:0 or DISPLAY=:0.0 prefix may be required:

	DISPLAY=:0.0 ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo decoded_file.avi

If you get the error "Failed to open VDPAU backend libvdpau_nvidia.so: cannot open shared object file: No such file or directory",
VDPAU_DRIVER=sunxi prefix may be required:

	VDPAU_DRIVER=sunxi ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo decoded_file.avi

In some cases your FFmpeg decode usage may look as:

	sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi ./ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo decoded_file.avi

If you are trying to run hardware decoding on the Allwinner H3 in the 3.4-cedrus or 2.8-libcedrus branch,
and get "Error retrieving the data from a VDPAU surface", 
you need to build the libcedrus, libvdpau-sunxi libraries modified for the Allwinner H3 (see *2. Installing dependencies*).

Writing unencoded raw video to a file can be slow with low fps.
If you want to test the hardware decoder with high fps, you can run decoding without writing:

	ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo -f null /dev/null

For mainline Linux kernel 5.x/6.x the VDPAU decoder is not used.
You can search for FFmpeg with the CedarX / VAAPI / DRM / V4L2 request API hardware decoder.


## 7. Playing video from file

If you are using a Linux specialized for your CPU, you can just run:

	mpv inputfile.mp4

If your Linux is not specialized for your CPU, you need to configure your play application by additional options.
For playing a video file your MPV usage may look as:

	sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi mpv -hwdec=vdpau -hwdec-codecs=all -vo=vdpau -fs inputfile.mp4

In this MPV example the VDPAU video output device is used.

If you have a server Linux without X11 desktop and VDPAU, you can try to set the framebuffer video output device.

You can get a list of available MPV video output devices:

	mpv -vo help

In most cases your MPV has several video output devices, such as: opengl, vdpau, xv, sdl, vaapi, x11, drm.
But the framebuffer (fb) video output device is not available.

If you need the framebuffer video output device, you can try MPlayer.
MPlayer has more video output devices than MPV.

You can get a list of available MPlayer video output devices:

	mplayer -vo help

In most cases your MPlayer supports framebuffer video output devices such as: fbdev, fbdev2, directfb.

If you have a server Linux without X11 desktop and VDPAU, 
you can try to run MPlayer in fullscreen mode (-fs) and output to the framebuffer:

	sudo mplayer -vo fbdev -fs inputfile.mp4

For playing a video file in X11 your MPlayer usage may look as:

	sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi mplayer -vo vdpau -vc ffh264vdpau,ffmpeg12vdpau,ffwmv3vdpau,ffvc1vdpau inputfile.mp4

If your MPV or MPlayer doesn't support hardware acceleration when decoding,
you can build FFmpeg with hardware acceleration support and then rebuild MPV or MPlayer using your built FFmpeg.

If you want to play via ffplay in X11, you can configure and build FFmpeg with the --enable-ffplay option.
If you want to build ffplay, you also need to install the development version of SDL before configuring and building FFmpeg.

In common case ffplay usage in fullscreen mode is:

	ffplay -fs inputfile.mp4

If you have difficult conditions, for playing a video file your ffplay usage may look as:

	sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi ./ffplay -vcodec h264_vdpau -fs inputfile.mp4

If you get "decode_slice_header error no frame!", you can pipe video from ffmpeg to ffplay:

	sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi ./ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo -f avi pipe: | sudo DISPLAY=:0.0 VDPAU_DRIVER=sunxi ./ffplay -i pipe:

If all these prefixes are looking scary, you can export the environment variables:

	export DISPLAY=:0.0
	export VDPAU_DRIVER=sunxi

After you export the environment variables, then you can run without prefixes:

	ffmpeg -hwaccel vdpau -i inputfile.mp4 -c:v rawvideo -f avi pipe: | ffplay -i pipe:

If you have a server Linux without X11 desktop and VDPAU, or if you couldn't build and run ffplay, 
you can output video to the framebuffer directly from ffmpeg:

	ffmpeg -i inputfile.mp4 -c:v rawvideo -pix_fmt bgra -f fbdev /dev/fb0

FFmpeg doesn't support fullscreen mode (-fs), but you can manually set the output resolution according to your display (rescaling may be slow):

	ffmpeg -i inputfile.mp4 -c:v rawvideo -pix_fmt bgra -s 1280x720 -f fbdev /dev/fb0

For more information you can also see *6. Decoding video from file*.
