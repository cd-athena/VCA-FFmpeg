# VCA  Filter Plugin for FFmpeg

[VCA](https://github.com/cd-athena/VCA) is an open source video complexity analyzer. The primary objective of VCA is to provide spatial and temporal complexity predictor for every frame/video segment/video in order to enhance predicting encoding parameters for applications like online per-title encoding. 

    
## Build 

If you don't have the FFmpeg source code already then first you need to obtain the latest revision of the FFmpeg source code from its Git repository:

```ssh
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
```


Afterward, or if you already have an FFmpeg source tree, clone this repository.

```ssh
git clone https://github.com/cd-athena/VCA-FFmpeg 
```

The plugin includes `install_vca.sh` shell script to copy vca source files and apply changes to the build files of ffmpeg:

```ssh
./VCA-FFmpeg/install_vca.sh path/to/ffmpeg
```

Note that neither `install_vca.sh` nor vca source files should be moved to another folder for `install_vca.sh` to work.

Provided all copies and changes were succesful now you can (re)configure and (re)compile FFmpeg according to your operating system and requirements, there is no known conflicts and no special provisions for compilation must be made:

```
cd ffmpeg

./configure

make -j8
```

Alternatively to using `install_vca.sh` you can manually copy vca source files  inside the `libavfilter` directory:

```ssh
cp -n VCA-FFmpeg/libavfilter/* path/to/ffmpeg/libavfilter/
```

Then you have to manually include the lines provided in `allfilters.c`, `Makefile` and `x86/Makefile` files to the corresponding files in target FFmpeg source.

## Usage 

The filter supports following command line options:

| Name                | Description                                    | Values       | Default |
| ------------------- | ---------------------------------------------- | ------------ | ------- |
| `blocksize`         | Set size of the DCT block for analysis         | 8, 16, 32    | 32      |
| `n`                 | Set number of frames to process (-1 all)       | -1, `INT_MAX`| -1      |
| `lowpass`           | Enable low-pass DCT (significantly faster)     | true, false  | true    |
| `simd`              | Enable accerelation with SIMD                  | true, false  | true    |
| `file`              | Set file where to print analysis information   | string       | `NULL`  |
| `brightness` | Enable analysis of brightness                         | true, false  | false   |
| `chroma`     | Enable analysis of UV chroma channels                 | true, false  | false   |
| `yuview`            | Produce a detailed blockwise output for YUView | true, false  | false   |

Example usage:

Use all defaults and output to vca.csv
```ssh
./ffmpeg -i input.y4m -vf vca=file=vca.csv -f null -
```

Enable brightness with blocksize 16 write to vca.csv
```ssh
./ffmpeg -i input.y4m -vf vca=blocksize=16:brightness=true:file=vca.csv -f null -
```

Enable chroma and brightness calculations with blocksize 32 write to vca.csv
```ssh
./ffmpeg -i input.y4m -vf vca=chroma=true:brightness=true:file=vca.csv -f null -
```

## Citation

If you use this project in your research, please cite:

```bibtex
@inproceedings{vca-ffmpeg,
  title={VCA-FFMPEG: A FFmpeg Filter for Video Complexity Analysis},
  author={Amirpour, Hadi and Skipenko, Mykyta and Timmerer, Christian},
  booktitle = {Proceedings of the 34rd ACM International Conference on Multimedia},
  year={2026},
  publisher = {Association for Computing Machinery},
  doi={10.1145/3767308.3834752},
  location = {Rio de Janeiro, Brazil},
  series = {MM '26}
}
```
