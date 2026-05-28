---
title:       "Using `ffmpeg` to Process Audiobooks"
description: ""
tags:        [ffmpeg, Audio]
redirect_from:
 - /2024/06/24/ffmpeg-audiobook.html
---

`ffmpeg` is a useful tool to convert audiobooks and other spoken word audio into smaller files.

- TOC
{:toc}

## Actionable Takeaways
If the audiobook is saved as `input.mp4`:

### Single-Pass Process
This works for 99% of audio files:
```
ffmpeg -i input.mp4 -vn -ar 22050 -b:a 32k -c:a libmp3lame -af "pan=mono|c0=.5*c0+.5*c1, highpass=60, lowpass=10000, loudnorm=i=-16: dual_mono=true, silenceremove=start_periods=1: start_silence=2: start_threshold=-30dB: stop_periods=-1: stop_silence=2: stop_threshold=-30dB, silenceremove=detection=peak: stop_periods=-1: stop_threshold=-30dB: stop_duration=1.5" output.mp3
```

### Two-Pass Process
Takes much longer to run and requires more fiddling to get the numbers right for any given audio file.
```
ffmpeg -i input.mp4 -vn -ac 1 -ar 22050 -b:a 32k -c:a libmp3lame -af "afftdn=nf=-25, highpass=60, lowpass=10000, silenceremove=start_periods=1: start_silence=2: start_threshold=-30dB: stop_periods=-1: stop_silence=2: stop_threshold=-30dB, silenceremove=detection=peak: stop_periods=-1: stop_threshold=-30dB: stop_duration=1.5, loudnorm=print_format=json" input.mp3
```

Then run (replacing the relevant values in the command with the values in the JSON output from the previous command):

```
ffmpeg -i input.mp3 -af "loudnorm=I=-20:TP=-1.5:LRA=8:measured_I=-17.0:measured_TP=-2.0:measured_LRA=12.0:measured_thresh=-27.0:offset=1.0" output.mp3
```


## Disable Video
The `-vn` parameter disables any video stream processing.


## Mono Conversion
Spoken word audio does not normally need two stereo channels and space can be saved by only storing a single mono channel.

To use the default algorithm: `-ac 1`

To explicilty control the mix between the channels use: `pan=mono|c0=.5*c0+.5*c1`

*Note:* The `pan` filter is applied immediately in the filter process, but the `-ac 1` parameter is applied at the end of filter processing as the result is encoded.  Depending on the situation, one of these may be more useful than the other.  Converting early to a single channel may be slightly more computationally efficient.


## Sample- and Bit-rates
For spoken word a sample rate of 22050Hz and a bitrate of 32kbps are sufficient.

These are applied with: `-ar 22050 -b:a 32k`


## MP3 vs AAC
For relatively low bitrate spoken word audio the choice is arbitrary.

Use either `-c:a libmp3lame` or `-c:a aac`


## Noise Reduction
Use the `afftdn=nf=-25` filter can be run with a noise floor of -25 to -30 to clean up background noise.


## Isolate Voice Frequencies
Highpass filters block or reduce frequencies below a cutoff value and lowpass frequencies do the same for frequencies above a give value.  The human voice's primary frequency range is in the 80Hz to 255Hz range, but sibilants can get up towards 10kHz.

Non-voice frequencies can be supressed with: `highpass=3000, lowpass=80`

The default behaviour of these filters does not completely remove the undesired frequencies, it only attenuates them.  Multiple iterations of highpass and lowpass filter can be used to further attenuate the undesired frequency ranges.


## Trim Silences
Audio can often have long silences at the beginning or end of the file, and some might have long silences between sections of the audio.

### Leading and Trailing Silences
The first two examples from the documentation[^1] can be combined to trim leading and trailing silence:
```
silenceremove=start_periods=1: stop_periods=-1: start_threshold=-30dB: stop_threshold=-30dB: start_silence=1: stop_silence=2
```

### Detect and Shorten Silences
The `silenceremove` filter can also be used to detect periods of silence in the middle of the file:
```
silenceremove=detection=peak: stop_periods=-1: stop_threshold=-30dB: stop_duration=1.5
```

Using `stop_periods=-1` allows the filter to work in the middle of the file.  

## Volume Normalization
Normalizing the volume to bring the peak to somewhere between -1dB to -6dB is generally advised.  (The gap between this peak and 0dB is referred to as **headroom**.

There are several methods, some require one pass over the audio and others require two passes.

The simplest method is to use `volumedetect` to find the `max_volume` followed by the `volume` filter to lower or raise the peak to the required level.

The `loudnorm` filter can be run in one- or two-pass mode.  In single pass a fixed length window is used.  In two pass mode measured values from the first pass are used.

*Note:* The default settings for `LRA` and `TP` are reasonable.  The default `I` value is based on the EBU R128 standard, but most podcasts and online media use a value closer to `I=-16`[^3]. Setting `dual_mono=true` should be done if the audio has been converted to mono[^2].

The `dynloudnorm` filter could also be used.  It gives more control over the window size.

### Method 1 - `volumedetect`
After the previous steps, run: 
```
ffmpeg -i input.mp3 -af "volumedetect" -vn -sn -dn -f null /dev/null
```
*Note:* If the `max_volume` is reported as 0.0dB, then the true peak might be above zero.

If the `max_volume` is not in the proper range, then add or remove dB as needed:  
```
ffmpeg -i input.mp3 -af "volume=5dB" output.mp3
```

### Method 2 - 'loudnorm'
For a single-pass, run:
```
ffmpeg -i input.mp3 -af "loudnorm=I=-16:dual_mono=true" output.mp3
```

For a two-pass workflow, run: 
```
ffmpeg -i input.mp3 -af "loudnorm=print_format=json" -f null -
```

This prints a series of values to the terminal in the JSON format.  Use those as the measured values to run: 
```
ffmpeg -i input.mp3 -af "loudnorm=I=-16:TP=-1.5:LRA=7:measured_I=-17.0:measured_TP=-2.0measured_LRA=12.0:measured_thresh=-27.0:offset=1.0" output.mp3
```

The `I=-20:TP=-1.5:LRA=8` values are reasonable settings for spoken word audio.



[^1]: [ffmpeg Documentation - Filters - silenceremove](https://ffmpeg.org/ffmpeg-filters.html#silenceremove)

[^2]: [ffmpeg Documentation - Filters - loudnorm](https://ffmpeg.org/ffmpeg-filters.html#loudnorm)

[^3]: [Auphonic Blog - Loudness Targets for Mobile Audio, Podcasts, Radio and TV](https://auphonic.com/blog/2013/01/07/loudness-targets-mobile-audio-podcasts-radio-tv/)
