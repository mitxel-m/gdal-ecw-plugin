# Compilar e instalar el plugin gdal_ecw en Linux Mint21 / Ubuntu 22.04 

Esto son unas notas  personales de recordatorio. Este repositorio está basado en estos otros dos:
 
https://github.com/giggls/gdal-ecw-plugin

https://github.com/interob/libgdal-ecw

Solamente se han añadido ligeras modificaciones y los ficheros necesarios para hacerlo funcionar  en Linux Mint21 (= Ubuntu 22.04) con GDAL 3.4.1 instalado desde repositorios.

También es posible usarlo con otras versiones de gdal haciendo unas pequeñas modificaciones. Leer al final.

## Descargar y ejecutar el kit de desarrollo ecw 

1. Descarga el kit ECW version 5.5 (update 4) para linux [desde la pagina de Hexagon Geospatial](http://download.hexagongeospatial.com/downloads/ecw/erdas-ecw-jp2-sdk-v5-5-update-4-linux) (requiere registro). Se descomprime el `.zip` descargado, y obtenemos un fichero `.bin`.
2. Dale permisos de ejecución con `chmod +x` y ejecutalo. Esto creará la carpeta `/home/hexagon` con los ficheros del Kit ECW. 

```
chmod +x ./ECWJP2SDKSetup_5.5.0.2268.bin
./ECWJP2SDKSetup_5.5.0.2268.bin --accept-eula=YES --install-type=1
``` 

## Compilar el plugin

1. Instalar el paquete `libgdal-dev` 

2. Clonar o descargar [este repositorio del plugin](https://github.com/mitxel-m/gdal-ecw-plugin)  y entrar en la carpeta `gdal-ecw-plugin` 

2. Ejecutar `make` , así se obtiene el fichero `gdal_ECW_JP2ECW.so`
3. Ejecutar `sudo make install` , así se copia el fichero `gdal_ECW_JP2ECW.so` a la carpeta `/usr/lib/gdalplugins` (esto también se puede hacer manualmente).

```
sudo apt install libgdal-dev
git clone https://github.com/mitxel-m/gdal-ecw-plugin
cd gdal-ecw-plugin
make
sudo make install 
```
Ya está. Puedes comprobar si funciona con:
```
gdalinfo --formats |grep ECW
```
que debería devolver algo similar a esto:
```
ECW -raster- (rw+): ERDAS Compressed Wavelets (SDK 5.5)
JP2ECW -raster,vector- (rw+v): ERDAS JPEG2000 (SDK 5.5)
``` 
Si funciona puedes hacer limpieza y quitar las carpetas `/home/hexagon`, y `gdal-ecw-plugin`  que ya no son necesarias. 


## Adaptarlo para otra versión de gdal

Algunos ficheros de este repositorio están copiados de las fuentes de gdal 3.4.1. y si tienes otra versión de gdal en tu sistema te dará error al compilar el plugin. 

Arreglarlo es sencillo, antes de compilar debes  sustituir estos ficheros de fuentes por sus correspondientes de la misma versión de gdal instalada en tu sistema:

1. Comprueba la versión de gdal instalada en tu sistema con `gdalinfo --version` y descarga las fuentes correspondientes a esa versión desde el [repositorio oficial de gdal](https://github.com/OSGeo/gdal/releases). Más concretamente,  nos interesan las carpetas `/src/frmts/ecw`  y  `src/frmts/mem`
5. En nuestra carpeta `gdal-ecw-plugin`  sustituimos los siguientes archivos por los nuevos descargados:
   - ecwasyncreader.cpp
   - ecwcreatecopy.cpp
   - ecwdataset.cpp
   - jp2userbox.cpp
   - ecwsdk_headers.h
   - gdal_ecw.h
   - memdataset.h
 
6. Además es necesario editar el archivo `ecwdataset.cpp` para hacer un pequeño ajuste. Con un editor modificamos la linea `#include "../mem/memdataset.h"` por `#include "memdataset.h"`
(en la linea 39 aproximadamente)
 
Una vez hecho esto podemos seguir los pasos indicados al inicio para compilar.
