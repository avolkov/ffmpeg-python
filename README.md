# ffmpeg-python: Powerfully simple A/V routing with Python

FFmpeg is extremely powerful, but its command-line interface gets really complicated really quickly - especially when working with signal graphs and doing anything more than trivial.

Take for example a signal graph that looks like this:
![Signal graph](https://raw.githubusercontent.com/kkroening/ffmpeg-python/master/doc/graph1.png|width=300)

The corresponding command-line arguments are pretty gnarly:
```
ffmpeg -i input.mp4 \
    -filter_complex "\
        [0]trim=start_frame=10:end_frame=20,setpts=PTS-STARTPTS[v0];\
        [0]trim=start_frame=30:end_frame=40,setpts=PTS-STARTPTS[v1];\
        [0]trim=start_frame=50:end_frame=60,setpts=PTS-STARTPTS[v2];\
        [v0][v1][v2]concat=n=3[v3]"\
     -map [v3] output.mp4
```

Maybe this looks great to you, but if you haven't worked with FFmpeg before, this probably looks pretty alien.

If you're like me and find Python to be powerful and readable, it's easy with `ffmpeg-python`:
```
import ffmpeg

in_file = ffmpeg.file_input('input.mp4')
joined = ffmpeg.concat(
    ffmpeg.trim(in_file, start_frame=10, end_frame=20),
    ffmpeg.trim(in_file, start_frame=30, end_frame=40),
    ffmpeg.trim(in_file, start_frame=50, end_frame=60)
)
out = ffmpeg.file_output(joined, 'output.mp4')
out.run()
```

`ffmpeg-python` takes care of running `ffmpeg` with the command-line arguments that correspond to the above filter diagram, and it's easy to what's going on and make changes as needed.

Real-world signal graphs can get a heck of a lot more complex, and `ffmpeg-python` doesn't care.
