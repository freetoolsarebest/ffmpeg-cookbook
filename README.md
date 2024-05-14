# CLI Commands

### Cut/Trim Video
`-ss` specifies the starting position and `-t` specifies the duration from the start position

`-c:v copy -c:a` copy commands copy the original audio and video without re-encoding.

```bash
ffmpeg -i $input_file.mp4 -ss 00:00:00 -t 00:00:35 -c:v copy -c:a copy $output_file.mp4
```

`-to` to specify an exact time to cut to from the starting position

```bash
ffmpeg -i $input_file.mp4 -ss 00:00:00 -to 00:00:30 -c:v copy -c:a copy $output_file.mp4
```

### Compress Video
Push the compression level further by increasing the CRF value — add, say, 4 or 6, since a reasonable range for H.265 may be 24 to 30. 

```bash
ffmpeg -i $input_file.mp4 -vcodec libx265 -crf 28 $output_file.mp4
```

### GIF converter

following skip the first 10 seconds `-ss 10` of the input and create a 5 second output `-t 3`

Change as per your need
```bash
ffmpeg -i $input_file.mp4 -vf "fps=5,scale=640:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -loop 0 $output_file.gif
```

### Resize GIFs
change scale for relatively resizing gifs
```bash
ffmpeg -i input.gif -vf "scale=iw*3/4:ih*3/4" $resized_output_file.gif
```

### Watermark
adjust watermark position

**center** - overlay=(main_w-overlay_w)/2:(main_h-overlay_h)/2 <br>
**top left** - overlay=(main_w-overlay_w):0<br>
**bottom left** - overlay=0:(main_h-overlay_h)<br>
**bottom right** - overlay=(main_w-overlay_w):(main_h-overlay_h)<br>

following command overlays watermark in right bottom
```bash
ffmpeg -i $input_file.mp4 -i /home/prashant/Downloads/icon.png -filter_complex "[1][0]scale2ref=oh*mdar:ih*0.1[logo][video];[video][logo]overlay=(main_w-overlay_w-20):(main_h-overlay_h-20)" $output_scaled.mp4
```

add oppacity in watermark <br>
`format=rgba,colorchannelmixer=aa=0.3`

```bash
ffmpeg -i $input_file.mp4 -i watermark.png -filter_complex "[1]format=rgba,colorchannelmixer=aa=0.3[logo];[logo][0]scale2ref=oh*mdar:ih[logo][video];[video][logo]overlay=overlay=(main_w-overlay_w-20):(main_h-overlay_h-20)" $output_center_cover_transparent.mp4
```

### Remove Audio From Video
You can remove audio by using the `-an` flag
```bash 
ffmpeg -f lavfi -i anullsrc -i $input_file.mp4 -c:v copy -c:a aac -map 0:a -map 1:v -shortest $output_file.mp4
```

### Add Audio to Video

```bash
ffmpeg -i $input_file.mp4 -i $input_file.mp3 -c copy -map 0:v:0 -map 1:a:0 $output_file.mp4
```

# CLI Commands to change media file from one to another

### Convert webp to mp4 format
```bash
ffmpeg -fflags +genpts -i input.webm -r 24 output.mp4
```
if convertion error comes like 
`[libx264 @ 0x560eb7c01840] width not divisible by 2 (wxh)`

then Try this
```bash
ffmpeg -fflags +genpts -i input.webm -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" -r 24 1.mp4
```

### Convert avi to mp4 format

```
ffmpeg -i input.avi -c:v copy -c:a copy output.mp4
```

# CLI Commands for Streaming IP Camera


### Stream RTSP stream to RTMP server

```bash 
ffmpeg -i rtsp://admin:password@192.168.1.1:554/ -c copy -f flv rtmp://rtmp.xyz.com:port/streamname
```

### Stream RTSP stream to RTMP server(if connection breaks pipeline after 30s)

```bash 
ffmpeg -rtsp_transport tcp -stimeout 30000000 -i rtsp://admin:bidaal2020@169.254.99.67:554/ -c copy -f flv rtmp://rtmp.bidaal.com:1935/lens/error
```
