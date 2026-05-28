---
title:       "Using `ffmpeg` on Video Files"
description: ""
tags:        [ffmpeg]
redirect_from:
 - /2024/08/14/ffmpeg-video.html
---



- TOC
{:toc}

## Detecting Interlace vs Progressive[^1]
```
ffmpeg -vf idet -frames:v 360 -an -f rawvideo -y /dev/null -i input.mp4
```
Look for the number of `TFF` and `BFF` (Top/Bottom First Field) detected.  If they are non-zero, then the video is interlaced.

**Explanation:** Runs the `idet` filter on 360 frames of video, ignoring audio, forcing rawvideo, and redirecting output to `/dev/null`.


## Preserving Interlace with H264
Because `ffmpeg` and `libx264` default to progressive (non-interlaced) video, in order to preserve interlace when dealing with an already interlaced video do the following:

```
ffmpeg -i input.mp4 -c:v libx264 -flags +ildct+ilme -x264opts "tff=1" -c:a copy output.mp4
```

The `-flags +ildct+ilme` flags enable interlaced DCT (Discrete Cosine Transform) and interlaced motion estimation.

Optionally, specifying `-x264opts "tff=1"` sets the video as top-field-first interlaced. If it is bottom-field-first, then use `tff=0`.

**Note:** Re-scaling interlaced video is very finnicky.  Do careful quality control checks on interlaced video that is resized or otherwise filtered before deleting source files.


## De-interlacing
```
ffmpeg -i input.mp4 -vf "yadif=1:-1:1" -c:v libx264 -c:a copy output.mp4
```

The `yadif` filter[^2] set to output one frame per field (doubling the frame rate), auto-detect TFF or BFF, and only deinterlace frames marked as interlaced.

Include `fps=fps=30` (or whatever the original frame rate was) in the filter chain to preserve original frame rate.

Alternately, use `yadif=0:-1:1` to maintain current framerate without needing the `fps` filter. 


## Re-Interlacing
Sometimes an interlaced video is converted to progressive without any de-interlace filter being used.  It can be reconfigured as an interlaced video with the `tinterlace` filter.  

### Top Field First (TFF)
```
ffmpeg -i input.mp4 -vf "tinterlace=interleave_top" -c:v libx264 -x264opts "tff=1" -flags +ildct+ilme -c:a copy output_interlaced.mp4
```

### Bottom Field First (BFF)
```
ffmpeg -i input.mp4 -vf "tinterlace=interleave_bottom" -c:v libx264 -x264opts "tff=0" -flags +ildct+ilme -c:a copy output_interlaced_bff.mp4
```

**Note:** If you pick the wrong one it will be obvious when watching fast motions in hte resulting video.


## Scale[^5]




## Crop[^6]
In the form `crop=w:h:x:y`, where `x` is zero indexed and starts at the left edge and y is zero indexed from the upper edge.

To trim one pixel from the left, three from the right, two from the top, and one from the bottom on a 640x480 video:
```
ffmpeg -i input.mp4 -vf "crop=636:477:1:2" output.mp4
```

In the form `crop=w:h`, the cropped size is taken out of the middle since `x` and `y` default to `(in_w-out_w)/2` and `(in_h-out_h)/2`.


## Storage vs Display Aspect Ration




## Gaussian Blur[^3]




## Unsharp Mask[^4]




## Adjust Audio / Subtitle Timing



## Extracting Closed Captions[^7]
Closed Captions are different than other subtitle formats.  They are encoded as part of the video stream itself.

### Extract CC to SRT file
```
ffmpeg -f lavfi -i "movie=input.mp4[out0+subcc]" -map s "output.srt"
```

**Note:** The CC data is still in the video stream after this command.

### Remove CC data from video stream

**For AVC encoded video:**
```
ffmpeg -i "INPUT.mp4" -c copy -map 0 -bsf:v filter_units=remove_types=6 "OUTPUT.mp4"
```

**For HEVC encoded videos:***
```
ffmpeg -i "INPUT.mp4" -c copy -map 0 -bsf:v filter_units=remove_types=39 "OUTPUT.mp4"
```


### PAL to NTSC
Historically many NTSC 23.976 fps films have been run at the PAL speed of 25fps because it is close enough that people cannot tell the difference.

This ends up making the films have a slightly shorter run time, and slowly desynchronizes NTSC subtitles the further into the film you get. 

```
ffmpeg -i input.mp4 -vf "setpts=25025/24000*PTS" -af "atempo=0.959040959040959" -r 24000/1001 -c:v libx264 -c:a aac output.mp4
```

**Note:** Both audio and video must be re-encoded to fix this. Update the `-c:v` and `-c:a` segments as needed.



[^1]: [StackOverflow - Progressive or Interlace detection in ffmpeg](https://stackoverflow.com/questions/24945378/progressive-or-interlace-detection-in-ffmpeg#62543887)

[^2]: [ffmpeg Documentation --- Filters - yadif](https://ffmpeg.org/ffmpeg-filters.html#yadif-1)

[^3]: [ffmpeg Documentation --- Filters - gblur](https://ffmpeg.org/ffmpeg-filters.html#gblur)

[^4]: [ffmpeg Documentation --- Filters - unsharp](https://ffmpeg.org/ffmpeg-filters.html#unsharp)

[^5]: [ffmpeg Documentation --- Filters - scale](https://ffmpeg.org/ffmpeg-filters.html#scale)

[^6]: [ffmpeg Documentation --- Filters - crop](https://ffmpeg.org/ffmpeg-filters.html#crop)

[^7]: [FFmpeg -- How to Extract and Remove Closed Captions](https://trac.ffmpeg.org/wiki/HowToExtractAndRemoveClosedCaptions)

[^8]: [FFmpeg-user -- Convert PAL to NTSC](https://ffmpeg.org/pipermail/ffmpeg-user/2013-July/016098.html)







