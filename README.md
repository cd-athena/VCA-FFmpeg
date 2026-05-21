# VCA  Filter Plug-in for FFmpeg

[VCA](https://vca.itec.aau.at/)  is an open source video complexity analyzer. The primary objective of VCA spatial and temporal complexity predictor for every frame/video segment/video in order to enhance predicting encoding parameters for applications like online per-title encoding. 

VCA is available under the GPLv3 license. More details on
the VCA method can be found in the following scientific papers:

1. Vignesh V Menon, Christian Feldmann, Klaus Schoeffmann, Mohammad Ghanbari, and Christian Timmerer. 2023. Green Video Complexity Analysis for Efficient Encoding in Adaptive Video Streaming. In Proceedings of the First International ACM Green Multimedia Systems Workshop (GMSys 2023). Association for Computing Machinery, New York, NY, USA, 259–264. [https://doi.org/10.1145/3593908.3593942](https://doi.org/10.1145/3593908.3593942)

2. Vignesh V Menon, Christian Feldmann, Hadi Amirpour, Mohammad Ghanbari, and Christian Timmerer. 2022. VCA: video complexity analyzer. In Proceedings of the 13th ACM Multimedia Systems Conference (MMSys '22). Association for Computing Machinery, New York, NY, USA, 259–264. [https://doi.org/10.1145/3524273.3532896](https://doi.org/10.1145/3524273.3532896)

This software allows to determine VCA output values for a video streams using the *FFmpeg* software suite, available at https://ffmpeg.org/
    
## Build 

Provided you you don't have the FFmpeg source code already then first,e you need to obtain the latest revision of the FFmpeg and VCA plugin source code from its Git repositories:

```ssh
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg

git clone https://github.com/mrcybercat/vca-filter.git vca
```

The plugin includes `install_vca.sh` shell script to copy vca source files anc upsert changes to the build files of ffmpeg:

```ssh
./vca/install_vca.sh path/to/ffmpeg
```

Note that nor `install_vca.sh` nor vca source files should not be moved to another folder for `install_vca.sh` to work.

Provided all copies and changes were succesfull now you can configure and compile FFmpeg according to your operating system and requirments, there is no known conflicts and no special provisions for compilation must be made:

```
cd ffmpeg

./configure

make -j8
```

Alternativly to using `install_vca.sh` you can manually copy vca source files  inside the `libavfilter` directory:

```ssh
cp -n vca/libavfilter/* path/to/ffmpeg/libavfilter/
```

Then you have to manually include the lines provided in `allfilters.c`, `Makefile` and `x86/Makefile` files to the corresonding files in target FFmpeg source.

## Usage 

VCA in principle support any video content with YUV pixel format. Filter supports following command line options:
| Name                | Description                                    | Values       | Default |
| ------------------- | ---------------------------------------------- | ------------ | ------- |
| `blocksize`         | Set size of the DCT block for analysis         | 8, 16, 32    | 32      |
| `n`                 | Set number of frames to process (-1 all)       | -1, `INT_MAX`| -1      |
| `lowpass`           | Enable low-pass DCT (significantly faster)     | true, false  | true    |
| `simd`              | Enable acceralation with SIMD                  | true, false  | true    |
| `file`              | Set file where to print analysis information   | string       | `NULL`  |
| `brightness` | Enable analysis of brightness                         | true, false  | false   |
| `chroma`     | Enable analysis of UV chroma channels                 | true, false  | false   |
| `yuview`            | Produce a detailed blockwise output for YUView | true, false  | false   |

Example usage:

Enable brightness with blocksize 16 write to vca.csv
```ssh
./ffmpeg -i input.y4m -vf vca=blocksize=16:brightness=true:file=vca.csv -f null -
```

Enable chroma and brightness calculations with blocksize 332 write to vca.csv
```ssh
./ffmpeg -i input.y4m -vf vca=chroma=true:brightness=true:file=vca.csv -f null -
```