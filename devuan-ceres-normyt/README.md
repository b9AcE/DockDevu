# normyt

Devuan ceres image to get files using youtube-dl fast then normalize audio levels to EBU R128.
Uses youtube-dl combined with aria2 to achieve parallel downloading of the audio-files (which may have video streams), then automatically executes "ffmpeg-normazlize" on the resulting files, which does a two-pass run using python and FFmpeg to normalize the audio-levels according to the EBU's R128.
For quick editing, the MP4 container format is used, as Matroska video files (mkv) requires several read to index, etc and therefore takes a lot longer to open the files using audio- and/or video-editor softwares.

Builds on b9ace/devuan-ceres:latest so therefore requires that, or use the equivalent Dockerfile available in this same GitHub-repository.

The name is of course a compound of "norm" from "normalize" and "yt" from "youtube-dl".
