_LEEME EN CASTELLANO [AQUI](README_es.md)_
# Compile and install gdal ecw plugin on Linux Mint21 / Ubuntu 22.04

These are some personal memo notes. This repository is based on these two:
 
https://github.com/giggls/gdal-ecw-plugin

https://github.com/interob/libgdal-ecw

Only slight modifications and the necessary files have been added to make it work on Linux Mint21 / Ubuntu 22.04 with GDAL 3.4.1 installed from repositories.

It is also possible to use it on other gdal versions with some tweaks.

## Download and run the ecw development kit 


1. Download the ECW SDK version 5.5 (update 4) for linux [from the Hexagon Geospatial website](http://download.hexagongeospatial.com/downloads/ecw/erdas-ecw-jp2-sdk-v5-5-update-4-linux) (requires registration). Unzip the downloaded file and you will get a `.bin` file.
1. Make it executable with `chmod +x`
1. Run the executable. This will create a `/home/hexagon` folder containing the ECW SDK files. 


```
chmod +x ./ECWJP2SDKSetup_5.5.0.2268.bin
./ECWJP2SDKSetup_5.5.0.2268.bin --accept-eula=YES ---install-type=1
``` 

## Compile the plugin

1. Install the `libgdal-dev` package. 

2. Clone or download [this plugin repository](https://github.com/mitxel-m/gdal-ecw-plugin) and enter into `gdal-ecw-plugin` folder. 

2. Call `make` , this will give you the `gdal_ECW_JP2ECW.so` file.
3. Call `sudo make install` , this will copy the `gdal_ECW_JP2ECW.so` file into the `/usr/lib/gdalplugins` folder (this can also be done manually).

```
sudo apt install libgdal-dev
git clone https://github.com/mitxel-m/gdal-ecw-plugin
cd gdal-ecw-plugin
make
sudo make install 
```
You're done. You can check that it works with:
```
gdalinfo --formats |grep ECW
```
which should return something like:
```
ECW -raster- (rw+): ERDAS Compressed Wavelets (SDK 5.5)
JP2ECW -raster,vector- (rw+v): ERDAS JPEG2000 (SDK 5.5)
``` 
If it works you can clean up and remove the `/home/hexagon`, and `gdal-ecw-plugin` folders which are no longer needed. 


## Adapt it for a different gdal version.

Some files in this repository are copied from the gdal 3.4.1 sources. If you have another gdal version on your system, you will get an error when compiling the plugin. 

Fixing this is as simple as replacing these source files with the ones corresponding to your gdal system version, before compiling:

1. Check which gdal version is installed on your system with `gdalinfo --version` and download the sources corresponding to that version from the [official gdal repository](https://github.com/OSGeo/gdal/releases). More specifically, you will only need some files from the `/src/frmts/ecw` and `src/frmts/mem` folders.
2. In our `gdal-ecw-plugin` folder replace the following files with the new ones you have downloaded:
   - ecwasyncreader.cpp
   - ecwcreatecopy.cpp
   - ecwdataset.cpp
   - jp2userbox.cpp
   - ecwsdk_headers.h
   - gdal_ecw.h
   - memdataset.h
 
3. It is also necessary to edit the new `ecwdataset.cpp` file to make a small tweak. Near the line 39 or so: 
 
     edit   `#include "../mem/memdataset.h"`
 
     to  `#include "memdataset.h"`

Once this is done you can follow the steps indicated above to compile.
