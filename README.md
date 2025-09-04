FFmpeg with Allwinner HW h264 cedrus encoder support for Allwinner H3 CPU

These are modified sources of the port of **FFmpeg 2.3** with Allwinner HW H264 cedrus encoder originally located here: https://github.com/Alcantor/FFmpeg/tree/sunxi-cedrus.

https://github.com/stulluk/FFmpeg-Cedrus mentioned jemk and alcantor but didn't change any code.

https://github.com/uboborov/ffmpeg_h264_H3 added Allwinner H3 support and made diff for https://github.com/stulluk/FFmpeg-Cedrus.

https://github.com/agustinov/FFmpeg-cedrus added H3 diff files back to FFmpeg, fixed green artefact on 1080p resolution and restored reference to original https://github.com/Alcantor/FFmpeg/tree/sunxi-cedrus.

https://github.com/avafinger/ffmpeg-3.3.4_cedrus264 ported to FFmpeg 3.3.

https://github.com/danielkucera/FFmpeg/tree/cedrus264 ported to FFmpeg 3.4.

Configure FFmpeg and then build it.

    ./configure --prefix=/usr --enable-nonfree --enable-gpl --enable-version3 --enable-vdpau --enable-libx264 --enable-libmp3lame --enable-libpulse --enable-libv4l2
	
    make && sudo make install
