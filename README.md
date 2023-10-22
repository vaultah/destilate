# Destilate

Detect static parts in videos and remove them using ffmpeg.

## Requirements

 - Python
 - ffmpeg

## Description

The script runs ffmpeg's mpdecimate filter against the input video to find successive frames that mpdecimate considers similar based on default or user-provided parameters. Then, it removes the frames using ffmpeg either via copy and concatenation (default, fast) or by reencoding the original video using a complex filter (slow).

When removal of static parts is done via copy and concatenation, intervals are aligned to keyframes in a way to maximize the number of frames kept. This mode should work well enough for most cases.

Removing static parts by reencoding the video can produce more accurate results, but it's generally slower and can also result in quality loss. That can be controlled by passing in additional encoding parameters to ffmpeg.

If the script doesn't work as desired by default, try adjusting [mpdecimate parameters](https://ffmpeg.org/ffmpeg-filters.html#mpdecimate) for your needs.

Minimum durations of parts to keep or drop are also customizable. Note that they are checked before any alignment.

Refer to `./destilate -h` for parameter defaults and basic CLI documentation.

## Examples

Remove static parts from `input.mp4` using default settings without reencoding, save the output to `trimmed_input.mp4`.

```bash
$ ./destilate input.mp4
```

Same, but with reencoding (`--mode filter`) and specific encoding params. If the output file already exists, the script will replace it without asking (`-y`, or `--yes`).

```bash
$ ./destilate input.mp4 \
    --mode filter \
    --encoding-params='-c:v libx264 -preset slow' \
    -y
```

Trim input file using custom mpdecimate settings (`--params`, or `-p`), only keeping non-static parts longer than half a second, and removing static parts longer than two seconds. Output file is specified explicitly.

```bash
$ ./destilate input.mp4 \
    --params 'hi=300:lo=200:frac=1:max=0' \
    --min-keep 0.5 \
    --min-drop 2 \
    /path/to/output/file.mp4
